<template>
  <!-- 申诉审批 -->
  <div class="appeal">
    <div class="btn">
      <button type="button" :class="{active:style==''}" @click="active('')">全部申诉</button>
      <button type="button" :class="{active:style==3}" @click="active(3)">特殊申诉</button>
      <button type="button" :class="{active:style==2}" @click="active(2)">保护期申诉</button>
      <button type="button" :class="{active:style==1}" @click="active(1)">跟进权申诉</button>
    </div>
    <div class="flex selectCon" v-if='btn'>
      <div class="flex justifyCenter select" @click="handleTimeSort">
        <span>申请时间{{isTimeSort==0?'顺序':'逆序'}}</span>
        <i>
          <img src="./timeIcon.png" alt="">
        </i>
      </div>
      <div class="flex justifyCenter select" @click="handleApprovalStatus">
        <span>{{selectStatus}}</span>
        <i>
          <img src="./1.png" alt="" v-if="!isShowList">
          <img src="./2.png" alt="" v-if="isShowList">
        </i>
      </div>
      <div class="maskList" v-if="isShowList">
        <ul class="flex">
          <li :class="{yellow:selectStatus==item.Name}" v-for="item in selectList" :key='item.Status' @click="handleSelectStatus(item)">{{item.Name}}</li>
        </ul>
      </div>
    </div>
    <div class="bgcMask" v-if="isShowList"></div>


    <div class="appealList">
      <div class="scroll-list-wrap">
          <cube-scroll ref="scroll" :data="list" :options="options" @pulling-up="onPullingUp" @pulling-down='onPullingDown'>
      <ul>
        <li v-for="(item,index) in list" :key="index" @click="applyDetail(item.ID)">
          <span :class="{red:item.TypeID==1,yellow:item.TypeID==2,grey:item.TypeID==3}">{{item.TypeName}}</span>
          <span v-if="item.Status==2" style="color:#6997C0">{{item.StatusName}}</span>
          <span v-if="item.Status==-1" style="color:#F26F53">{{item.StatusName}}</span>
          <div class="up">
            <p class="CompanyName">{{item.CompanyName}} </p>
            <!-- <span class="UserName">业务员{{item.UserName}}</span> -->
            <div class="upBtn" v-if="item.Status == 1||item.Status == 0">
              <button type="button" class="no" @click.stop="noAllow(true,item.ID)" v-if="btn">不通过</button>
              <button type="button" class="yes" @click.stop="isAllow(true,item.ID)" v-if="btn">审批通过</button>
            </div>
          </div>
          <div class="down">
            <i>申诉日期：{{item.CreateDate}}</i>
            <a href="javascript:;">
              <span>查看申诉详情>></span>
            </a>
          </div>
        </li>
      </ul>
      <empty v-if='emptyFlag'></empty>
      <template slot="pulldown" slot-scope="props">
          <div v-if="props.pullDownRefresh" class="cube-pulldown-wrapper" :style="props.pullDownStyle">
            <div v-if="props.beforePullDown" class="before-trigger" :style="{paddingTop: props.bubbleY + 'px'}">
              <span :class="{rotate: props.bubbleY > 0}">↓</span>
            </div>
            <div class="after-trigger" v-else>
              <div v-show="props.isPullingDown" class="loading">
                <cube-loading></cube-loading>
              </div>
            </div>
          </div>
        </template>
    </cube-scroll>
  </div>
    </div>

    <div id="mask" v-if="isShowMask">
      <div class="maskContain">
        <p class="title">审批不通过原因</p>
        <div class="textarea">
          <textarea name="" id="" cols="30" rows="6" v-model="giveUpReason"></textarea>
        </div>
        <div class="btn">
          <span class="cancel" @click="noAllow(false)">取消</span>
          <span class="confirm" @click.stop="isAllow(false)">确认</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import qs from 'qs'
  import axios from "axios";
  import {
    mapGetters,
    mapMutations
  } from 'vuex';
  import empty from '../../components/empty'
  export default {
    name: 'appeal',
    data() {
      return {
        list: [],
        emptyFlag: false,
        style: '',
        btn: true,
        isShowMask: false,
        giveUpReason: '',
        Id: '',
        isShowList:false,
        isTimeSort:1,
        TypeID:'',
        selectStatus:'未审批',
        Status:0,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        secondStop: 26,
        selectList:[
          {
            Status:1,
            Name:'已审批'
          },
          {
            Status:0,
            Name:'未审批'
          }
        ],
      }
    },
    components: {
      empty
    },
    beforeCreate () {
      document.querySelector('body').setAttribute('style', 'background-color:#fff')
    },
    beforeDestroy () {
      document.querySelector('body').removeAttribute('style')
    },
    computed: {
      options() {
        return {
          pullUpLoad: this.pullUpLoadObj,
          scrollbar: true,
          pullDownRefresh: {
            threshold: 60,
            stop: 44,
            stopTime: 100,
            txt: '更新成功'
          },
        }
      },
      pullUpLoadObj: function () {
        return {
          threshold: parseInt(this.pullUpLoadThreshold),
          txt: {
            more: this.pullUpLoadMoreTxt,
            noMore: this.pullUpLoadNoMoreTxt
          }
        }
      },
      ...mapGetters([
        'AccessId'
      ])
    },
    created() {
      this.getList("")
      if (this.AccessId == 4) {
        this.btn = false
      }
    },
    methods: {
      onPullingUp() {
        // 更新数据
        if (this.pageCount >= this.page) {
          this.getList(this.page)
        } else {
          this.$refs.scroll.forceUpdate()
        }
      },
      onPullingDown() {
        setTimeout(() => {
          // this.page=1
          this.getList(this.TypeID,this.isTimeSort,this.Status)
          this.$refs.scroll.scrollTo(0, this.secondStop, 100)
        }, 1000)
      },
      getList() {
        axios({
            url: this.getHost() + '/Approval/GetApplyList',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
              TypeID: this.TypeID,
              IsSort: this.isTimeSort,
              Status: this.Status
            })
          })
          .then(res => {
            console.log(res)
            if (res.data.Status === 1) {
              this.list = res.data.Data.list
              if (this.list == '') {
                this.emptyFlag = true
              } else {
                this.emptyFlag = false
              }
            } else if (res.data.Status < 0) {
              this.getToast("登录失效，请重新登录", 'warn')
              setTimeout(() => {
                this.delCookie("UserId")
                this.delCookie("token")
                this.setAccessId('')
                location.replace('/')
              }, 2000);
            } else {
              this.getToast(res.data.Message, 'warn')
            }
          })
      },
      active(style) {
        this.style = style
        this.TypeID=this.style
        this.getList()
      },
      handleSelectStatus(item) {
        console.log(item.Status)
        this.isShowList = false
        this.selectStatus = item.Name
        this.Status = item.Status
        this.TypeID=this.style
        this.getList()

      },
      handleTimeSort(){
        this.isTimeSort = this.isTimeSort == 0 ? 1 :0
        this.getList(this.style,this.Status)
      },
      handleApprovalStatus() {
        this.isShowList = !this.isShowList
      },
      isAllow(bool, id) {
        if (bool) {
          axios({
              url: this.getHost() + '/Approval/AgreeSate',
              method: 'post',
              data: qs.stringify({
                UserId: getCookie('UserId'),
                token: getCookie('token'),
                Id: id,
              })
            })
            .then(res => {
              console.log(res)
              if (res.data.Status === 1) {
                this.getToast("操作成功", 'warn')
                this.getList(this.style)
              } else if (res.data.Status < 0) {
                this.getToast("登录失效，请重新登录", 'warn')
                setTimeout(() => {
                  this.delCookie("UserId")
                  this.delCookie("token")
                  this.setAccessId('')
                  location.replace('/')
                }, 2000);
              } else {
                this.getToast(res.data.Message, 'warn')
              }
            })
        } else {
          if (!this.giveUpReason) {
            this.getToast("请输入不通过的原因", 'warn')
            return
          }
          axios({
              url: this.getHost() + '/Approval/CancelSate',
              method: 'post',
              data: qs.stringify({
                UserId: getCookie('UserId'),
                token: getCookie('token'),
                Id: this.Id,
                Reason: this.giveUpReason
              })
            })
            .then(res => {
              console.log(res)
              if (res.data.Status === 1) {
                this.getToast("操作成功", 'warn')
                this.getList(this.style)
                this.noAllow(false)
              } else if (res.data.Status < 0) {
                this.getToast("登录失效，请重新登录", 'warn')
                setTimeout(() => {
                  this.delCookie("UserId")
                  this.delCookie("token")
                  this.setAccessId('')
                  location.replace('/')
                }, 2000);
              } else {
                this.getToast(res.data.Message, 'warn')
              }
            })
        }
      },
      applyDetail(id) {
        console.log(id);

        this.$router.push({
          path: '/appealDetails',
          query: {
            id: id
          }
        })
      },
     
      noAllow(bool, id) {

        this.isShowMask = bool
        console.log(bool);

        if (bool) {
          this.Id = id
        } else {
          this.giveUpReason = ''
        }
      },

    }

  }

