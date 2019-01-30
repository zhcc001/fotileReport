<!-- 修改新建评级 -->
<template>
  <div class="modifyGrade">
    <div class="maskBox">
      <h5>{{text}}</h5>
      <div class="maskFrom">
        <p>
          <i class="line"></i>
          <span class="titleTime">当前{{onQuarter}}</span>
        </p>
        <div class="gradeItem">
          <b class="itemTitle">评级（单选）</b>
          <ol>
            <li ref='letterDom' :class="{active:index==liActive}" v-for='(item,index) in Letter' :key='index' @click='LetterClick(item,index)'>{{item}}</li>
          </ol>
        </div>
        <div class="LabelBox">
          <b class="itemTitle">标签</b>
          <div class="labelRight">
            <ul>
              <li v-for='(item,index) in jiLiArr' :key='index'>
                <input type="text" placeholder="请输入" v-model="jiLiArr[index].BucklePoint" maxlength="9" >
                <i class="addLabel" v-if='index==0' @click='addFanli(index)'>
                  <img src="./icon3.png" alt="">
                </i>
                <i class="addLabel" v-if='jiLiArr.length>1&&index!=0' @click='delindex(index)'><img src="./del.png" alt=""></i>
              </li>
            </ul>
          </div>
        </div>
        <p class="tips">不得超过9个字符！</p>
       
      </div>
    </div>
    <div class="btn" v-show="hidshow">
            <button type="button" class="sure" @click='saveGrade()' v-if='style==1'>保存</button>
            <button type="button" class="sure" @click='saveGradeMore()' v-if='style==2'>保存</button>
          </div>
  </div>
