<template>
<!-- 消息中心 -->
  <div id="messageCenter">
       <div class="scroll-list-wrap">
      <cube-scroll
        ref="scroll"
        :data="list"
        :options="options"
        @pulling-up="onPullingUp" @pulling-down='onPullingDown'>
      <div class="contentList" v-for="(item,index) in list" :key="index">
        <div class="contentListTop">
          <p class="firstLine">
            <span class="status">{{item.TagName}}</span>
            <span>{{item.CreateDate}}</span>
          </p>
          <p class="msg">
            {{item.Content}}
            <!-- <span>撤销申诉</span> -->
          </p>
        </div>
        <!-- <div class="contentListBottom">
          <span>待审批／审批中不可撤销</span>
        </div> -->
        <div class="contentListBottom" >
          <p class="person" v-if='item.SourceID!=0'>
            审批人：{{item.UserName}}
          </p>
          <p class="person" v-else>
              发布人：{{item.UserName}}
            </p>
          <p class="enterDetail" v-if="item.TagID!=2&&item.SourceID!=0"><a href="javascript:;" to="/companyDetail" @click="companyDetail(item.SourceID)">点击进入公司详情页>></a></p>
        </div>
      </div>
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
      <empty v-if='emptyFlag'></empty>
      <!-- <div class="lookMore">
        <a href="javascript:;">查看全部</a>
      </div> -->
    </div>

</template>

<script>
import qs from 'qs'
import axios from "axios";
import appealHisList from "../../components/appealHisList";
import empty from '../../components/empty'
export default {
  name: 'messageCenter',
  data(){
    return{
        list:[],
        emptyFlag:false,
        pageCount:1,
        page:1,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        secondStop: 26
      }
  },
  components:{
    appealHisList,
    empty
  },
  created(){
    this.getList()
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
  pullUpLoadObj: function() {
    return {
      threshold: parseInt(this.pullUpLoadThreshold),
      txt: {
        more: this.pullUpLoadMoreTxt,
        noMore: this.pullUpLoadNoMoreTxt
      }
    }
  }
},
  methods:{
     onPullingUp() {
          // 更新数据
           if (this.pageCount>=this.page) {
             this.getList(this.page)
           }else{
             this.$refs.scroll.forceUpdate()
           }
        },
        onPullingDown() {
        setTimeout(() => {
          this.page=1
          this.getList(this.page)
          this.$refs.scroll.scrollTo(0, this.secondStop, 100)
        }, 1000)
      },
    getList(page){
      axios({
        url:this.getHost()+'/UserInfo/GetMessageList', 
        method:'post',
        data:qs.stringify({
          UserId:getCookie('UserId'),
          token:getCookie('token'),
          page:page||1
        })
      })
      .then(res=>{
        console.log(res)
        if (res.data.Status===1) {
           this.pageCount = res.data.Data.pageCount
                if (this.page==1) {
                  this.list = res.data.Data.list
                }else{
               if (this.list.length>0&&this.page>1) {
                  // 如果有新数据
                  // let newPage = _foods.slice(0, 5)
                  this.list = this.list.concat(res.data.Data.list)
                  
                }
                }
              this.page++
              if (this.list.length==0) {
                this.emptyFlag = true
              }else{
                this.emptyFlag = false
              }
        }else if (res.data.Status<0) {
          this.getToast("登录失效，请重新登录",'warn')
          setTimeout(() => {
            this.delCookie("UserId")
            this.delCookie("token")
            this.setAccessId('')
            location.replace('/')
          }, 2000);
        }
        else{
          this.getToast(res.data.Message,'warn')
        }
      })
    },
    companyDetail(id){
      this.$router.push({
        path:"/companyDetail",
        query:{
          id:id
        }
      })
    }
  }
}
</script>

<style scoped>
@import '../../common/focusList.css';
.scroll-list-wrap {
  height: calc(100vh - 50px);
}
#messageCenter{
  padding: 15px;
  box-sizing: border-box;
}
.contentListTop,.contentListBottom{
  margin-left: 15px;
}
.firstLine .status{
  position: absolute;
  left: 0px;
  border-radius: 0px 15px 15px 0 !important;
  background:rgba(246, 234, 212, 1);
  color: #BB9F61 !important;
}
.twoLine{
  padding-bottom: 0;
}
.contentListTop .twoLine span{
  font-size: 14px;
  font-weight: normal;
  line-height: 18px;
  display: block;
  text-align: left;
}
.contentListBottom .person{
  color: #F26F53;
  width: 0;
  flex-grow: 1;
}
.contentListBottom a{
  color: #4d4d4d;
}
.enterDetail{
  text-align: right;
}
.firstLine span:first-child{
  top: 10px;
  padding: 0 6px;
  line-height: 24px;
}
.msg{
  padding-top: 16px;
  padding-bottom: 10px;
  line-height: 18px;
}
</style>
