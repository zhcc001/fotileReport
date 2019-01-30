<template>
  <!-- 竞品分析 -->
  <div id="competeProductAnalysis">
    <div class="date">
      <p class="years">
        <span v-for='(item,index) in yearList'  :class="{active:item==year}" @click="handleYear(item,index)" :key='index'>{{item}}年</span>
      </p>
      <div class="months" ref="wrapper">
        <ul>
          <li @click="handleMonth(item)" :class="{active:item == month}" v-for="(item,index) in months" :key="index">{{item}}月</li>
        </ul>
      </div>
    </div>
    <!-- 月份竞品分析 -->
    <div class="model">
      <div class="competition">
        <div class="followDays">
          <p>{{complete.Month}}月销售分析数据</p>
        </div>
        <div class="competitionInfo" v-if="infoNull">
          <p>
            <span>公司销售金额</span>
            <span>{{complete.CompanySaleCount}}</span>
          </p>
          <p>
            <span>竞品销量</span>
            <span>{{complete.SaleCount}}</span>
          </p>
          <p>
            <span>竞品出样量</span>
            <span>{{complete.SampleCount}}</span>
          </p>
        </div>
        <div class="competitionNum" v-if="infoNull">
          <p>门店数&emsp;<span>{{complete.ShopCount}}</span></p>
          <p>开工数&emsp;<span>{{complete.WorkCount}}</span></p>
          <p>设计师数&emsp;<span>{{complete.StylistCount}}</span></p>
        </div>
        <div class="infoNull" v-if="!infoNull">
          信息为空
        </div>
        <a href="javascript:;" id="button" v-if="show" @click="editCompete">编辑当前月份数据</a>
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
  const date = new Date()
  export default {
    name: 'competeProductAnalysis',
    data() {
      
      return {
        // years: date.getFullYear(),
        yeared:date.getFullYear()-1,
        month: date.getMonth(),
        months: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12],
        ID: '',
        complete: {
          Month: '',
          SaleCount: '',
          WorkCount: '',
          SampleCount: '',
          ShopCount: '',
          StylistCount: '',
        },
        infoNull: false,
        show: false,
        active:'',
        yearIndex:0,
        yearList:[date.getFullYear()-1,date.getFullYear()],
        // active:false
      }
    },
    created() {
      // this.time()
      this.ID = this.$route.query.id
       let month = this.$route.query.month|| ''
       let year = this.$route.query.year || ''
      this.stylePlay = this.$route.query.stylePlay||''
      if(month){
          this.month =month;
      }
      else{
        if(date.getMonth()+1==1){
          this.month = 12;
        }
        else{
          this.month =date.getMonth();
        }
      }

      if(year){
       this.year=year;
      }else{
        if(date.getMonth()+1==1){
          this.year = date.getFullYear()-1;
        }
        else{
          this.year =date.getFullYear();
        }

      }


      this.getComplete(this.ID,this.month,this.year)
      this.showModel()
    },
    computed: {
      ...mapGetters([
        'AccessId'
      ])
    },
    methods: {
      editCompete() {
        this.$router.push({
          path: '/editCompete',
          query: {
            id: this.ID,
            month: this.month,
            year: this.year,
            enter:2,
          }
        })
      },
      //判断角色，显示对应的内容
      showModel() {
        console.log(this.AccessId,this.stylePlay)
        if ((this.AccessId == 5 && this.stylePlay == "") || this.AccessId == -1) {
          this.show = true

        } else {
          this.show = false
        }
      },
      handleYear(item,index) {
        this.year=item
        this.getComplete(this.ID, this.month, this.year)

      },
      
      handleMonth(num) {
        this.month = num
        this.getComplete(this.ID, this.month, this.year)
      },
      //获取竞品信息
      getComplete(ID, month, year) {
        axios({
            url: this.getHost() + '/Company/CompanyAnalyzeByYM',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
              Year: year,
              Month: month,
              CompanyID: ID
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.complete = res.data.Data
              if (this.complete.Code == 1) {
                this.infoNull = true
              } else {
                this.infoNull = false
              }
             
        

            } else if (res.data.Status < 0) {
              this.delCookie("UserId")
              this.delCookie("token")
              this.setAccessId('')
              location.replace('/')
            } else {
              this.getToast(res.data.Message, 'warn')
            }
          })
      },
    }
  }

