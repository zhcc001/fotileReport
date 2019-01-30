<!-- 销售统计 -->
<template>
  <div class="saleStatistics">
    <div class="container">
      <!-- 累计销额概览（名下） -->
      <div class="xiaoEGaiLan">
        <h2>累计销额概览（名下）</h2>
        <div class="zhanBiModel">
          <div class="xinzengZhanBi">
            <p>最近三个月销额（元）</p>
            <b>{{LastMonthSalePrice}}</b>
          </div>
          <i class="line"></i>
          <div class="qianYueZhanBi">
            <p>今年销额累计（元）</p>
            <b>{{YearSalePrice}}</b>
          </div>
          <i class="line"></i>
          <div class="qianYueZhanBi">
            <p>去年销额累计（元）</p>
            <b>{{LastYearSalePrice}}</b>
          </div>
        </div>
      </div>
      <!-- 月销额折线图（名下） -->
      <div class="xiaoELine">
        <h2>月销额折线图（名下）</h2>
        <span class="lineTips">销售（万元）</span>
        <div class="xeLineImg">
          <div id="echarts" :style="{width: '300px', height: '300px'}"></div>
          <div class="lineColor">
            <p><i class="dot1"></i><span>名下去年月销额</span></p>
            <p><i class="dot2"></i><span>名下今年月销额</span></p>
          </div>
        </div>
      </div>
      <!-- 销售概览及公司排行 -->
      <div class="hierarchy" ref='hierarchy'>
        <div class="topTipsBox">
        <p class="topTips"> <img src="./paihang.png" alt=""> 销售概览及公司排行 </p>
      </div>
        <div class="checkBox">
          <!-- 层级筛选 -->
          <div class="hierarchyTop">
            <div class="filter" @click='fixedTop(2)'>
              <div>
                <span class="filterResult">{{timeText}}</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='timeMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>
            <i class="lineI" v-if='AccessId!=4&&AccessId!=5'></i>

            <div class="filter" @click='fixedTop(1)' v-if='AccessId!=4&&AccessId!=5'>
              <div>
                <span class="filterResult" v-if='gangWei' :class="{active:hasMask}">{{statusSelect}}</span>
                <span v-if='!gangWei' :class="{active:hasMask}">{{salesText}}({{salesNum}})</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='hasMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>


          </div>
          <div class="zhanBiModel">
            <div class="xinzengZhanBi">
              <p v-if='leijiTime'>{{timeText}}销额累计（元）</p>
              <p v-if='leiji'>今年销额累计(元)</p>
              <b>{{ThisSalePrice}}</b>
            </div>
            <i class="line"></i>
            <div class="qianYueZhanBi">
              <p v-if='leijiTime'>{{lastTimeText}}销额累计（元）</p>
              <p v-if='leiji'>去年同时期销额累计(元)</p>
              <b>{{LastSalePriceNum}}</b>
            </div>
            <i class="line"></i>
            <div class="qianYueZhanBi">
              <p v-if='leijiTime'>月销额同比</p>
              <p v-if='leiji'>年销额同比</p>
              <b>{{Proportion}}%</b>
            </div>
          </div>
          <div class="xiaoETongB">
            <div class="filter" @click='showXt()'>
              <div>
                <span class="filterResult">{{xiaoETongBiText}}</span>
                <img src="./2.png" class="icon" alt="" v-if='xtFlag'>
                <img src="./1.png" class="icon" alt="" v-else>
              </div>
            </div>
            <!-- <button type="button" class="checkBtn xiaoETongBtn" @click='showXt()'>{{xiaoETongBiText}}</button> -->
            <table border="0" cellspacing="0" cellpadding="0" style="border:0;">
              <thead>
                <tr>
                  <td><span>名下排行</span></td>
                  <td><span>分公司排行</span></td>
                  <td><span>公司名称</span></td>
                  <td><span>业务员</span></td>
                  <td><span>经销商</span></td>
                  <td><span v-if='leijiTime'>{{timeText}}销额</span><span v-if='leiji'>今年销额</span></td>
                  <td><span v-if='leijiTime'>月销额同比</span><span v-if='leiji'>年销额同比</span></td>
                </tr>
              </thead>
              <tbody>
                <tr v-for='(item,index) in saleTableArr' :key='index' v-if='index<10'>
                  <td><span>{{item.NewRn}}</span></td>
                  <td><span>{{item.Rn}}</span></td>
                  <td><span>{{item.Name}}</span></td>
                  <td><span>{{item.UserName}}</span></td>
                  <td><span>{{item.DealerName}}</span></td>
                  <td><span>{{item.Saleprice}}</span></td>
                  <td><span>{{item.Proportion}}</span></td>
                </tr>

              </tbody>
            </table>
            <p class="xtMore" @click='hierarchyMore()' v-if='saleTableArr.length>10'>查看更多(前{{saleTableArr.length}}名)</p>
          </div>



        </div>
      </div>
      <!--年度计销额－经销商排行 -->
      <div class="distributor" ref='distributor'>
          <div class="topTipsBox">
        <p class="topTips"> <img src="./paihang.png" alt="">年度计销额－经销商排行</p>
          </div>
        <div class="checkDis">

          <div class="butCheck">

            <div class="filter" @click='fixedTop(3)' v-if='AccessId==-1'>
              <div>
                <span class="filterResult">{{officeText}}</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='hasMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>
            <i class="lineI" v-if='AccessId==-1'></i>
            <div class="filter" @click='fixedTop(4)'>
              <div>
                <span class="filterResult">{{xiaoEHanBiText}}</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='timeMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>
          </div>


          <table border="0" cellspacing="0" cellpadding="0" style="border:0;">
            <thead>
              <tr>
                <td><span>分公司排行</span></td>
                <td><span>经销商</span></td>
                <td><span>公司数</span></td>
                <td><span>今年销额</span></td>
                <td><span>年销额同比</span></td>
              </tr>
            </thead>
            <tbody>
              <tr v-for='(item,index) in distTableArr' :key='index' v-if='index<10'>
                <td><span>{{item.Rn}}</span></td>
                <td><span>{{item.Name}}</span></td>
                <td><span>{{item.CompanyCount}}</span></td>
                <td><span>{{item.Saleprice}}</span></td>
                <td><span>{{item.Proportion}}</span></td>
              </tr>
            </tbody>
          </table>
          <p class="xtMore" @click='distributorMore()' v-if='distTableArr.length>10'>查看更多(前{{distTableArr.length}}名)</p>
        </div>
      </div>
      <!-- 整体概览－公司评级（岗位范围内） -->
      <div class="grade">
          <div class="topTipsBox">
        <p class="topTips"> <img src="./pingji.png" alt=""> 整体概览－公司评级（岗位范围内）</p>
          </div>
        <div class="gradeBingImg">
          <div class="leftColor">
            <p><i class="BingImgI no"></i><span class="BingImgSpan">未评级</span></p>
            <p><i class="BingImgI B2"></i><span class="BingImgSpan">B-级公司</span></p>
            <p><i class="BingImgI B1"></i><span class="BingImgSpan">B级公司</span></p>
            <p><i class="BingImgI B"></i><span class="BingImgSpan">B+级公司</span></p>
          </div>
          <div id="echarts" :style="{width: '300px', height: '300px'}"></div>
          <div class="rightColor">
            <p><i class="BingImgI A"></i><span class="BingImgSpan">A级公司</span></p>
            <p><i class="BingImgI A1"></i><span class="BingImgSpan">A+级公司</span></p>
            <p><i class="BingImgI A2"></i><span class="BingImgSpan">A-级公司</span></p>


          </div>
        </div>
        <div class="gradeMore">
          <p :class='{gradeMoreP:AccessId==1}'>{{yearGrade}}年第{{jiduGrade}}季度<span>{{nameGrade}}级公司</span>{{numGrade}}家,一共{{totalGrade}}家,占比{{PercentageGrade}}</p>
          <i v-if='AccessId==1'></i>
          <div class="moreText" @click='showNotRating()' v-if='AccessId==1'>
            <p>未评级公司列表（{{CompanyCount}}）</p>
            <b>点击查看>></b>
          </div>
        </div>
      </div>

    </div>
    <!-- 月新增公司－业务员排行层级筛选 -->
    <div class="maskBox" v-if='hierarchyTopMask'>
      
      <div class="hierarchyTopMask">


        <div class="filter" @click="maskTime()">
          <div>
            <span class="filterResult">{{timeText}}</span>
            <img src="./2.png" class="icon" alt="" v-if='timeMask'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
        <i class="lineI" v-if='AccessId!=4&&AccessId!=5'></i>
        <div class="filter" @click="maskStatus()" v-if='AccessId!=4&&AccessId!=5'>

          <div>
            <span class="filterResult" v-if='gangWei' :class="{active:hasMask}">{{statusSelect}}</span>
            <span v-if='!gangWei' :class="{active:hasMask}">{{salesText}}({{salesNum}})</span>
            <img src="./2.png" class="icon" alt="" v-if='hasMask'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
      </div>
      <!-- 层级筛选弹层 -->
      <div class="allMask" v-if='AccessId!=4&&AccessId!=5'>
        <div class="allListMask" v-show="hasMask">
          <div>
            <div class="allListMask" v-show="hasMask">
              <div>
                <div class="leftUl  widthUL grayF9">
                  <ul class="leftList" v-show='showDistrib'>
                    <li v-for='(item,index) in leftList' @click='leftClick(item,index)' :class='{active:index==leftActive}'>{{item.Name}}</li>
                    <li :class='{active:leftTextColor}' v-html='rightAllText' @click='allClick&&leftAllClick()'></li>
                  </ul>
                </div>
                <!-- 业务员列表 -->
                <div class="scroll-list-wrap saleWarp" v-show="showDistrib">
                  <cube-scroll ref="scrollSale" :data="Status" :options="saleOptions">
                    <ul class="rightList white" v-show='showDistrib'>
                      <li @click='rightAllClick()' :class='{active:textColor}' v-html='leftAllText'></li>
                      <li v-for="(item,index) in Status" :key='index' :dataZM='item.ZM' @click='clickFlag&&index!=rightActive&&item.JobID!=5&&rightClick(item,index)'
                        :class='{active:index==rightActive}'>
                        <span>{{item.Name}}</span>
                        <div class="checkIcon" @click='checkSales(item,index)' v-if='checkBox'>
                          <i class="emptyBox" :class="{emptyBox:true,checkBoxIcon:index==isFoodActive}"></i>
                        </div>
                      </li>
                    </ul>
                  </cube-scroll>
                  <div class="letterSort" v-if="hasMask&&sortArr.length>0">
                    <p v-for='(item,index) in sortArr' @click='namePosition(item,index)' :class='{active:index==zmFlag}'>{{item}}</p>
                  </div>
                </div>
              </div>
              <div class="stateBtn">
                <button type="button" class="gray" @click='rightAllClick()'>重 置</button>
                <button type="button" class="yellow" @click='sureSales()'>确 认</button>
              </div>
            </div>
          </div>

        </div>
      </div>
      <!-- 时间筛选 -->
      <div class="timeCheckMask" v-if='timeMask'>
        <ul>
          <li v-for='(item,index) in timeCheckArr' :key='index' :class='{xtActive:index==timeActive}' @click='timeCheckClick(item,index)'>{{item.Name}}</li>
        </ul>
      </div>

    </div>
    <!-- 月新增公司－业务员排行销额/同比 -->

    <!-- 销额同比筛选弹层 -->
    <div class="xtMask" v-if='xtFlag'>
      <div class="xtBox">
        <div class="titleBtn">
          <div class="filter" @click='HideXt()'>
            <div>
              <span class="filterResult">{{xiaoETongBiText}}</span>
              <img src="./2.png" class="icon" alt="" v-if='xtFlag'>
              <img src="./1.png" class="icon" alt="" v-else>
            </div>
          </div>
          <!-- <button type="button" class="checkBtn xiaoETongBtn" @click='showXt()'>{{xiaoETongBiText}}</button> -->
        </div>
        <ul>
          <li v-for='(item,index) in xiaoArr' :key='index' :class='{xtActive:index==isXt}' @click='checkXt(item,index)'>{{item.name}}</li>
        </ul>
      </div>


    </div>

    <div class="disMask" v-if='disMask'>
      <div class="disTopmask">
        <div class="filter" @click='officeMask()' v-if='AccessId==-1'>
          <div>
            <span class="filterResult">{{officeText}}</span>
            <img src="./2.png" class="icon" alt="" v-if='officeFlag'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
        <i class="lineI"></i>
        <div class="filter" @click='hbMask()'>
          <div>
            <span class="filterResult">{{xiaoEHanBiText}}</span>
            <img src="./2.png" class="icon" alt="" v-if='hbFlag'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
      </div>
      <!-- 分公司 -->
      <div class="officeMask" v-if='officeFlag'>
        <ul>
          <li v-for='(item,index) in officeArr' :key='index' :class='{xtActive:index==officeActive}' @click='officeClick(item,index)'>{{item.Name}}</li>
        </ul>
      </div>
      <!-- 环比弹层 -->
      <div class="huanBiMask" v-if='hbFlag'>
        <ul>
          <li v-for='(item,index) in hbArr' :key='index' :class='{xtActive:index==hbActive}' @click='checkHb(item,index)'>{{item.name}}</li>
        </ul>
      </div>
    </div>

    <div class="rightNav">
      <div class="iconBox">
        <!-- <img src="./assets/kefu.png" alt=""> -->
          <img v-if='backTop' src="./top.png" alt="" @click='backTopClick()'>
      </div>
    </div>
  </div>
