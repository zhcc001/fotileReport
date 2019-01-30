ok<!-- 分总公司评级 -->
<template>
  <div class="grade">
    <div class="topTime" @click="showDatePicker">
      <div class="topText">
        <img class="dataIcon" src="./dataIcon.png" alt="">
        <span>{{year}}</span>
        <img class="sanJiao" src="./sanjiao.png" alt="">
      </div>
    </div>
    <div class="content">
      <div class="box">
        <ul>
          <li>
            <img class="icon1" src="./leftIcon.png" alt="">
            <div class="liRight">
              <div class="up"><span>{{aa}}年 {{quarter.fourth}}</span> </div>
              <div class="down">
                <div class="midDown" v-if='quarter4==4'>
                  <b><span>{{rate4}}</span>级</b>
                  <ol v-if='tage4.length>0'>
                    <li v-for='(t,i) in tage4' :key='i'>{{t}}</li>
                  </ol>
                  <span v-else>暂无标签</span>
                </div>
                <b v-else>暂未评级！</b>
                <i class="edit" @click='maskClick(id4,quarter4,4)' v-if='quarter4==4&&AccessId==1'>
                  <img src="./icon2.png" alt="">
                </i>
                <i class="edit" @click='maskClick(id4,quarter4,4)' v-if='quarter4==-1&&AccessId==1'>
                  <img src="./icon3.png" alt="">
                </i>
              </div>
            </div>
          </li>
          <li>
            <img class="icon1" src="./leftIcon.png" alt="">
            <div class="liRight">
              <div class="up editActive"><span>{{aa}}年 {{quarter.third}}</span> </div>
              <div class="down">
                <div class="midDown" v-if='quarter3==3'>
                  <b><span>{{rate3}}</span>级</b>
                  <ol v-if='tage3.length>0'>
                    <li v-for='(t,i) in tage3' :key='i'>{{t}}</li>
                  </ol>
                  <span v-else>暂无标签</span>
                </div>
                <b v-else>暂未评级！</b>
                <i class="edit" @click='maskClick(id3,quarter3,3)' v-if='quarter3==3&&AccessId==1'>
                  <img src="./icon2.png" alt="">
                </i>
                <i class="edit" @click='maskClick(id3,quarter3,3)' v-if='quarter3==-1&&AccessId==1'>
                  <img src="./icon3.png" alt="">
                </i>
              </div>
            </div>
          </li>
          <li>
            <img class="icon1" src="./leftIcon.png" alt="">
            <div class="liRight">
              <div class="up editActive"><span>{{aa}}年 {{quarter.second}}</span> </div>
              <div class="down">
                <div class="midDown" v-if='quarter2==2'>
                  <b><span>{{rate2}}</span>级</b>
                  <ol v-if='tage2.length>0'>
                    <li v-for='(t,i) in tage2' :key='i'>{{t}}</li>
                  </ol>
                  <span v-else>暂无标签</span>
                </div>
                <b v-else>暂未评级！</b>
                <i class="edit" @click='maskClick(id2,quarter2,2)' v-if='quarter2==2&&AccessId==1'>
                  <img src="./icon2.png" alt="">
                </i>
                <i class="edit" @click='maskClick(id2,quarter2,2)' v-if='quarter2==-1&&AccessId==1'>
                  <img src="./icon3.png" alt="">
                </i>
              </div>
            </div>
          </li>
          <li>
            <img class="icon1" src="./leftIcon.png" alt="">
            <div class="liRight">
              <div class="up editActive"><span>{{aa}}年 {{quarter.first}}</span> </div>
              <div class="down">
                <div class="midDown" v-if='quarter1==1'>
                  <b><span>{{rate1}}</span>级</b>
                  <ol v-if='tage1.length>0'>
                    <li v-for='(t,i) in tage1' :key='i'>{{t}}</li>
                  </ol>
                  <span v-else>暂无标签</span>
                </div>
                <b v-else>暂未评级！</b>
                <i class="edit" @click='maskClick(id1,quarter1,1)' v-if='quarter1==1&&AccessId==1'>
                  <img src="./icon2.png" alt="">
                </i>
                <i class="edit" @click='maskClick(id1,quarter1,1)' v-if='quarter1==-1&&AccessId==1'>
                  <img src="./icon3.png" alt="">
                </i>

              </div>
            </div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import eventVue from '../../js/eventVue.js'
  import qs from 'qs'
  import axios from "axios";
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  const date = new Date()
  export default {
    name: 'Grade',

    data() {
      return {
        text:'评级修改',
        // addText:'新建评价',
        inputVisible:false,
        newVal:'',
        time: '',
        year: date.getFullYear(),
        yearText: date.getFullYear(),
        aa: '',
        Letter: ['A+', 'A', 'A-', 'B+', 'B', 'B-'],
        liActive: 0,
        maskFlag: false,
        rate1: '',
        rate2: '',
        rate3: '',
        rate4: '',
        tage1: [],
        tage2: [],
        tage3: [],
        tage4: [],
        quarter1: -1,
        quarter2: -1,
        quarter3: -1,
        quarter4: -1,
        id1: 0,
        id2: 0,
        id3: 0,
        id4: 0,
        bb: -1,
        cc: '',
        rateId:0,
        quarter: {
          'first': '第一季度 01月～03月',
          'second': '第二季度 04月～06月',
          'third': '第三季度 07月～09月',
          'fourth': '第四季度 10月～12月',
        },
        
        onQuarter: '',
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

      }
    },
    computed:{
      ...mapGetters([
        'AccessId'
      ]),
    },
    created() {
      this.ID = this.$route.query.id
      let thisYear=JSON.parse(sessionStorage.getItem('Year'))
      if(thisYear){
        this.year=thisYear
        this.aa=this.year.toString().substring(2)
      }else{
        if (date.getMonth() + 1 == 1 || date.getMonth() + 1 == 2 || date.getMonth() + 1 == 3) {
        this.year = date.getFullYear() - 1;
        this.yearText = date.getFullYear() - 1
        this.yearText = this.yearText.toString()
        this.aa = this.yearText.substring(2)
      } else {
        this.year = date.getFullYear();
        this.yearText = date.getFullYear()
        this.yearText = this.yearText.toString()
        this.aa = this.yearText.substring(2)
      }
      }
      
      this.getDataMsg()
    },
    methods: {
      getDataMsg() {
        axios({
          url: this.getHost() + '/Company/CompanyRateById',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            CompanyID: this.ID,
            Year: this.year
          })


        }).then(res => {
          if(res.data.Status == 1){
            let data = res.data.Data.list
          if(data.length>0){
            data.forEach((item, index) => {
            this.Rate=item.ID
            if (item.Quarter) {
              if (item.Quarter == 1) {
                this.rate1 = item.Rate
                console.log(this.rate1)
                this.quarter1 = item.Quarter
                this.id1 = item.ID
                if (item.Tage != '') {
                  this.tage1 = item.Tage.split(',')
                }
              } else if (item.Quarter == 2) {
                this.rate2 = item.Rate
                this.quarter2 = item.Quarter
                this.id2 = item.ID
                if (item.Tage != '') {
                  this.tage2 = item.Tage.split(',')
                }
              } else if (item.Quarter == 3) {
                this.rate3 = item.Rate
                this.quarter3 = item.Quarter
                this.id3 = item.ID
                if (item.Tage != '') {
                  this.tage3 = item.Tage.split(',')
                }
              } else if (item.Quarter == 4) {
                this.rate4 = item.Rate
                this.quarter4 = item.Quarter
                this.id4 = item.ID
                if (item.Tage != '') {
                  this.tage4 = item.Tage.split(',')
                }

              }
            }

          })
          }
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
      showDatePicker() {
        if (!this.datePicker) {
          this.$createDatePicker({
            title: '选择年份',
            min: new Date(2018, 1, 1),
            max: new Date(2020, 12, 31),
            value: new Date(),
            columnCount: 1,
            onSelect: this.selectHandle,
            //   onCancel: this.cancelHandle
          }).show()

        }

      },
      selectHandle(date, selectedVal, selectedText) {
        this.year = selectedText.join(' ')
        this.yearText = selectedText.join(' ')
        this.yearText = this.yearText.toString()
        this.aa = this.yearText.substring(2)
        this.rate1= '',
        this.rate2= '',
        this.rate3= '',
        this.rate4= '',
        this.tage1= [],
        this.tage2= [],
        this.tage3= [],
        this.tage4= [],
        this.quarter1= -1,
        this.quarter2= -1,
        this.quarter3= -1,
        this.quarter4= -1,
        this.id1= 0,
        this.id2= 0,
        this.id3= 0,
        this.id4= 0,
        this.getDataMsg()
        

      },
      //   编辑评级弹层
      maskClick(num, jidu,numA) {
        this.$router.push({
          path: '/modifyGrade',
          query:{
            num:num,
            jidu:jidu,
            numA:numA,
            CompanyID:this.ID,
            Year: this.year,
            Rate:this.Rate,
            rateId:this.RateId,
            style:1
          }

        })
        if(jidu!=-1){
          this.text='修改评级'
        }else{
          this.text='新建评级'
        }
      },
      ...mapMutations({
        setAccessId: 'SET_ACCESSID'
      }),
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
  .grade {
    width: 100%;
  }

  .topTime {
    position: fixed;
    z-index: 2;
    top: 0;
    left: 0;
    width: 100%;
    height: 54px;
    background: white;
    box-shadow: 0px 4px 4px 0px rgba(109, 109, 109, 0.04);

  }

  .topText {
    display: flex;
    width: 22%;
    margin: 0 auto;
    justify-content: space-between;
    padding: 18px 0;
  }

  .topText span {
    font-size: 18px;
    color: #747474;
  }

  .dataIcon {
    display: block;
    width: 12px;
    height: 12px;
    margin-top: 2px;
  }

  .sanJiao {
    display: block;
    width: 11px;
    height: 7px;
    margin-top: 4px;
  }

  .content {
    width: 96%;
    margin: 0 auto;
    /* padding: 0 8px; */
    padding-bottom: 90px;
    padding-top: 54px;
  }

  .box {
    overflow: hidden;
  }

  .box ul {
    width: 94%;
    margin: 0 auto;
  }

  .box ul li {
    display: flex;
    position: relative;
    background: rgba(255, 255, 255, 1);
    border-left: 1px solid #F5F5F5;
    padding-top: 16px;

  }

  .liRight {
    width: 100%;
    border: 1px solid rgba(235, 232, 232, 1);
    box-shadow: 0px 3px 7px 1px rgba(226, 204, 163, 0.14);
    border-radius: 6px;
    margin-left: 12px;
    padding-bottom: 20px;

  }

  .icon1 {
    display: block;
    width: 14px;
    height: 14px;
    position: absolute;
    top: 34px;
    left: -8px;
  }

  .up {
    width: 100%;
    background: #FFFAF0;
    height: 48px;
  }

  .up span {
    display: block;
    font-size: 18px;
    color: #333333;
    line-height: 48px;
    margin-left: 16px;
  }

  .down {
    margin-left: 16px;
    display: flex;
    justify-content: space-between;
    padding-top: 10px;
  }

  .down>b {
    color: #F26F53;
    font-size: 14px;
    padding-top: 4px;
  }

  .midDown {
    width: 76%;
    display: flex;
    padding-top: 6px;
  }

  .midDown>span {
    font-size: 12px;
    line-height: 20px;
  }

  .midDown b {
    font-size: 12px;
    color: #4D4D4D;
    margin-right: 10px;
    padding-top: 2px;
  }

  .midDown b span {
    font-size: 16px;
  }

.midDown ol{
  width: 86%;
}
  .midDown ol li {
    float: left;
    border: 1px solid rgba(226, 199, 143, 1);
    border-radius: 1px;
    font-size: 10px;
    color: #E2C78F;
    line-height: 16px;
    margin-right: 4px;
    padding: 0 4px;
    margin-bottom: 4px;
  }

  .edit {
    display: block;
    padding: 7px 14px;
    background: #E2C78F;
    border-radius: 14px;
    margin-right: 14px;
    position: absolute;
    right: 0;
  }

  .edit img {
    display: block;
    width: 16px;
    height: 16px;
  }

  .editActive {
    background: #FBFBFB;
  }

  /* 弹层 */
  .gradeMask {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    z-index: 10;
  }

  .maskBox {
    width: 88%;
    margin: 0 auto;
    margin-top: 72px;
    background: white;
    border-radius: 12px;
    padding-bottom: 18px;
    max-height: 76%;

  }

  h5 {
    font-size: 18px;
    color: #404040;
    text-align: center;
    padding: 20px 0;
  }

  .maskFrom {
    margin-left: 16px;
    
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
    justify-content: space-between;
    flex-wrap: wrap;
  }

  .gradeItem ol li {
    padding: 0 28px;
    color: #808080;
    border: 1px solid #CCCCCC;
    line-height: 26px;
    border-radius: 13px;
    margin-bottom: 8px;
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
    max-height: 210px;
    overflow-y: scroll;
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
.labelRight .demoInput{
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
    display: -webkit-box;
    display: -webkit-flex;
    display: flex;
    margin-left: 40px;
    -webkit-box-pack: justify;
    /* -webkit-justify-content: space-between; */
    justify-content: space-between;
  }

  .btn button {
    display: block;
    border: none;
    background: none;
    padding: 0 36px;
    line-height: 34px;
    font-size: 13px;
    border-radius: 2px;
  }

  .btn .cancel {
    background: #EDECEB;
    color: #989898;
  }

  .btn .sure {
    background: #D6B77C;
    color: #FFFFFF;
  }
.nullBox{
  display: flex;
  position: relative;
  margin-bottom: 6px;
}
</style>