</template>
<script>
  import qs from 'qs'
  import axios from "axios";
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  export default {
    name: 'ModifyGrade',
    data() {
      return {
        style:0,
        Letter: ['A+', 'A', 'A-', 'B+', 'B', 'B-'],
        liActive: 0,
        onQuarter: '',
        text: '评级修改',
        jilib: [{
          BucklePoint: ""
        }],
        jiLiA: {
          srcAdd: require('./icon3.png'),
          srcDel: require('./del.png'),
          BucklePoint: '',
        },
        jiLiArr: [{
          srcAdd: require('./icon3.png'),
          srcDel: require('./del.png'),
          BucklePoint: '',

        }],
        CompanyID: 0,
        Year: 0,
        Quarter: 0,
        Rate: 'A+',
        TagList: [],
        num: 0,
        jidu: 0,
        numA: 0,
        rateId: -1,
        quarter: {
          'first': '第一季度 01月～03月',
          'second': '第二季度 04月～06月',
          'third': '第三季度 07月～09月',
          'fourth': '第四季度 10月～12月',
        },
        docmHeight: document.documentElement.clientHeight,  //默认屏幕高度
        showHeight: document.documentElement.clientHeight,   //实时屏幕高度
        hidshow:true , //显示或者隐藏footer
        DealerID:0,
        IsSort:false,
      }
    },
    created() {
      this.DealerID=this.$route.query.DealerID
      this.IsSort=this.$route.query.IsSort
      this.style=this.$route.query.style
      if(this.$route.query.style==1){
        console.log(this.$route.query.style)
        this.CompanyID = this.$route.query.CompanyID
      this.Year = this.$route.query.Year
      this.Quarter = this.$route.query.Quarter
      this.num = this.$route.query.num
      this.jidu = this.$route.query.jidu
      this.numA = this.$route.query.numA
      this.rateId = this.$route.query.rateId
      if (this.jidu != -1) {
        this.text = '修改评级'
        this.showItemGrade(this.num)

      } else {
        this.text = '新建评级'
      }
      if (this.numA == 1) {
        this.onQuarter = this.quarter.first
      } else if (this.numA == 2) {
        this.onQuarter = this.quarter.second
      } else if (this.numA == 3) {
        this.onQuarter = this.quarter.third
      } else if (this.numA == 4) {
        this.onQuarter = this.quarter.fourth
      }

      }else if(this.$route.query.style==2){
        console.log(123)
        if(this.$route.query.Quarter==1){
          this.onQuarter = this.quarter.first
        }else if(this.$route.query.Quarter==2){
          this.onQuarter = this.quarter.second
        }else if(this.$route.query.Quarter==3){
          this.onQuarter = this.quarter.third
        }else if(this.$route.query.Quarter==4){
          this.onQuarter = this.quarter.fourth
        }
        this.CompanyID = JSON.parse(sessionStorage.getItem('idList'))
        console.log(this.CompanyID)
        this.Year=this.$route.query.Year
        this.numA=this.$route.query.Quarter
      }
      


    },
    watch: {
        showHeight:function() {
        if(this.docmHeight > this.showHeight){
            this.hidshow=false
        }else{
            this.hidshow=true
        }
    }
    },
    mounted() {
    // window.onresize监听页面高度的变化
    window.onresize = ()=>{
        return(()=>{
            this.showHeight = document.body.clientHeight;
        })()
    }
  },
    methods: {
        
      //   查看单个评级季度
      showItemGrade(ID) {
        this.jiLiA = {}
        this.jiLiArr = []
        axios({
          url: this.getHost() + '/Company/CompanyRateByRateId',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            Id: ID,
          })
        }).then(res => {
          if(res.data.Status == 1){
            let tagArr = res.data.Data.Tag
          let tag = tagArr.split(',')
          this.Letter.forEach((b, p) => {
            if (res.data.Data.Rate == b) {
              this.liActive = p
              this.Rate = b
            }
          })
          tag.forEach((a, b) => {
            console.log(a)
            this.jiLiA = {
              'srcAdd': require('./icon3.png'),
              'srcDel': require('./del.png'),
              'BucklePoint': a,
            };

            this.jiLiArr.push(this.jiLiA);
            console.log(this.jiLiArr)


          })
          }else if (res.data.Status < 0) {
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
      //   提交评价
      saveGrade() {
        console.log(123)
        this.jiLiArr.forEach((e, f) => {
          console.log(e.BucklePoint)
            this.TagList.push(e.BucklePoint)
        })
        axios({
          url: this.getHost() + '/Company/SaveCompanyRate',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            CompanyID: this.CompanyID,
            Year: this.Year,
            Quarter: this.numA,
            Rate: this.Rate,
            TagList: this.TagList,
            id: this.rateId

          })
        }).then(res => {
          console.log(res)
          if (res.data.Status == 1) {
            this.getToast("保存成功", 'warn')
            // console.log(this.Year,'saveGrade')
            sessionStorage.setItem('Year', JSON.stringify(this.Year))
            this.$router.replace({
              path: '/grade',
              query: {
                id: this.CompanyID,
                // Year: this.Year,
              }

            })
            this.$router.go(-1)
          }else if (res.data.Status < 0) {
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
      LetterClick(t, i) {
        this.liActive = i
        this.Rate = t
      },
      //   添加标签
      addFanli(i) {
        this.jiLiA = {
          srcAdd: require('./icon3.png'),
          srcDel: require('./del.png'),
          BucklePoint: '',
        }
        if (this.jiLiArr[this.jiLiArr.length - 1].BucklePoint != '' && this.jiLiArr.length >= 1) {
          this.jiLiArr.push(this.jiLiA);
        }

      },
      //   删除标签
      delindex(index) {
        this.jilib.splice(index, 1)
        this.jiLiArr.splice(index, 1);
        this.jiLiArr.slice();
      },


      //   提交批量评价
      saveGradeMore() {
        this.jiLiArr.forEach((e, f) => {
          console.log(e.BucklePoint)
            this.TagList.push(e.BucklePoint)
        })
        axios({
          url: this.getHost() + '/Company/SaveCompanyRateById',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            CompanyID: this.CompanyID,
            Year: this.Year,
            Quarter: this.numA,
            Rate: this.Rate,
            TagList: this.TagList,
          })
        }).then(res => {
          console.log(res)
          if (res.data.Status == 1) {
            console.log(this.numA,this.IsSort)
            this.getToast("评级成功", 'warn')

            this.$router.replace({
              path: '/notRating',
              // query: {
              //   page: 1,
              //   DealerID:this.DealerID,
              //   Quarter:this.numA,
              //   IsSort:this.IsSort,
              // }

            })
            this.$router.go(-1)

          }else if (res.data.Status < 0) {
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
    },

    beforeCreate() {
      document.querySelector('body').setAttribute('style', 'background-color:#fff')
    },
    beforeDestroy() {
      document.querySelector('body').removeAttribute('style')
    },
  }

</script>

<style scoped>
  .maskBox {
    width: 92%;
    margin: 0 auto;
    padding-bottom: 18px;

  }

  h5 {
    font-size: 18px;
    color: #404040;
    text-align: center;
    padding: 20px 0;
  }

  .maskFrom {
    height: 78vh;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;

  }

  .maskFrom p {
    display: flex;
  }

  .line {
    display: block;
    width: 4px;
    height: 14px;
    background: #e2c78f;
    margin-right: 7px;
  }

  .titleTime {
    font-size: 14px;
    color: #5e5e5e;
  }

  .gradeItem {
    padding-left: 10px;
    padding-top: 16px;
  }

  .gradeItem b {
    display: block;
    font-weight: normal;
    color: #5e5e5e;
    font-size: 14px;
    padding-bottom: 10px;
  }

  .gradeItem ol {
    width: 80%;
    display: flex;
    /* justify-content: space-between; */
    flex-wrap: wrap;
  }

  .gradeItem ol li {
    padding: 0 28px;
    color: #808080;
    border: 1px solid #CCCCCC;
    line-height: 26px;
    border-radius: 13px;
    margin-bottom: 8px;
    margin-right: 10px;
  }

  .gradeItem ol li.active {
    background: #E7C58B;
    border: 1px solid transparent;
    color: white;
  }

  .LabelBox {
    padding-left: 10px;
    padding-top: 16px;
    display: flex;
  }

  .labelRight {
    display: flex;
    width: 70%;
  }

  .LabelBox>b {
    display: block;
    line-height: 36px;
    margin-right: 10px;
  }

  .labelRight ul {
    width: 100%;
  }

  .labelRight ul li {
    padding: 0;
    margin: 0;
    position: relative;
    margin-bottom: 6px;
  }

  .labelRight .demoInput {
    border: none;
    background: none;
    border: 1px solid #D6B77C;
    height: 36px;
    padding-left: 12px;
    width: 76%;
  }

  .labelRight ul li input {
    border: none;
    background: none;
    border: 1px solid #D6B77C;
    height: 36px;
    padding-left: 12px;
    width: 76%;
  }

  .addLabel {
    display: block;
    padding: 6px;
    background: #E2C78F;
    border-radius: 50%;
    position: absolute;
    right: 0;
    top: 8px;
  }

  .addLabel img {
    display: block;
    width: 12px;
    height: 12px;
  }

  .tips {
    width: 40%;
    font-size: 11px;
    color: #D9D9D9;
    padding-bottom: 14px;
    text-align: center;
    margin: 0 auto;
    padding-top: 10px;
  }

  .btn {
    width: 70%;
    display: flex;
    justify-content: space-between;
    position: fixed;
    bottom: 0;
    left: 0;
    padding: 40px 0;
    right: 0;
    margin: auto;
    z-index: 100;
  }

  .btn button {
    display: block;
    border: none;
    background: none;
    padding: 0 88px;
    line-height: 34px;
    font-size: 13px;
    border-radius: 2px;
    margin: 0 auto;
  }

  .btn .cancel {
    background: #EDECEB;
    color: #989898;
  }

  .btn .sure {
    background: #D6B77C;
    color: #FFFFFF;
  }

  .nullBox {
    display: flex;
    position: relative;
    margin-bottom: 6px;
  }

</style>