</template>
<script>
  import eventVue from '../../js/eventVue.js';
  import qs from 'qs';
  import axios from "axios";
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  const data = new Date()
  export default {
    name: 'saleStatistics',
    data() {
      return {
        leijiTime: true,
        leiji: false,
        lastTimeText: '',
        SalePrice: [], //今年销售数据
        rateList: [],
        RateCountList: [],
        huanList: [], //环形图数据
        CompanyCount: -1, //未评级公司数量
        disMask: false,
        hierarchyTopMask: false,
        LastMonthSalePrice: -1, //最近三个月销额
        YearSalePrice: -1, //今年销额累计
        LastYearSalePrice: -1, //去年销额累计
        LastSalePrice: [], //去年
        Month: [], //月份
        officeFlag: true,
        officeText: '分公司',
        isXt: 0, //销额同比选中状态
        isHB: 0,
        xtFlag: false,
        hbFlag: false,
        xiaoETongBiText: '按销额降序',
        xiaoEHanBiText: '按销额降序',
        xiaoArr: [{
          id: 1,
          name: '按销额降序',
        }, {
          id: 2,
          name: '按同比降序',
        }],
        hbArr: [{
          id: 1,
          name: '按销额降序',
        }, {
          id: 2,
          name: '按同比降序',
        }],
        gangWei: true,
        textColor: true,
        sortArr: [], //字母排序
        // postShow: true, //岗位筛选显示隐藏
        moreNum: 0, //高级筛选数量现实
        salesNum: 0, //岗位筛选业务员数量显示
        salesNumFlag: false,
        salesmanFlag: true, //全部业务员
        distribFlag: true, //全部经销商
        allClassify: true, //全部公司分类
        allStatus: true, //全部公司状态
        isStyle: [],
        isStatus: [],
        salesArr: [],
        isFoodActive: -1,
        salesMask: false, //业务员列表
        salesmanLists: [],
        Status: [], //经销商
        // 公司类型列表
        Style: [],
        // 业务员
        Person: [],
        hasMask: true,
        leftActive: -1,
        rightActive: -1,
        statusSelect: "岗位",
        personHasActive: 0,
        personSelect: "高级筛选",
        styleHasActive: 0,
        checkBoxs: [],
        checkAllBox: false,
        idList: [],
        active: false,
        allStateActive: false,
        checkFlag: false,
        SaleID: '', //业务员ID
        DealerID: '', //经销商ID
        flag: false,
        showDistrib: true, //经销商显示隐藏
        topHight: 0,
        zmArr: [],
        nameData: [],
        salesText: '业务员',
        leftList: [],
        rightList: [],
        allText: '',
        linShiArr: [],
        positionID: [],
        leftAllText: '',
        rightIdArr: [],
        checkBox: false,
        clickFlag: true,
        allColor: true,
        boxFlag: false,
        startArr: [],
        zmFlag: -1,
        allClick: true,
        quanBu: '全部',
        leftTextColor: true,
        JobID: [],
        secondStop: 26,
        rightAllText: '',
        topHight: 0,
        // 全部分类
        allStateText: [{
          ID: 1,
          Name: '洽谈中',
          check: false,
        }, {
          ID: 2,
          Name: '签约中',
          check: false,
        }, {
          ID: 3,
          Name: '已签约',
          check: false,
        }, {
          ID: -3,
          Name: '已过期',
          check: false,
        }],
        timeMask: true,
        timeText: '时间筛选',
        timeCheckArr: [],
        timeActive: 0,
        timeValue: '',
        hbActive: 0,
        officeActive: 0,
        officeArr: [],
        ThisSalePrice: -1, //当前销额累计
        LastSalePriceNum: -1, //上次销额累计
        Proportion: -1, //销额同比
        saleTableArr: [],
        distTableArr: [],
        timesoucre: 'a', //时间参数
        IsSort: 1, //销额同比
        BranchID: 0,
        IsSort1: 1,
        nameArr: [],
        yearGrade: 0, //年
        jiduGrade: 0, //季度
        nameGrade: '', //评级名称
        totalGrade: 0, //总数
        PercentageGrade: '', //占比
        numGrade: '',
        backTop: false,
      }
    },
      beforeRouteEnter(to, from, next) {
      if (from.name == 'Home'||from.name == 'AdminIndex') {
          document.body.scrollTop = 0;
          document.documentElement.scrollTop = 0;
      }
      next();
    },
    created() {
      this.monthBrokenLine()
      this.totalSalePrice()
      this.hierarchyTable()
      this.distributorTable()
      this.gradeData()
      this.timeData()
      this.move()

    },
    computed: {
      saleOptions() {
        return {
          pullingDown: false,
          beforePulldown: true,
          isPullingDown: false,
          pulldown: false,
          pullup: false
        }

      },

      ...mapGetters([
        'AccessId'
      ])
    },
    mounted() {
      window.addEventListener('scroll', this.handleScroll)
    },
    methods: {
      handleScroll() {
        let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
        if (scrollTop > 600) {
          this.backTop = true
        } else {
          this.backTop = false
        }
      },
      backTopClick(){
        let scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop
        if(scrollTop!=0){
          window.pageYOffset=0
          document.documentElement.scrollTop=0
          document.body.scrollTop=0
        }
      },
      /***滑动限制***/
      stop() {
        var mo = function (e) {
          e.preventDefault();
        };
        document.body.style.overflow = 'hidden';
        document.addEventListener("touchmove", mo, false); //禁止页面滑动
      },
      /***取消滑动限制***/
      move() {
        var mo = function (e) {
          e.preventDefault();
        };
        document.body.style.overflow = ''; //出现滚动条
        document.removeEventListener("touchmove", mo, false);
      },

      // 累计销额概览
      totalSalePrice() {
        axios({
          url: this.getHost() + '/Statistics/TotalSalePrice',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.LastMonthSalePrice = res.data.Data.LastMonthSalePrice
            this.LastYearSalePrice = res.data.Data.LastYearSalePrice
            this.YearSalePrice = res.data.Data.YearSalePrice
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
      // 月销额折线图
      monthBrokenLine() {
        axios({
          url: this.getHost() + '/Statistics/MonthBrokenLine',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.lineData(res.data.Data.Month, res.data.Data.SalePrice, res.data.Data.LastSalePrice)
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
      lineData(Month, SalePrice, LastSalePrice) {
        let dom1 = document.querySelector('.xeLineImg #echarts')
        let myChartLine = this.echarts.init(dom1)
        // 绘制图表
        myChartLine.setOption({
          title: {
            // text: '折线图堆叠'
          },
          tooltip: {
            trigger: 'axis',

          },
          // legend: {
          //   data: ['名下去年月销额', '名下今年月销额']
          // },
          grid: {
            left: '0',
            right: '6%',
            bottom: '3%',
            containLabel: true
          },
          toolbox: {
            feature: {
              // saveAsImage: {}下载按钮
            }
          },
          xAxis: {
            type: 'category',
            boundaryGap: false,

            data: Month,
            axisLabel: {
              interval: 0,
              fontSize: '11'
            }

          },
          yAxis: {
            type: 'value'
          },

          series: [{
              name: '名下去年月销额',
              type: 'line',
              // stack: '总量',
              data: LastSalePrice,
              itemStyle: {
                normal: {
                  lineStyle: {
                    color: '#85878F'
                  }
                }
              },
            },
            {
              name: '名下今年月销额',
              type: 'line',
              // stack: '总量',
              data: SalePrice,
              itemStyle: {
                normal: {
                  lineStyle: {
                    color: '#DDB971'
                  }
                }
              },
            },

          ]
        })
      },
      fixedTop(num) {
        if (num == 1) {
          this.hierarchyTopMask = true
          this.timeMask = false
          this.hasMask = true
          // this.stop()
          if (this.Status.length > 0) {} else {
            this.axiosloading()
            if (this.AccessId == 2) {
              this.rightAllText = '全部区域经理'
              this.leftAllText = '全部区域经理'
            }
            if (this.AccessId == 1) {
              this.rightAllText = '全部部门经理'
              this.leftAllText = '全部部门经理'
            }
            if (this.AccessId == 3) {
              this.rightAllText = '全部经销商'
              this.leftAllText = '全部经销商'
            }
            if (this.AccessId == 4) {
              this.rightAllText = '全部业务员'
              this.leftAllText = '全部业务员'
            }
            if (this.AccessId == -1) {
              this.rightAllText = '全部分总'
              this.leftAllText = '全部分总'

            }
            axios({
                url: this.getHost() + '/Company/UserInfoListById',
                method: 'post',
                data: qs.stringify({
                  UserId: getCookie('UserId'),
                  token: getCookie('token'),
                })
              })
              .then(res => {
                if (res.data.Status == 1) {
                  this.Status = res.data.Data.list
                  this.sortArr = res.data.Data.zmlist
                  this.Status.forEach((t, i) => {
                    t.flag = false
                    t.positionID = 1
                    if (t.JobID == 5) {
                      this.checkBox = true
                    }
                    this.startArr = this.Status
                    t.list.forEach((m) => {
                      m.flag = false
                      m.positionID = 2
                      m.list.forEach((q) => {
                        q.flag = false
                        q.positionID = 3
                        q.list.forEach((x) => {
                          x.flag = false
                          x.positionID = 4
                          x.list.forEach((n) => {
                            n.flag = false
                            n.positionID = 5
                          })
                        })
                      })
                    })
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
          }
          // this.maskStatus()
        } else if (num == 2) {
          this.hierarchyTopMask = true
          this.hasMask = false
          this.timeMask = true
          // this.stop()

          // this.maskTime()
        } else if (num == 3) {
          this.disMask = true
          this.hbFlag = false
          this.officeFlag = true
          // this.officeMask()
          // this.stop()
          axios({
            url: this.getHost() + '/Statistics/BranchCompanyList',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
            })
          }).then(res => {
            if (res.data.Status == 1) {
              this.officeArr = res.data.Data.list

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
        } else if (num == 4) {
          this.disMask = true
          this.hbFlag = true
          this.officeFlag = false
          // this.hbMask()
        }
      },
      // 岗位筛选遮罩
      maskStatus() {
        if (this.hasMask == false) {
          this.fixedTop(1)
          this.xtFlag = false
          this.timeMask = false

        } else {
          this.move()
          this.hierarchyTopMask = false
        }


      },
      //右边全部按钮点击
      rightAllClick() {
        if (this.isFoodActive != -1) {
          this.isFoodActive = -1
          this.SaleID = ''
          this.textColor = true
          
        }
        this.statusSelect = this.leftAllText
        if (this.leftAllText == this.rightAllText&&this.textColor==true) {
          return false
        }
        this.Status.forEach((y) => {
          this.textColor = true
          this.leftTextColor = true
          this.rightActive = -1
          this.leftList.forEach((s) => {
            if (this.JobID.indexOf(s.JobID) <= -1) {
              this.JobID.push(s.JobID)
            }

          })
          this.leftList.splice(this.JobID.indexOf(y.JobID))
          this.rightIdArr.splice(this.rightIdArr.indexOf(y.ID))
          this.isFoodActive = -1
          this.salesNum = 0
          // this.statusSelect=this.rightAllText
          if (y.JobID == 1) {
            this.leftAllText = this.quanBu + '分总'
          }
          if (y.JobID == 2) {
            this.leftAllText = this.quanBu + '部门经理'
          }
          if (y.JobID == 3) {
            this.leftAllText = this.quanBu + '区域经理'

          }
          if (y.JobID == 4) {
            this.leftAllText = this.quanBu + '经销商'
          }
          if (y.JobID == 5) {
            this.leftAllText = this.quanBu + '业务员'
            if (this.isFoodActive.indexOf(y.ID) > -1) {
              this.isFoodActive = []
              this.textColor = true
              this.leftTextColor = true
              this.allColor = false
            }
          }
          this.rightAllText = this.leftAllText
        })
      },
      //左边全部按钮点击
      leftAllClick() {
        this.textColor = true
        this.leftTextColor = true
        this.leftActive = -1
        this.rightActive = -1
        let allSale = this.leftList[this.leftList.length - 1]
        this.Status = allSale.list
        if (this.isFoodActive.length > 0) {
          this.leftAllText = this.quanBu + '业务员'
          this.textColor = false
        } else {
          this.leftAllText = this.rightAllText
        }
        allSale.list.forEach((i) => {
          if (i.list.length == 0) {
            this.checkBox = true
          }
        })
      },
      //右边列表的点击
      rightClick(item, index) {
        this.isFoodActive = -1
        if (item.JobID == 1) {
          this.leftAllText = this.quanBu + "部门经理"
        }
        if (item.JobID == 2) {
          this.leftAllText = this.quanBu + "区域经理"
        }
        if (item.JobID == 3) {
          this.leftAllText = this.quanBu + "经销商"
        }
        if (item.JobID == 4) {
          this.leftAllText = this.quanBu + "业务员"
        }

        this.rightAllText = this.leftAllText

        this.allColor = false
        let positionID = this.positionID.indexOf(item.positionID)
        let rightID = this.rightIdArr.indexOf(item.ID)

        if (this.leftList.length == 0) {
          this.leftList.push(item)
          this.rightIdArr.push(item.ID)
          this.leftActive = -1
          this.leftTextColor = true
          this.textColor = true
          this.positionID.push(item.positionID)
        }
        if (this.leftList.length == 1) {
          this.leftList.forEach((g) => {
            if (item.positionID != g.positionID) {
              if (this.rightIdArr.indexOf(item.ID) < 0) {
                this.rightIdArr.push(item.ID)
                this.leftList.push(item)
                this.leftActive = -1
                this.leftTextColor = true
                this.textColor = true
              }
              if (this.positionID.indexOf(item.positionID) < 0) {
                this.positionID.push(item.positionID)
              }
            }
            if (item.positionID == g.positionID) {
              this.rightIdArr = []
              this.leftList = []
              if (this.rightIdArr.indexOf(item.ID) < 0) {
                this.rightIdArr.push(item.ID)
                this.leftList.push(item)
                this.leftActive = -1
                this.leftTextColor = true
                this.textColor = true
              }
            }
          })
        }
        if (this.leftList.length > 1) {
          this.leftList.forEach((g) => {
            if (this.rightIdArr.indexOf(item.ID) < 0) {
              this.rightIdArr.push(item.ID)
              this.leftList.push(item)
              this.leftActive = -1
              this.leftTextColor = true
              this.textColor = true
              if (this.positionID.indexOf(item.positionID) < 0) {
                this.positionID.push(item.positionID)
              }
            } else {}
            if (item.positionID == g.positionID && item.ID != g.ID) {
              this.leftActive = -1
              this.leftTextColor = true
              this.textColor = true
              this.leftList.splice(positionID, this.leftList.length - 1 - positionID)
              this.positionID.splice(positionID + 1, this.positionID.length - 1 - positionID)
              this.rightIdArr.splice(rightID + 1, this.rightIdArr.length - 1 - rightID)
            }
          })
        }
        this.rightActive = -1
        item.list.forEach((k, l) => {
          if (k.list.length <= 0) {
            this.clickFlag = false
            this.checkBox = true
            this.leftActive = -1
            this.textColor = true
            this.leftTextColor = true
          } else {
            this.checkBox = false
            this.clickFlag = true
          }
        })
        this.Status = item.list
        this.sortArr = item.zmlist
      },
      //左边列表的点击
      leftClick(item, index) {
        if (item.JobID == 1) {
          this.leftAllText = this.quanBu + "分总"
        }
        if (item.JobID == 2) {
          this.leftAllText = this.quanBu + "部门经理"
        }
        if (item.JobID == 3) {
          this.leftAllText = this.quanBu + "区域经理"
        }
        if (item.JobID == 4) {
          this.leftAllText = this.quanBu + "经销商"
        }

        this.leftActive = index
        if (this.leftActive >= 0) {
          this.textColor = false
          this.leftTextColor = false
        }
        if (item.list.length >= 0) {
          this.checkBox = false
          this.clickFlag = true
        } else {
          this.checkBox = true
          this.clickFlag = false
        }
        this.leftActive = index
        let idArr = []
        this.leftList.forEach((e, f) => {
          e.list.forEach((c, d) => {
            if (idArr.indexOf(c.ID) < 0) {
              idArr.push(c.ID)
            }
            if (item.ID == c.ID) {
              this.Status = e.list
              this.sortArr = e.zmlist
              this.rightActive = d
            }
          })
        })
        if (idArr.indexOf(item.ID) < 0) {
          this.Status = this.startArr
          this.Status.forEach((t, i) => {
            this.sortArr = t.zmlist
            if (t.ID === item.ID) {
              this.rightActive = i
            }
          })
        }
      },
      checkSales(item, index) {
        this.salesmanFlag = false
        this.isFoodActive = index
        this.SaleID = item.ID
        this.statusSelect = item.Name
        this.textColor = false

      },
      // 业务员列表重置
      resetSales() {
        this.isFoodActive = -1
        this.SaleID = ''
      },
      //确认业务员
      sureSales() {
        console.log(this.isFoodActive)
        if (this.isFoodActive == -1) {
          this.textColor = true
          this.statusSelect = this.leftAllText
        }
        if (this.leftList.length == 0) {
          this.DealerID = ''
        } else {
          this.DealerID = this.leftList[this.leftList.length - 1].ID

        }
        // this.statusSelect = this.leftAllText
        this.hierarchyTopMask = false
        // eventVue.$emit("gangWeiHide", this.hasMask)
        this.hierarchyTable()
        this.move()
      },
      // 首字母定位
      namePosition(item, index) {
        this.zmFlag = index
        let newList = this.sortArr.slice(0, this.sortArr.indexOf(item))
        let number = 0
        this.Status.forEach((el) => {
          if (newList.indexOf(el.ZM) > -1) {
            number++
          }
        })
        this.scrollTo(number)
      },
      scrollTo(number) {
        var liHeight = 40 //li的高度
        this.$refs.scrollSale.scrollTo(0, this.scrollToY = -((number) * liHeight), )
      },
      timeData() {
        axios({
          url: this.getHost() + '/Statistics/TimeSoucre',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.timeCheckArr = res.data.Data
            this.timeText = this.timeCheckArr[this.timeCheckArr.length - 1].Name
            this.timeActive = this.timeCheckArr.length - 1
            this.lastTimeText = parseInt(this.timeText.substring(0, 4)) - 1 + this.timeText.substring(4)
          }
        })
      },
      //时间筛选
      maskTime() {
        if (this.timeMask == false) {
          this.fixedTop(2)
          this.hasMask = false
          this.xtFlag = false
        } else {
          this.hierarchyTopMask = false
          this.move()
        }
      },
      //选择时间
      timeCheckClick(c, d) {
        console.log(c.Name,'c.Name')
        if (c.Name == '今年累计') {
          this.leijiTime = false
          this.leiji = true
          this.timesoucre=''
        } else {
          this.leijiTime = true
          this.leiji = false
        this.timesoucre = c.Value
        }
        this.timeActive = d
        this.timeValue = c.Value
        this.timeText = c.Name
        this.timeMask = false
        this.xtFlag = false
        // eventVue.$emit("gangWeiHide", this.hasMask)
        this.move()
        this.hierarchyTopMask = false
        this.lastTimeText = parseInt(this.timeText.substring(0, 4)) - 1 + this.timeText.substring(4)
        this.hierarchyTable()
      },
      //销售概览及公司排行Table
      hierarchyTable() {
        if (this.timesoucre == 'a') {
          if (data.getMonth() + 1 == 1) {
            this.timesoucre = data.getFullYear() - 1 + '-' + 12

          } else {
            this.timesoucre = data.getFullYear() + '-' + data.getMonth()
          }
        }

        axios({
          url: this.getHost() + '/Statistics/SaleView',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            timesoucre: this.timesoucre,
            SaleID: this.SaleID,
            DealerID: this.DealerID,
            IsSort: this.IsSort,
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.ThisSalePrice = res.data.Data.ThisSalePrice
            this.LastSalePriceNum = res.data.Data.LastSalePrice
            this.Proportion = res.data.Data.Proportion
            this.saleTableArr = res.data.Data.list
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
      //查看更多五十名
      hierarchyMore() {
        sessionStorage.setItem('hieTrList', JSON.stringify(this.saleTableArr))
        this.$router.push({
          path: '/companyRanking',
          query:{
            leijiTime:this.leijiTime,
            leiji:this.leiji,
            timeText:this.timeText
          }

        })
      },

      //   按销额/同比排序
      // 显示弹层
      showXt() {
        this.xtFlag = true
        // eventVue.$emit("xtShow", this.xtFlag)
        // this.stop()
      },
      checkXt(t, i) {
        this.isXt = i
        this.xtFlag = false
        this.xiaoETongBiText = t.name
        this.IsSort = t.id
        // eventVue.$emit("xtHide", this.xtFlag)
        this.hierarchyTable()
        this.move()
      },
      HideXt() {
        this.xtFlag = false
        this.move()
      },
      //   年度计销额－经销商排行Table
      distributorTable() {
        axios({
          url: this.getHost() + '/Statistics/DealerRank',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            BranchID: this.BranchID,
            IsSort: this.IsSort1,
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.distTableArr = res.data.Data.list
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
      //查看更多五十名
      distributorMore() {
        sessionStorage.setItem('disTrList', JSON.stringify(this.distTableArr))
        this.$router.push({
          path: '/distriRanking',
          

        })
      },
      //分公司筛选
      officeMask() {
        if (this.officeFlag == false) {
          this.fixedTop(3)
          this.hbFlag = false
        } else {
          this.move()
          this.disMask = false
        }
      },
      officeClick(s, d) {
        this.officeActive = d
        this.BranchID = s.ID
        this.distributorTable()
        this.disMask = false
        this.move()
      },
      hbMask() {
        if (this.hbFlag == false) {
          this.fixedTop(4)
          this.officeFlag = false
        } else {
          this.disMask = false
          this.move()
        }
      },

      //   同比
      checkHb(a, b) {
        this.hbFlag = false
        this.xiaoEHanBiText = a.name
        this.IsSort1 = a.id
        this.hbActive = b
        this.distributorTable()
        this.disMask = false
        this.move()
      },
      // 整体概览-公司评级
      gradeData() {
        axios({
          url: this.getHost() + '/Statistics/CompanyAnnularRate',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.huanList = res.data.Data.list
            this.CompanyCount = this.huanList[6].RateCount
            this.huanData(this.huanList[0].RateCount, this.huanList[1].RateCount, this.huanList[2].RateCount, this.huanList[
              3].RateCount, this.huanList[4].RateCount, this.huanList[5].RateCount, this.huanList[6].RateCount)
              this.yearGrade = this.huanList[1].Year, //年
              this.jiduGrade = this.huanList[1].Quarter, //季度
              this.nameGrade = this.huanList[1].Rate, //评级名称
              this.totalGrade = this.huanList[1].CompanyCount, //总数
              this.PercentageGrade = this.huanList[1].Percentage, //占比
              this.numGrade = this.huanList[1].RateCount,
              this.huanList.forEach(item => {
                this.nameArr.push(item.Rate)
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
      huanData(num1, num2, num3, num4, num5, num6, num7) {
        // console.log(num2,'num2')A+
        // console.log(num1,'num1')A
        // console.log(num3,'num3')A-
        // console.log(num4,'num4')B+
        // console.log(num5,'num5')B
        // console.log(num6,'num6')B-
        // console.log(num7,'num7')未评级
        let dom2 = document.querySelector('.gradeBingImg #echarts')
        let myChartBing = this.echarts.init(dom2)


        // 绘制图表
        myChartBing.setOption({
          tooltip: {
            trigger: 'item',
            formatter: "{a} <br/>{b}: {c}"
          },
          // legend: {
          //   orient: 'vertical',
          //   x: 'left',
          //   // data:['直接访问','邮件营销','联盟广告','视频广告','搜索引擎']
          // },
          color: ['#E0C58B', '#EFD59E', '#FAE4B6', '#E68F8F', '#F8A6A6', '#FFD5D5', '#d4d4d4'],
          series: [{
            name: '评级公司',
            type: 'pie',
            radius: ['50%', '70%'],
            avoidLabelOverlap: false,
            label: {
              normal: {
                show: false,
                position: 'center'
              },
              emphasis: {
                show: true,
                textStyle: {
                  fontSize: '18',
                  fontWeight: 'bold'
                }
              }
            },
            labelLine: {
              normal: {
                show: false
              }
            },
            data: [{
                value: num1,
                name: 'A+',
              },
              {
                value: num2,
                name: 'A',
              },
              {
                value: num3,
                name: 'A-',
              },
              {
                value: num5,
                name: 'B',
              },
              {
                value: num4,
                name: 'B+',
              },
              {
                value: num6,
                name: 'B-',
              },
              {
                value: num7,
                name: '未评级',


              },
            ]
          }]
        })
        let that=this
        myChartBing.on('click', function (params) {
          if (params.value) {
            let index=that.nameArr.indexOf(params.name)
            if(index!=-1){
              that.yearGrade = that.huanList[index].Year, //年
            that.jiduGrade = that.huanList[index].Quarter, //季度
            that.nameGrade = that.huanList[index].Rate, //评级名称
            that.totalGrade = that.huanList[index].CompanyCount, //总数
            that.PercentageGrade = that.huanList[index].Percentage, //占比
            that.numGrade = that.huanList[index].RateCount
            }else{
              that.yearGrade = that.huanList[that.huanList.length-1].Year, //年
            that.jiduGrade = that.huanList[that.huanList.length-1].Quarter, //季度
            that.nameGrade = that.huanList[that.huanList.length-1].Rate, //评级名称
            that.totalGrade = that.huanList[that.huanList.length-1].CompanyCount, //总数
            that.PercentageGrade = that.huanList[that.huanList.length-1].Percentage, //占比
            that.numGrade = that.huanList[that.huanList.length-1].RateCount
            }
          }
        });
      },
      //查看更多未评级
      showNotRating() {
        this.$router.push({
          path: '/notRating',
          // query: {
          // }
        })
      },
    }
  }

</script>
<style scoped>
  @import '../../common/filterH.css';

  .businessStatistics {
    padding-top: 4px;
  }

  .xeLineImg {
    margin-top: -30px;
  }

  .xiaoEGaiLan {
    background: white;
    padding-top: 20px;
  }

  .hierarchy {
    background: white;
  }

  .hierarchyTop,
  .hierarchyTopMask {
    display: flex;
    height: 36px;
    border-bottom: 1px solid #F5F5F5;
    justify-content: space-around;

  }

  .hierarchy .zhanBiModel b {
    color: #DDB971;
    font-weight: normal;
    padding-top: 4px;
  }

  .hierarchy .zhanBiModel {
    padding-top: 16px;
    padding-bottom: 16px;
  }

  .allListMask ul li.active {
    color: #BB9F61
  }

  h2 {
    font-size: 16px;
    color: #404040;
    padding-left: 10px;
    border-left: 2px solid #D5AE61;
    font-weight: 550;
    width: 88%;
    margin: 0 auto;
    margin-bottom: 16px;
  }

  .xiaoETongB .filter {
    padding-top: 0;
    padding-bottom: 12px;
    margin: 0 auto;
  }

  .filter {
    padding-top: 12px;
  }

  .filter>div {
    padding: 0 20px;
  }

  .topTips {
    display: inline-block;
    /* background: #FFF5E7; */
    /* padding-left: 16px; */
    color: #D3B488;
    line-height: 34px;
    font-size: 11px;
  }

  .lineI {
    display: block;
    width: 2px;
    height: 16px;
    background: #F5F5F5;
    margin-top: 12px;
  }

  .filter span {
    color: #464646 !important;
    font-size: 14px;
  }

  .zhanBiModel {
    display: flex;
    justify-content: space-around;
    border-bottom: 1px solid #F5F5F5;
    width: 96%;
    margin: 0 auto;
    padding-bottom: 20px;
  }



  .zhanBiModel p {
    font-size: 12px;
    color: #616161;
    line-height: 24px;
    text-align: center;
  }


  .zhanBiModel b {
    display: block;
    font-size: 15px;
    color: #282828;
    text-align: center;
    font-weight: 550;
  }

  .zhanBiModel i {
    width: 2px;
    height: 36px;
    background: #F5F5F5;
    margin-top: 6px;
  }

  .xiaoELine {
    padding-top: 20px;
    margin-bottom: 10px;
    background: white;
    padding-bottom: 24px;
    margin-bottom: 10px;
  }

  .lineTips {
    font-size: 10px;
    color: #303030;
    padding-left: 24px;
    background: none;
    margin-bottom: 24px;
  }

  .topTipsBox{
    display: flex;
    justify-content: space-around;
    background: #FFF5E7;
  }

  .topTips img {
    display: block;
    width: 12px;
    height: 12px;
    float: left;
    /* margin-left: 108px; */
    margin-top: 9px;
    margin-right: 6px;

  }
  

  .xiaoETongB {
    padding-top: 16px;
    background: white;
    padding-bottom: 10px;
  }

  .checkBtn {
    display: block;
    background: #F5F5F5;
    border-radius: 14px;
    padding: 0 30px;
    height: 26px;
    line-height: 26px;
    font-size: 11px;
    color: #5F5F5F;
    margin-left: 10px;
    margin-bottom: 16px;
  }

  table {
    width: 100%;
    border: none;
  }

  table thead tr td {
    font-size: 11px;
    color: #C0C0C0;
    width: 34px;
  }

  table thead tr {
    background: #F5F5F5;
  }

  table tr td {
    border: 0;
    font-size: 10px;
    color: #303030;
    text-align: center;
    padding: 12px 2px;

  }

  table tr td span {
    line-height: 18px;

  }

  table tbody tr:nth-child(even) {
    background: #F5F5F5;
  }

  .maskBox,
  .xtBox,
  .disCheckBox {
    position: absolute;
    width: 100%;
  }

  .xtMask {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    position: fixed;
    top: 0;
    left: 0;
    z-index: 105;
  }

  .huanBiMask {
    width: 100%;
    background: white;
    position: absolute;
    top: 40px;
    z-index: 105;
    height: 12vh;
  }

  .xtMask ul li,
  .huanBiMask ul li,
  .timeCheckMask ul li,
  .officeMask ul li {
    font-size: 12px;
    color: black;
    padding: 10px;
    border-bottom: 1px solid #F5F5F5;
  }

  .xtMask ul li.xtActive,
  .huanBiMask ul li.xtActive,
  .timeCheckMask ul li.xtActive,
  .officeMask ul li.xtActive {
    color: #DDB971;
    font-size: 12px;
  }

  .xtMore {
    text-align: center;
    font-size: 13px;
    color: #696969;
    padding: 18px 0;
  }

  .distributor {
    background: white;
    padding-bottom: 10px;
  }

  .butCheck {
    display: flex;
    padding: 14px 0;
    justify-content: space-around;
  }

  .branchOffice {
    margin-right: 14px;
  }

  .branchOffice .icon {
    float: right;
    display: block;
    width: 12px;
    height: 6px;
    margin-top: 10px;
    margin-left: 4px;
  }

  .grade {
    background: white;
    padding-bottom: 58px;
  }

  .gradeMore {
    display: flex;
    justify-content: space-around;
    width: 88%;
    margin: 0 auto;
  }

  .gradeMoreP {
    width: 40%;
  }

  .moreText {
    width: 40%;
  }

  .gradeMore p {
    font-size: 12px;
    color: #696969;
    padding-top: 10px;
    line-height: 20px;
  }

  .gradeMore i {
    width: 2px;
    height: 66px;
    background: #F5F5F5;
    margin-top: 6px;
  }

  .gradeMore b {
    display: block;
    color: #AC8F4F;
    font-size: 12px;
  }

  .saleWarp .letterSort {
    top: 0;
    position: absolute;
  }

  .lineColor {
    width: 88%;
    margin: 0 auto;
    display: flex;
    justify-content: space-around;
    padding-top: 10px;
  }

  .lineColor p {
    display: flex;
    width: 50%;
  }

  .lineColor i {
    display: block;
    width: 6px;
    height: 6px;
    border-radius: 50%;
    margin-right: 9px;
    margin-top: 2px;
    margin-left: 20%;
  }

  .lineColor .dot1 {
    background: #8A8D98;
  }

  .lineColor .dot2 {
    background: #DDB971;
  }

  .lineColor span {
    display: block;
    color: #303030;
    font-size: 11px;
  }

  .allListMask {
    top: 18px;
    z-index: 3000000000 !important;
  }

  .timeCheckMask {
    width: 100%;
    top: 34px;
    position: absolute;
    /* max-height: 60vh; */
    z-index: 105;

  }

  .officeMask,
  .huanBiMask {
    width: 100%;
    top: 38px;
    position: absolute;
    /* max-height: 60vh; */
    z-index: 105;
    border-top: 1px solid #f5f5f5;

  }

  .timeCheckMask ul {
    width: 100%;
    height: 70vh;
    background: white;
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
  }



  .mask {
    position: fixed;
    height: 54vh;
  }


  .BingImgI {
    display: block;
    width: 3px;
    height: 11px;
    border-radius: 2px;
    margin-right: 8px;
  }

  .A {
    background: #E0C58B;

  }

  .A1 {
    background: #EFD59E;

  }

  .A2 {
    background: #FAE4B6;

  }

  .B {
    background: #E68F8F;

  }

  .B1 {
    background: #F8A6A6;

  }

  .B2 {
    background: #FFD5D5;

  }

  .no {
    background: #F2F2F2;
  }

  .gradeBingImg {
    width: 86%;
    display: flex;
    justify-content: space-around;
    margin: 0 auto;

  }

  .leftColor,
  .rightColor {
    padding-top: 40px;
  }

  .gradeBingImg p {
    display: flex;
    margin-bottom: 10px;
  }

  .BingImgSpan {
    font-size: 13px;
    color: #696969;
    display: block;
  }

  .maskBox {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    z-index: 10000000;
  }

  .hierarchyTopMask {
    background: white;
  }

  .titleBtn {
    width: 100%;
    padding: 12px;
    background: white;
  }

  .titleBtn .filter {
    padding-top: 0;
    margin: 0 auto;
  }

  .titleBtn .checkBtn {
    margin-bottom: 0;
  }

  .xtBox ul {
    background: white;
    height: 60vh;
  }

  .disMask {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    z-index: 105;
    position: fixed;
    top: 0;
    left: 0;
  }

  .disTopmask {
    width: 100%;
    background: white;
    display: flex;
    padding-bottom: 12px;
    justify-content: space-around;
  }

  .checkDis .butCheck {
    padding-top: 0;
  }

  .huanBiMask ul,
  .officeMask ul {
    height: 60vh;
    background: white;
  }
  .rightNav {
    position: fixed;
    right: 10px;
    bottom: 8vh;
  }

  .rightNav a {
    display: block;
  }

  .rightNav img {
    display: block;
    width: 40px;
    height: 40px;
  }


</style>
