<!-- 业务统计 -->
<template>
  <div class="businessStatistics">
    <div class="container">
      <div class="huanTu">
        <div class="top">
          <h2>累计签约概览（名下）</h2>
          <p class="topTips">筛选范围内 {{nameGrade}}公司{{StatusCountTop}}家，名下累计{{CompanyCountTop}}家，占比{{yiQianYue}}</p>
        </div>
        <div class="gradeBingImg">
          <div class="leftColor">
            <p><i class="BingImgI B1"></i><span class="BingImgSpan">累计已过期</span></p>
            <p><i class="BingImgI B"></i><span class="BingImgSpan">累计已签约</span></p>
          </div>
          <div id="echarts" :style="{width: '300px', height: '300px'}"></div>
          <div class="rightColor">
            <p><i class="BingImgI A"></i><span class="BingImgSpan">累计洽谈中</span></p>
            <p><i class="BingImgI A1"></i><span class="BingImgSpan">累计签约中</span></p>


          </div>
        </div>
        <div class="huanTuButtom">
          <p>各状态比例环状图</p>
        </div>

      </div>
      <div class="lineTu">
        <div class="top">
          <h2>最近半年各状态公司累计折线图（名下）</h2>
          <span class="lineTips">累计数（月）</span>
        </div>
        <div class="xeLineImg">
          <div id="echarts" :style="{width: '300px', height: '300px'}"></div>
          <div class="lineColor">
            <p><i class="dot1"></i><span>总共公司数</span></p>
            <p><i class="dot2"></i><span>累计洽谈中</span></p>
            <p><i class="dot3"></i><span>累计签约中</span></p>
            <p><i class="dot4"></i><span>累计已签约</span></p>
          </div>
        </div>
        <div class="lineBottom">
          <div class="waitXuQian">
            <p>待续签公司列表（{{xuQianNum}}）</p>
            <a href="javascript:void(0)" @click='waitXuQian()'>点击查看>></a>
          </div>
          <i class="line"></i>
          <div class="noXuQian">
            <p>未签约公司列表（{{qianYueNum}}）</p>
            <a href="javascript:void(0)" @click='noQianYue()'>点击查看>></a>
          </div>
        </div>
      </div>
      <!-- 层级筛选 -->
      <div class="hierarchy" ref='hierarchy'>
          <div class="topTipsBox">
        <p class="topTips"> <img src="./paihang.png" alt="">月-业务概览及各状态公司新增业务员排行榜</p>
          </div>
        <div class="hierarchyTop" v-if='AccessId!=4&&AccessId!=5'>
          <div class="filter" @click='fixedTop(1)'>
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
            <p>{{getMonth}}月累计新增／今年累计新增</p>
            <b>{{monthAdd}}/{{yearAdd}}</b>
            <p>{{getMonth}}月新增占比<span>{{AddProportion}}</span></p>
          </div>
          <i class="line"></i>
          <div class="qianYueZhanBi">
            <p>{{getMonth}}月累计签约／今年累计签约</p>
            <b>{{monthQY}}/{{yearQY}}</b>
            <p>{{getMonth}}月新增占比<span>{{QYProportion}}</span></p>
          </div>
        </div>
        <div class="checkTable">

          <div class="butCheck">

            <div class="filter" @click='fixedTop(3)'>
              <div>
                <span class="filterResult">{{statuText}}</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='hasMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>
            <i class="lineI"></i>
            <div class="filter" @click='fixedTop(4)'>
              <div>
                <span class="filterResult">{{timeText}}</span>
                <!-- <img src="./2.png" class="icon" alt="" v-if='timeMask'> -->
                <img src="./1.png" class="icon" alt="">
              </div>
            </div>
          </div>
          <table border="0" cellspacing="0" cellpadding="0" style="border:0;">
            <thead>
              <tr>
                <td><span>名下排行</span></td>
                <td><span>分公司排行</span></td>
                <td><span>业务员</span></td>
                <td><span>新增洽谈中</span></td>
                <td><span>新增签约中</span></td>
                <td><span>新增签约</span></td>
              </tr>
            </thead>
            <tbody>
              <tr v-for='(item,index) in distTableArr' :key='index' v-if='index<10'>
                <td><span>{{item.NewRn}}</span></td>
                <td><span>{{item.Rn}}</span></td>
                <td><span>{{item.Name}}</span></td>
                <td><span>{{item.QTCount}}</span></td>
                <td><span>{{item.QYCount}}</span></td>
                <td><span>{{item.YQYCount}}</span></td>
              </tr>
            </tbody>
          </table>
          <p class="xtMore" @click=' newSale()' v-if='distTableArr.length>10'>查看更多(前{{distTableArr.length}}名)</p>
        </div>
      </div>
    </div>
    <div class="maskBox" v-if='hierarchyTopMask'>
      <div class="hierarchyTopMask">

        <div class="filter" @click="maskStatus()">
          <div>
            <span class="filterResult" v-if='gangWei' :class="{active:hasMask}">{{statusSelect}}</span>
            <span v-if='!gangWei' :class="{active:hasMask}">{{salesText}}({{salesNum}})</span>
            <img src="./2.png" class="icon" alt="" v-if='hasMask'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
      </div>
      <!-- 层级筛选弹层 -->
      <div class="allMask">
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
                      <li v-for="(item,index) in Status" :key='index' :dataZM='item.ZM' @click='clickFlag&&index!=rightActive&&rightClick(item,index)'
                        :class='{active:index==rightActive}'>
                        <span>{{item.Name}}</span>
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

    </div>
    <div class="disMask" v-if='disMask'>
      <div class="disTopmask">
        <div class="filter" @click='officeMask()'>
          <div>
            <span class="filterResult">{{statuText}}</span>
            <img src="./2.png" class="icon" alt="" v-if='officeFlag'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
        <i class="lineI"></i>
        <div class="filter" @click='hbMask()'>
          <div>
            <span class="filterResult">{{timeText}}</span>
            <img src="./2.png" class="icon" alt="" v-if='hbFlag'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
      </div>
      <!-- 分公司 -->
      <div class="officeMask" v-if='officeFlag'>
        <ul>
          <li v-for='(item,index) in statuArr' :key='index' :class='{xtActive:index==statuActive}' @click='officeClick(item,index)'>{{item.Name}}</li>
        </ul>
      </div>
      <!--日期弹层 -->
      <div class="huanBiMask" v-if='hbFlag'>
        <ul>
          <li v-for='(item,index) in timeArr' :key='index' :class='{xtActive:index==timeActive}' @click='timeClick(item,index)'>{{item.Name}}</li>
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
  import qs from 'qs'
  import axios from "axios";
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  const date = new Date()
  export default {
    name: 'BusinessStatistics',

    data() {
      return {
        getMonth: date.getMonth() + 1,
        statuIsSortID: 1,
        statuText: '新增洽谈中降序',
        distTableArr: [],
        statuActive: 0,
        statuArr: [{
          id: 1,
          Name: '新增洽谈中降序'
        }, {
          id: 3,
          Name: '签约中降序'
        }, {
          id: 5,
          Name: '已签约降序'
        }, ],
        timeID: 2,
        timeText: '本月',
        timeActive: 1,
        timeArr: [{
          id: 1,
          Name: '上月'
        }, {
          id: 2,
          Name: '本月'
        }, {
          id: 3,
          Name: '今日'
        }, {
          id: 4,
          Name: '昨日'
        }, ],
        hbFlag: false,
        officeFlag: true,
        disMask: false,
        hierarchyTopMask: false,
        gangWei: true,
        textColor: true,
        sortArr: [], //字母排序
        postShow: true, //岗位筛选显示隐藏
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
        hasMask: false,
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
        StatusCountList: [],
        Month: [],
        strCount: [],
        strQTCount: [],
        strQYCount: [],
        strYQYCount: [],
        AddProportion: -1,
        QYProportion: -1,
        monthAdd: -1,
        monthQY: -1,
        yearAdd: -1,
        yearQY: -1,
        xuQianNum: 0,
        qianYueNum: 0,
        yiQianYue: 0,
        CompanyCountTop:0,
        StatusCountTop:0,
        nameArr: [],
        nameGrade:'',    
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
      this.companySignatory()
      this.lineImgFun()
      this.newAddView()
      this.getTable()
      this.getXuQian()
      this.getQianYue()
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
      //累计签约概览
      companySignatory() {
        axios({
          url: this.getHost() + '/Statistics/CompanySignatory',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.huanList = res.data.Data.list
            this.yiQianYue = this.huanList[1].Percentage
            this.nameGrade = this.huanList[1].StatusName, 
            this.StatusCountTop = this.huanList[1].StatusCount
            this.CompanyCountTop = this.huanList[1].CompanyCount
            this.huanData(this.huanList[0].StatusCount, this.huanList[1].StatusCount, this.huanList[2].StatusCount,this.huanList[3].StatusCount)
            this.huanList.forEach(item => {
                this.nameArr.push(item.StatusName)
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
      huanData(num1, num2, num3, num4) {
        // console.log(num1,'num1')已过期
        // console.log(num2,'num2')洽谈中
        // console.log(num3,'num3')签约中
        // console.log(num4,'num4')已签约
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
          color: ['#F6EAD4', '#FAC5B8', '#F3CF9D', '#CDDFEE'],
          series: [{
            name: '公司状态',
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
                  fontSize: '20',
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
                value: num2,
                name: '洽谈中'
              },
              {
                value: num3,
                name: '签约中'
              },
              {
                value: num4,
                name: '已签约'
              },
              {
                value: num1,
                name: '已过期'
              }
            ]
          }]
        })
        let that=this
        myChartBing.on('click', function (params) {
          if (params.value) {
            let index=that.nameArr.indexOf(params.name)
            that.nameGrade = that.huanList[index].StatusName, //评级名称
            that.CompanyCountTop = that.huanList[index].CompanyCount, //总数
            that.yiQianYue = that.huanList[index].Percentage, //占比
            that.StatusCountTop = that.huanList[index].StatusCount
          }
        });
      },
      // 最近半年各状态公司累计折线图
      lineImgFun() {
        axios({
          url: this.getHost() + '/Statistics/CompanySignatoryLine',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.lineData(res.data.Data.Month, res.data.Data.strCount, res.data.Data.strQTCount, res.data.Data.strQYCount,
              res.data.Data.strYQYCount)
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
      lineData(Month, strCount, strQTCount, strQYCount, strYQYCount) {
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
          //   data: ['总共公司数', '名下今年月销额','名下去年月销额', '名下今年月销额']
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
              name: '总共公司数',
              type: 'line',
              // stack: '总量',
              data: strCount,
              // data:[16, 0, 0, 0, 0, 0],
              color: '#8A8D98',
              selected:true
            },
            {
              name: '累计洽谈中',
              type: 'line',
              // stack: '总量',
              data: strQTCount,
              // data:[0, 2, 0, 1, 2, 0],
              color: '#F6EAD4'
            },
            {
              name: '累计签约中',
              type: 'line',
              // stack: '总量',
              data: strQYCount,
              // data:[0, 11, 0, 0, 0, 0],
              color: '#FAC5B8'
            },
            {
              name: '累计已签约',
              type: 'line',
              // stack: '总量',
              data: strYQYCount,
              // data:[0, 0, 0, 0, 0, 0],
              color: '#F3CF9D'
            },
          ]
        })
       



      },
      // 月新增公司－业务员排行
      newAddView() {
        axios({
          url: this.getHost() + '/Statistics/NewAddView',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.AddProportion = res.data.Data.AddProportion
            this.QYProportion = res.data.Data.QYProportion
            this.monthAdd = res.data.Data.monthAdd
            this.monthQY = res.data.Data.monthQY
            this.yearAdd = res.data.Data.yearAdd
            this.yearQY = res.data.Data.yearQY
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
      // 各状态公司新增业务员排行榜
      getTable() {
        axios({
          url: this.getHost() + '/Statistics/UserRankingView',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            IsSort: this.statuIsSortID,
            Time: this.timeID,
            DealerID: this.DealerID
          })
        }).then(res => {
          if(res.data.Status == 1){
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
      //禁止页面滑动
      stop() {
        let mo = function (e) {
          e.preventDefault();
        };
        document.body.style.overflow = 'hidden';
        document.addEventListener("touchmove", mo, false);
      },
      //移除禁止页面滑动
      move() {
        var mo = function (e) {
          e.preventDefault();
        };
        document.body.style.overflow = '';
        document.removeEventListener("touchmove", mo, false);
      },
      fixedTop(num) {
        if (num == 1) {
          this.hierarchyTopMask = true
          this.maskStatus()
        } else if (num == 2) {
          this.hierarchyTopMask = true
          this.maskTime()
        } else if (num == 3) {
          this.disMask = true
          this.hbFlag = false
          this.officeFlag = true
          // this.officeMask()
          // this.stop()
        } else if (num == 4) {
          this.disMask = true
          this.hbFlag = true
          this.officeFlag = false
          // this.stop()
        }
      },
      // 岗位筛选遮罩
      maskStatus() {
        this.hasMask = !this.hasMask
        if (this.hasMask == true) {
          // this.stop()
          if (this.Status.length > 0) {} else {
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
        if (this.leftAllText == this.rightAllText) {
          this.isFoodActive = -1
          this.textColor = true
          this.rightActive = -1
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
            // if (this.isFoodActive.indexOf(y.ID) > -1) {
            //   this.isFoodActive = []
            //   this.textColor = true
            //   this.leftTextColor = true
            //   this.allColor = false
            // }
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
        this.statusSelect = item.Name
        this.DealerID = item.ID
        console.log(this.DealerID, 'this.DealerID111')
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
        // if (item.JobID == 4) {
        //   this.leftAllText = this.quanBu + "业务员"
        // }
        if (item.JobID != 4) {
          this.rightAllText = this.leftAllText
        }

        this.allColor = false
        let positionID = this.positionID.indexOf(item.positionID)
        let rightID = this.rightIdArr.indexOf(item.ID)

        if (this.leftList.length == 0) {
          if (item.JobID != 4) {
            this.leftList.push(item)

          }
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
                if (item.JobID != 4) {
                  this.leftList.push(item)
                }
                this.rightIdArr.push(item.ID)
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
                if (item.JobID != 4) {
                  this.leftList.push(item)
                }
                this.rightIdArr.push(item.ID)
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
              if (item.JobID != 4) {
                this.leftList.push(item)

              }
              this.rightIdArr.push(item.ID)
              this.leftActive = -1
              this.leftTextColor = true
              this.textColor = true
              if (this.positionID.indexOf(item.positionID) < 0) {
                this.positionID.push(item.positionID)
              }
            }
            if (item.positionID == g.positionID && item.ID != g.ID) {
              if (item.JobID != 4) {

                this.leftList.splice(positionID, this.leftList.length - 1 - positionID)
              }
              this.leftActive = -1
              this.leftTextColor = true
              this.textColor = true
              this.positionID.splice(positionID + 1, this.positionID.length - 1 - positionID)
              this.rightIdArr.splice(rightID + 1, this.rightIdArr.length - 1 - rightID)
            }
          })
        }
        if (item.JobID != 4) {
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
        } else {
          this.rightActive = index
          this.textColor = false
        }

        if (item.JobID != 4) {
          this.Status = item.list
        }
        this.sortArr = item.zmlist
        console.log(this.Status)


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
        if (this.leftList.length ==0) {
          this.DealerID = ''
        console.log(this.DealerID,'this.DealerIDthis.DealerID')
        this.hasMask = false
        this.hierarchyTopMask = false
        // this.$refs.scroll.scrollTo(0, 0)
        this.move()
        this.getTable()
          return
        } 
        if(this.rightAllText == this.leftAllText&&this.textColor == true){
          this.DealerID = this.leftList[this.leftList.length - 1].ID
          this.statusSelect == this.leftAllText
        }
        console.log(this.leftList.length)
        
        if (this.leftList.length ==1&&!this.leftList[0].ID) {
          this.DealerID = ''
        } 
       
        this.hasMask = false
        this.hierarchyTopMask = false
        // this.$refs.scroll.scrollTo(0, 0)
        this.move()
        this.getTable()
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
      // 待续签
      officeMask() {
        if (this.officeFlag == false) {
          this.fixedTop(3)
          this.hbFlag = false
        } else {
          this.move()
          this.disMask = false
        }
      },
      officeClick(t, i) {
        this.statuActive = i
        this.statuText = t.Name
        this.statuIsSortID = t.id
        this.disMask = false
        this.move()
        this.getTable()
      },
      timeClick(a, b) {
        this.timeActive = b
        this.timeText = a.Name
        this.timeID = a.id
        this.disMask = false
        this.move()
        this.getTable()
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

      getXuQian() {
        axios({
          url: this.getHost() + '/Statistics/WaitRenewCompanyList',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            page: 1,
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.xuQianNum = res.data.Data.rowCount

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
      waitXuQian() {
        this.$router.push({
          path: '/waitXuQian',

        })
      },
      getQianYue() {
        axios({
          url: this.getHost() + '/Statistics/NoSignatoryCompanyList',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            page: 1,
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.qianYueNum = res.data.Data.rowCount

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
      noQianYue() {
        this.$router.push({
          path: '/noQianYue',

        })
      },
      newSale() {
        sessionStorage.setItem('newSaleTrList', JSON.stringify(this.distTableArr))
        this.$router.push({
          path: '/newSale',

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
  .allListMask {
    z-index: 3000000000 !important;
  }
  .allListMask ul li.active {
    color: #BB9F61
  }

  /* .container {
    padding-top: 20px;
  } */

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
.top .topTips{

  display: block;
  background: #FFF5E7; 
  padding-left: 16px;
}
  .topTips {
    display: inline-block;
    /* background: #FFF5E7; */
    /* padding-left: 16px; */
    color: #D3B488;
    line-height: 34px;
    font-size: 11px;
  }

  .huanTu {
    padding-bottom: 34px;
    background: #fff;
    padding-top: 20px;
  }

  .huanTuButtom>p {
    font-size: 14px;
    color: #303030;
    text-align: center;
    line-height: 24px;
  }

  .huanTuButtom span {
    display: block;
    font-size: 12px;
    color: #B0B0B0;
    text-align: center;
  }

  .huanTuButtom {
    width: 88%;
    margin: 0 auto;
    border-bottom: 2px solid #F5F5F5;
    padding-bottom: 28px;
    margin-top: -12px;
  }

  .lineTips {
    font-size: 10px;
    color: #303030;
    padding-left: 18px;
    background: none;
    margin-bottom: 24px;
  }

  .lineBottom,
  .zhanBiModel {
    display: flex;
    justify-content: space-around;
    border-bottom: 1px solid #F5F5F5;
    background: white;
  }

  .lineBottom {
    padding: 20px 30px 24px 30px;

  }

  .zhanBiModel {
    padding: 22px 10px 24px 10px;
    margin-bottom: 10px;
  }

  .lineBottom p,
  .zhanBiModel p {
    font-size: 12px;
    color: #696969;
    line-height: 24px;
  }

  .zhanBiModel p:nth-child(1) {
    color: #616161;
  }

  .zhanBiModel p:nth-child(3) {
    font-size: 11px;
    text-align: center;
  }

  .zhanBiModel p:nth-child(3) span {
    font-size: 15px;
    font-weight: 550;
  }

  .zhanBiModel b {
    display: block;
    font-size: 15px;
    color: #DDB971;
    text-align: center;
  }

  .lineBottom i,
  .zhanBiModel i {
    width: 2px;
    height: 48px;
    background: #F5F5F5;
  }

  .lineBottom a {
    display: block;
    font-size: 12px;
    color: #AC8F4F;
  }

  .hierarchyTop .filter,
  .hierarchyTopMask .filter {
    padding-top: 12px;
  }

  .hierarchyTopMask .filter {
    margin: 0 auto;
  }

  .shaiXuan {
    display: flex;
    text-align: center;
    width: 22%;
    margin: 0 auto;

  }

  .shaiXuan span {
    display: block;
    color: #464646;
    font-size: 14px;
    margin-right: 8px;

  }

  .shaiXuan img {
    display: block;
    width: 9px;
    height: 6px;
    margin-top: 4px;

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
  .allMask .saleWarp {
    width: 100%;
    height: 60vh;
    background-color: #F5F5F5;
    overflow-y: scroll !important;
    -webkit-overflow-scrolling: touch;
  }

  .saleWarp .letterSort {
    top: 0;
    position: absolute;
  }

  .hierarchyTop .mask {
    display: none;
  }

  .hierarchyTop .filter {
    margin: 0 auto;
  }

  .BingImgI {
    display: block;
    width: 3px;
    height: 11px;
    border-radius: 2px;
    margin-right: 8px;
  }

  .A {
    background: #F6EAD4;

  }

  .A1 {
    background: #FAC5B8;

  }

  .B {
    background: #F3CF9D;

  }

  .B1 {
    background: #CDDFEE;

  }

  .gradeBingImg {
    width: 86%;
    display: flex;
    justify-content: space-around;
    margin: 0 auto;

  }

  .leftColor,
  .rightColor {
    padding-top: 60px;
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

  .xeLineImg {
    margin-top: -30px;
  }

  .lineColor {
    width: 56%;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    flex-wrap: wrap;
    padding-top: 10px;
  }

  .lineColor p {
    display: flex;
    margin-bottom: 10px;
    width: 50%;
  }

  .lineColor i {
    display: block;
    width: 6px;
    height: 6px;
    border-radius: 50%;
    margin-right: 9px;
    margin-top: 2px;
    margin-left: 14px;
  }

  .lineColor .dot1 {
    background: #8A8D98;
  }

  .lineColor .dot2 {
    background: #F6EAD4;
  }

  .lineColor .dot3 {
    background: #FAC5B8;
  }

  .lineColor .dot4 {
    background: #F3CF9D;
  }

  .lineColor span {
    display: block;
    color: #303030;
    font-size: 11px;
  }

  .mask {
    position: fixed;
    height: 54vh;
  }

  .btnCheck {
    display: flex;
    justify-content: space-around;
    padding: 18px 0;
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

  .xtMore {
    text-align: center;
    font-size: 13px;
    color: #696969;
    padding: 18px 0;
  }

  .maskBox {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    z-index: 100000000;
  }

  .hierarchyTop,
  .hierarchyTopMask {
    display: flex;
    height: 36px;
    border-bottom: 1px solid #F5F5F5;
    background: white;
    justify-content: space-around;

  }

  .butCheck {
    display: flex;
    padding: 14px 0;
    background: white;
  }

  .disTopmask {
    width: 100%;
    background: white;
    display: flex;
    padding-bottom: 12px;
  }

  .huanBiMask ul,
  .officeMask ul {
    height: 60vh;
    background: white;
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

  .disTopmask .filter {
    padding-top: 12px;
  }

  .huanBiMask ul li,
  .officeMask ul li {
    font-size: 12px;
    color: black;
    padding: 10px;
    border-bottom: 1px solid #F5F5F5;
  }

  .huanBiMask ul li.xtActive,
  .officeMask ul li.xtActive {
    color: #DDB971;
    font-size: 12px;
  }

  .lineTu {
    margin-bottom: 10px;
    background: white;
  }

  .checkTable {
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