</script>

<style scoped>
  @import '../../common/mask.css';
  .appealList .scroll-list-wrap {
    height: calc(100vh - 116px);
  }
  .flex{
    display: flex;
    align-items: center;
  }
  .justifyCenter{
    justify-content: center;
  }
  .select{
    flex: 1;
  }
  .select img{
    width: 9px;
    margin-left: 5px;
  }
  .selectCon{
    background-color: #fff;
    border-top:1px solid #F5F5F5;
    border-bottom: 1px solid #F5F5F5; 
    position: relative;
    height: 45px;
  }
  .maskList{
    position: absolute;
    top: 46px;
    left: 0;
    width: 100%;
    z-index: 5;
  }
  .maskList .yellow{
    color: #E2C78F;
  }
  .maskList ul{
    background-color: #fff;
    flex-direction: column;
    width: 100%;
  }
  .maskList ul li{
    width: 100%;
    height: 40px;
    border-bottom: 1px solid #F5F5F5;
    padding-left: 15px;
    box-sizing: border-box;
    display: flex;
    align-items: center;
  }
  .maskList ul li:last-child{
    border-bottom: none;
  }
  .bgcMask{
    width: 100%;
    min-height:calc(100vh - 150px);
    position: absolute;
    bottom: 0;
    left: 0;
    z-index: 2;
    background-color: rgba(0,0,0,.4);
  }
  .appeal {
    width: 100%;
    box-sizing: border-box;
    overflow: hidden;
  }

  .btn {
    width: 94%;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    padding: 16px 0;
  }

  .btn button {
    height: 29px;
    border-radius: 4px;
    font-size: 14px;
    font-family: PingFangSC-Regular;
    color: rgba(179, 179, 179, 1);
    border: 1px solid #959595;
    background: none;
    padding: 0 10px;
    display: flex;
    justify-content: center;
    align-items: center
  }

  .btn button.active {
    background: #E2C78F;
    color: rgba(255, 255, 255, 1);
    border: none
  }


  .appealList ul{
    width: 92%;
    margin: 0 auto;
    margin-top: 20px;
  }
  .appealList ul li {
    overflow: hidden;
    background: white;
    padding: 12px;
    margin-bottom: 9px;
    padding-bottom: 20px;
    border-radius: 4px;
    box-shadow: 0 0px 15px rgba(125, 125, 125, 0.19);
  }

  .appealList ul li .up {
    width: 96%;
    margin: 0 auto;
    border-bottom: 1px solid #F5F5F5;
    padding: 15px 0;
    margin-bottom: 14px;
    /* margin-left: 15px; */
    position: relative;
    display: flex;
    justify-content: space-between;
  }

  .appealList ul li>span {
    display: inline-block;
    height: 20px;
    border-radius: 3px;
    line-height: 20px;
    text-align: center;
    /* border: 1px solid #BFBFBF; */
    padding: 0 10px;
    font-size: 12px;
    /* color: rgba(191, 191, 191, 1); */
  }

  .appealList ul li .up .CompanyName {
    width: 58%;
    word-break: break-all;
    font-size: 16px;
    font-family: PingFangSC-Regular;
    color: rgba(77, 77, 77, 1);
    line-height: 30px;
    border: none;
    margin-right: 4px;
  }

  .appealList ul li .up .UserName {
    font-size: 16px;
    font-family: PingFangSC-Regular;
    color: rgba(77, 77, 77, 1);
    line-height: 28px;
    margin-left: 4px;
  }

  .appealList ul li .up .upBtn {
    display: flex;
    width: 44%;
    height: 30px;
    justify-content: space-between;
    position: relative;
  }

  .appealList ul li .up .upBtn button {
    display: block;
    border-radius: 3px;
    padding: 0 10px;
    line-height: 28px;
    height: 28px;
    text-align: center;
    font-size: 12px;
    position: absolute;
    top:0;
    bottom: 0;
    margin: auto;
  }

  .appealList ul li .up .upBtn .no {
    background: rgba(228, 228, 228, 1);
    color: rgba(178, 178, 178, 1);
    left: 0;
  }

  .appealList ul li .up .upBtn .yes {
    background: rgba(226, 199, 143, 1);
    color: rgba(255, 255, 255, 1);
    right: 0;
  }

  .appealList ul li .down {
    width: 94%;
    display: flex;
    justify-content: center;
    margin: 0 auto;
  }

  .appealList ul li .down i {
    font-style: normal;
    font-size: 12px;
    color: rgba(242, 111, 83, 1);
    flex-grow: 1;
    width: 0;
  }
  .red{
  border:1px solid #F26F53;
  color: #F26F53 !important;
}
.yellow{
  border:1px solid #BB9F61;
  color: #BB9F61 !important;
}
.grey{
  border:1px solid #BFBFBF;
  color:  #BFBFBF !important;
}
  .appealList ul li .down a {
    font-size: 12px;
    color: rgba(128, 128, 128, 1);
    /* margin-left: 110px; */
  }
.flex .yellow{
  border:none;
}
</style>