</script>

<style scoped>
  #competeProductAnalysis {
    padding-top: 15px;
  }

  .date {
    background-color: #fff;
    padding: 10px 15px 0;
  }

  .years {
    padding-bottom: 10px;
    border-bottom: 1px solid #f0f0f0;
  }

  .years span {
    padding: 2px 10px;
  }

  .years span.active,
  .months li.active {
    background-color: #F6EAD4;
    color: #E2C78F;
    border-radius: 4px;
    line-height: 26px;
  }

  .months {
    position: relative;
    height: 58px;
    width: 100%;
    background-color: #fff;
    overflow: hidden;
    overflow-x: auto;
  }

  .months ul {
    position: absolute;
    /* width: 570px; */
    height: 100%;
    display: -webkit-box;
    display: -webkit-flex;
    /* display: flex; */
    /* -webkit-box-pack: center; */
    /* -webkit-justify-content: center; */
    /* justify-content: center; */
    /* -webkit-box-align: center; */
    /* -webkit-align-items: center; */
    align-items: center;
    /* -webkit-flex-wrap: wrap; */
    /* flex-wrap: wrap; */
  }

  .months li {
    width: 55px;
    float: left;
    text-align: center;

  }

  /* 月份竞品分析 */

  .model {
    padding: 0 10px;
    box-sizing: border-box;
  }

  .competition {
    background-color: #fff;
    width: 100%;
    border-radius: 4px;
    padding: 15px;
    margin-top: 10px;
    box-sizing: border-box;
  }

  .followDays .month {
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 12px;
    background-color: #F6EAD4;
    border-radius: 4px;
    padding: 0 10px;
    height: 20px;
    margin-left: 10px;
  }

  .competitionInfo {
    display: flex;
    padding: 10px;
    border-top: 4px solid #f0f0f0;
    border-bottom: 1px solid #f0f0f0;
  }

  .competitionInfo p {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    flex-grow: 1;
    border-right: 1px solid #f0f0f0;
  }

  .competitionInfo p:last-child {
    border: none;
  }

  .competitionInfo p span:nth-child(1) {
    margin-bottom: 10px;
    color: #808080;
  }

  .competitionInfo p span:nth-child(2) {
    color: #000;
  }

  .competitionNum {
    display: flex;
    padding: 10px 0;
  }

  .competitionNum p {
    height: 35px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-grow: 1;
    color: #808080;
  }

  .competitionNum p span {
    color: #000;
  }

  .competitionNum p:nth-child(1),
  .competitionNum p:nth-child(2) {
    border-right: 1px solid #f0f0f0;
  }

  #button {
    background-image: none;
    border: 1px solid #BB9F61;
    color: #BB9F61;
  }

  .followInfo {
    padding-bottom: 15px;
  }

  .followDays {
    display: flex;
    align-items: center;
    padding: 15px 0;
  }

  .followDays p {
    width: 0;
    flex-grow: 1;
    font-size: 16px;
    font-weight: 500;
    color: #4d4d4d;
    display: flex;
    align-items: center;
  }

  .followDays p span {
    font-size: 20px;
    color: #BB9F61;
  }

  .followDays a {
    color: #BB9F61;
  }

  .followInfo {
    padding-bottom: 15px;
  }

  .followDays {
    display: flex;
    align-items: center;
    padding: 15px 0;
  }

  .followDays p {
    width: 0;
    flex-grow: 1;
    font-size: 16px;
    font-weight: 500;
    color: #4d4d4d;
    display: flex;
    align-items: center;
  }

  .followDays p span {
    font-size: 20px;
    color: #BB9F61;
  }

  .followDays a {
    color: #BB9F61;
  }

  .infoNull {
    color: #ccc;
    font-size: 28px;
    font-weight: 600;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100px;
    border-top: 4px solid #f0f0f0;
  }

</style>
