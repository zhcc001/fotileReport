<!-- 未评级列表 -->
<template>
  <div class="notRating">
    <div class="container">
      <div class="topCheck">
        <div class="titleBox">
          <div class="filters" @click="maskStatus()">
            <div>
              <span class="filterResult" v-if='gangWei' :class="{active:hasMask}">{{statusSelect}}</span>
              <span v-if='!gangWei' :class="{active:hasMask}">{{salesText}}({{salesNum}})</span>
              <img src="./2.png" class="icon" alt="" v-if='hasMask'>
              <img src="./1.png" class="icon" alt="" v-else>
            </div>
          </div>
          <i class="lineI"></i>
          <div class="filters" @click='showJiDu()'>
            <div>
              <span class="filterResult">{{jiduText}}</span>
              <img src="./2.png" class="icon" alt="" v-if='jiDuMask'>
              <img src="./1.png" class="icon" alt="" v-else>
            </div>
          </div>
          <i class="lineI"></i>
          <div class="filters" @click='xiaoEclick()'>
            <div>
              当季销额{{xiaoESort==true?'升序':'降序'}} <img class="timeIcon" src="./timeIcon.png" alt="">
            </div>
          </div>
        </div>
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
        <p class="numText">该列表一共有<span>{{num}}</span>家公司未评级</p>
        <div class="box">
          <div class="scroll-list-wrap" style="background: white;">
            <cube-scroll ref="scroll" :data="trList" :options="options" @pulling-up="onPullingUp" @pulling-down='onPullingDown'>
              <table border="0" cellspacing="0" cellpadding="0" style="border:0;">
                <thead>
                  <tr>
                    <td><span>多选</span></td>
                    <td><span>公司名称</span></td>
                    <td><span>当季销额</span></td>
                    <td><span>年累计销额</span></td>
                    <td><span>经销商</span></td>
                    <td><span> </span></td>
                  </tr>
                </thead>

                <tbody>
                  <tr v-for='(item,index) in trList' :key='index'>
                    <td @click='checkItem(item,index)'><a href="javascript:;" class="round" :class="{active:companyChecks[index]}"><b
                          :class="{active:companyChecks[index]}"></b></a></td>
                    <td><span>{{item.Name}}</span></td>
                    <td><span>{{item.QuarterSalePrice}}</span></td>
                    <td><span>{{item.YearSalePrice}}</span></td>
                    <td><span>{{item.DealerName}}</span></td>
                    <td @click='detailClick(item,index)'><span class="detailLink">></span></td>
                  </tr>

                </tbody>
              </table>
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
      </div>
      <footer>
        <div class="left" @click='checkAll(trList)'>
          <a href="javascript:;" class="round" :class="{active:companyAllBox}"><b :class="{active:companyAllBox}"></b></a>
          <!-- <span>{{allCheck==true?'全选':'取消全选'}}</span> -->
          <span class="content">已选择{{idList.length}}个公司</span>
        </div>
        <div class="rightBtn">
          <button type="button" class="gradeBtn" @click='gradeClick()'>批量评级</button>
        </div>
      </footer>

      <!-- 季度 -->
      <div class="jiDuMask" v-if='jiDuMask'>
        <ul>
          <li v-for='(item,index) in jiDuList' :key='index' :class='{xtActive:index==jiduActive}' @click='jiduCheck(item,index)'>{{item.Name}}</li>
        </ul>
      </div>
      <div class="mask" v-show="hasMask||jiDuMask"></div>

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
    name: 'NotRating',
    data() {
      return {
        jiduText: '',
        IsSort: 1,
        jiduValue: '',
        jiduActive: 3,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        timer: null,
        pageMax: 1,
        pageCount: 1,
        jiDuMask: false,
        jiDuList: [],
        xiaoESort: false,
        showDis: false,
        gangWei: true,
        timeFlag: false,
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
        isFoodActive: [],
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
        allCheck: false,
        trList: [],
        companyChecks: [],
        allItem: false,
        companyAllBox: false,
        idList: [],
        Letter: ['A+', 'A', 'A-', 'B+', 'B', 'B-'],
        liActive: 0,
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
        maskFlag: false,
        onQuarter: '1季度',
        num: -1,
        secondStop: 26,
        rightAllText: '',
        topHight: 0,

      }
    },
    beforeRouteEnter(to, from, next) {
      if (from.name == 'saleStatistics') {
          
        sessionStorage.removeItem('DealerID')
        sessionStorage.removeItem('Quarter')
        sessionStorage.removeItem('IsSort')
        to.meta.isBack = true;
      }
      next();
    },
    activated() {
      if (this.$route.meta.isBack) {
        console.log(this.$route.meta.isBack)
        console.log(1233)
        this.jiduText = '',
          this.IsSort = 1,
          this.jiduValue = '',
          this.jiduActive = 3,
          this.pullUpLoad = false,
          this.pullUpLoadThreshold = 0,
          this.pullUpLoadMoreTxt = '--加载更多--',
          this.pullUpLoadNoMoreTxt = '--已经到底部了--',
          this.timer = null,
          this.pageMax = 1,
          this.pageCount = 1,
          this.jiDuMask = false,
          this.jiDuList = [],
          this.xiaoESort = false,
          this.showDis = false,
          this.gangWei = true,
          this.timeFlag = false,
          this.textColor = true,
          this.sortArr = [], //字母排序
          // postShow= true, //岗位筛选显示隐藏
          this.moreNum = 0, //高级筛选数量现实
          this.salesNum = 0, //岗位筛选业务员数量显示
          this.salesNumFlag = false,
          this.salesmanFlag = true, //全部业务员
          this.distribFlag = true, //全部经销商
          this.allClassify = true, //全部公司分类
          this.allStatus = true, //全部公司状态
          this.isStyle = [],
          this.isStatus = [],
          this.salesArr = [],
          this.isFoodActive = [],
          this.salesMask = false, //业务员列表
          this.salesmanLists = [],
          this.Status = [], //经销商
          // 公司类型列表
          this.Style = [],
          // 业务员
          this.Person = [],
          this.hasMask = false,
          this.leftActive = -1,
          this.rightActive = -1,
          this.statusSelect = "岗位",
          this.personHasActive = 0,
          this.personSelect = "高级筛选",
          this.styleHasActive = 0,
          this.checkBoxs = [],
          this.checkAllBox = false,
          this.idList = [],
          this.active = false,
          this.allStateActive = false,
          this.checkFlag = false,
          this.SaleID = '', //业务员ID
          this.DealerID = '', //经销商ID
          this.flag = false,
          this.showDistrib = true, //经销商显示隐藏
          this.topHight = 0,
          this.zmArr = [],
          this.nameData = [],
          this.salesText = '业务员',
          this.leftList = [],
          this.rightList = [],
          this.allText = '',
          this.linShiArr = [],
          this.positionID = [],
          this.leftAllText = '',
          this.rightIdArr = [],
          this.checkBox = false,
          this.clickFlag = true,
          this.allColor = true,
          this.boxFlag = false,
          this.startArr = [],
          this.zmFlag = -1,
          this.allClick = true,
          this.quanBu = '全部',
          this.leftTextColor = true,
          this.JobID = [],
          this.secondStop = 26,
          this.rightAllText = '',
          this.allCheck = false,
          this.trList = [],
          this.companyChecks = [],
          this.allItem = false,
          this.companyAllBox = false,
          this.idList = [],
          this.Letter = ['A+', 'A', 'A-', 'B+', 'B', 'B-'],
          this.liActive = 0,
          this.jilib = [{
            BucklePoint: ""
          }],
          this.jiLiA = {
            srcAdd: require('./icon3.png'),
            srcDel: require('./del.png'),
            BucklePoint: '',
          },
          this.jiLiArr = [{
            srcAdd: require('./icon3.png'),
            srcDel: require('./del.png'),
            BucklePoint: '',

          }],
          this.maskFlag = false,
          this.onQuarter = '1季度',
          this.num = -1
        // this.trList=[]
        // this.DealerID=JSON.parse(sessionStorage.getItem('DealerID'))
        // this.jiduValue=JSON.parse(sessionStorage.getItem('jiduValue'))
        // this.IsSort=JSON.parse(sessionStorage.getItem('IsSort'))
        // DealerID:this.DealerID,
        //   Quarter:this.jiduValue,
        //   IsSort:this.IsSort,

        // this.getTrList()
        this.quarterList()

      } else {
        console.log(222)
        this.trList = []
        this.companyChecks = []
        this.idList = []
        this.companyAllBox = false
        this.pageCount = 1
        this.DealerID = JSON.parse(sessionStorage.getItem('DealerID'))
        this.jiduValue = JSON.parse(sessionStorage.getItem('jiduValue'))
        this.IsSort = JSON.parse(sessionStorage.getItem('IsSort'))
        // this.getTrList()
        this.quarterList()
        this.move()
      }
      // 恢复成默认的false，避免isBack一直是true，导致下次无法获取数据
      this.$route.meta.isBack = false


    },
    created() {
      document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
      this.quarterList()
      this.move()
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
      saleOptions() {
        return {
          pullingDown: false,
          beforePulldown: true,
          isPullingDown: false,
          pulldown: false,
          pullup: false
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
    methods: {
      onPullingDown() {
        // 下拉刷新数据

        setTimeout(() => {
          this.pageCount = 1
          this.trList = []
          this.getTrList()
          this.$refs.scroll.scrollTo(0, this.secondStop, 100)
        }, 1000)
      },
      onPullingUp() {
        // 更新数据
        if (this.pageMax >= this.pageCount) {
          clearTimeout(this.timer)
          this.timer = setTimeout(() => {
            this.getTrList()
          }, 1000)
        } else {
          // 增加过渡时间，解决多次触发bug
          this.$refs.scroll.forceUpdate()

        }
      },
      getTrList() {
        axios({
          url: this.getHost() + '/Statistics/CompanyNotRateList',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            page: this.pageCount,
            DealerID: this.DealerID,
            Quarter: this.jiduValue,
            IsSort: this.IsSort,

          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.num = res.data.Data.rowCount
            this.pageMax = res.data.Data.pageCount
            this.pageCount++
            this.trList = this.trList.concat(res.data.Data.list)
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
      // 岗位筛选遮罩
      maskStatus() {
        this.jiDuMask = false
        this.hasMask = !this.hasMask
        if (this.hasMask == true) {
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
        } else {
          this.move()
        }
      },
      //右边全部按钮点击
      rightAllClick() {
        console.log(this.leftList,'this.leftList')
        console.log(this.leftList.length,'this.leftList.length')
        
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
        if (this.leftAllText == this.rightAllText) {
          return false
        }
        if (this.leftList.length == 1&&this.leftList[0].ID) {
          this.DealerID = ''
          // return false
        } 
        if (this.leftList.length == 2&&this.leftList[1].ID)  {
          console.log('console.log')
          this.DealerID = this.leftList[this.leftList.length - 1].ID
          // return false

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
          console.log(this.DealerID,'this.DealerID333')
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
      checkSales(item) {
        this.topHight = event.currentTarget.offsetTop
        this.salesmanFlag = false
        let id = item.ID
        let indexId = this.isFoodActive.indexOf(id);
        if (indexId < 0) {
          this.isFoodActive.push(id);
          this.salesNum = this.isFoodActive.length
          this.SaleID = this.isFoodActive.join(',')
          this.rightAllText = "业务员" + "(" + this.salesNum + ")"
        } else {
          this.isFoodActive.splice(indexId, 1);
          this.salesNum = this.isFoodActive.length
          this.rightAllText = "业务员" + "(" + this.salesNum + ")"
          if (this.salesNum == 0) {
            this.rightAllText = this.quanBu + "业务员"
            this.leftAllText = this.quanBu + "业务员"
            this.textColor = true
          }
          this.SaleID = this.isFoodActive.join(',')
        }
        if (this.isFoodActive.indexOf(item.ID) > -1) {
          this.textColor = false
          this.leftTextColor = true
        }
      },
      // 业务员列表重置
      resetSales() {
        this.isFoodActive = []
        this.SaleID = ''
      },
      //确认业务员
      sureSales() {
        if (this.leftList.length ==0) {
          this.DealerID = ''
        console.log(this.DealerID,'this.DealerIDthis.DealerID')
        this.pageCount = 0
        this.getTrList()
        this.hasMask = false
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
       
        this.pageCount = 0
        this.getTrList()
        this.hasMask = false
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

      //禁止页面滑动
      //  禁止页面滑动
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

      // 销额升序降序
      xiaoEclick() {
        this.xiaoESort = !this.xiaoESort
        if (this.xiaoESort == true) {
          this.IsSort = 2
        } else {
          this.IsSort = 1
        }
        this.companyAllBox = false
        this.idList = []
        this.companyChecks = []
        this.pageCount = 0
        this.trList = []
        this.getTrList()
      },
      // 批量评级全选
      checkAll(List) {
        if (this.companyAllBox) {
          this.companyAllBox = false
          this.idList = this.idList.splice()
          for (let i = 0; i < List.length; i++) {
            this.companyChecks[i] = false
          }
          this.companyChecks = this.companyChecks.slice()
        } else {
          this.companyAllBox = true
          for (let i = 0; i < List.length; i++) {
            this.companyChecks[i] = true
            this.idList.push(List[i].ID)
          }
          this.companyChecks = this.companyChecks.slice()
          this.idList = this.idList.slice()
        }
      },
      checkItem(t, d) {
        let id = t.ID
        if (this.companyChecks[d]) {
          this.companyChecks[d] = false
          this.companyAllBox = false
          for (let i = 0; i < this.idList.length; i++) {
            if (this.idList[i] == id) {
              this.idList.splice(i, 1)
            }
          }
          for (let i = 0; i < this.companyChecks.length; i++) {
            if (this.companyChecks[i]) {
              this.companyAllBox = true
            }
          }
        } else {
          this.companyChecks[d] = true
          this.idList.push(id)
          this.companyAllBox = true
        }
        this.idList = this.idList.slice()
        this.companyChecks = this.companyChecks.slice()

      },

      showJiDu() {
        this.hasMask = false
        this.jiDuMask = !this.jiDuMask
        if (this.jiDuMask == true) {
          // this.stop()
        } else {
          this.move()
        }
      },
      quarterList() {
        axios({
          url: this.getHost() + '/Statistics/QuarterSoucre',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.jiDuList = res.data.Data
            this.jiduValue = this.jiDuList[3].Value
            this.jiduText = this.jiDuList[3].Name
            this.getTrList()
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
      jiduCheck(a, b) {
        this.jiduText = a.Name,
          this.companyAllBox = false
        this.idList = []
        this.companyChecks = []
        this.jiduActive = b
        this.jiduValue = a.Value
        this.jiDuMask = false
        this.pageCount = 0
        this.trList = []
        this.getTrList()
      },
      // 进入详情页
      detailClick(item, index) {
        this.$router.push({
          path: '/companyDetail',
          query: {
            id: item.ID
          }
        })
      },
      //批量评级
      gradeClick() {
        if (this.idList.length > 0) {
          console.log(this.DealerID)
          if (this.DealerID == '') {
            sessionStorage.setItem('DealerID', JSON.stringify(''))
          } else {
            sessionStorage.setItem('DealerID', JSON.stringify(this.DealerID))
          }
          sessionStorage.setItem('idList', JSON.stringify(this.idList))
          sessionStorage.setItem('Quarter', JSON.stringify(this.jiduValue))
          sessionStorage.setItem('IsSort', JSON.stringify(this.IsSort))
          // DealerID:this.DealerID,
          //   Quarter:this.jiduValue,
          //   IsSort:this.IsSort,
          this.$router.push({
            path: '/modifyGrade',
            query: {
              style: 2,
              Quarter: parseInt(this.jiduValue.charAt(this.jiduValue.length - 1)),
              Year: parseInt(this.jiduValue.substring(0, 4))
            }
          })
        } else {
          this.getToast("请选择需要评级的公司", 'warn')
        }

      },
    }
  }

</script>
<style scoped>
  @import '../../common/filterH.css';

  .box .scroll-list-wrap {
    height: calc(110vh - 210px);
  }

  .notRating {
    padding-top: 10px;
  }

  .container {
    background: white;
  }

  .checkBtn {
    display: block;
    background: #F5F5F5;
    border-radius: 14px;
    padding: 0 10px;
    height: 26px;
    line-height: 26px;
    font-size: 11px;
    color: #5F5F5F;
  }

  .titleBox {
    padding: 15px 0;
    display: flex;
    justify-content: space-around;
    border-bottom: 1px solid #F5F5F5;
  }

  .filters {
    display: flex;
    border-radius: 4px;
  }

  .filters span {
    font-size: 14px;
    color: #464646;
    margin-right: 10px;
  }

  .filters>div {
    margin: 0 auto;
    color: #666;
  }

  .filters .filterResult {
    color: #666;
    padding-left: 5px;
    box-sizing: border-box;
  }

  .filters .icon {
    float: right;
    display: block;
    width: 12px;
    height: 6px;
    margin-top: 4px;
  }

  .branchOffice .icon {
    float: right;
    display: block;
    width: 12px;
    height: 6px;
    margin-top: 10px;
    margin-left: 6px;
  }

  .saleWarp .letterSort {
    top: 0;
    position: absolute;
  }

  .numText {
    font-size: 14px;
    color: #303030;
    padding: 14px 0 14px 16px;
  }

  .numText span {
    font-size: 16px;
    font-weight: 550;
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
    padding: 12px 0;

  }

  table tr td span {
    line-height: 18px;

  }

  table tr td span.detailLink {
    color: #BCBCBC;
  }

  table tbody tr:nth-child(even) {
    background: #F5F5F5;
  }

  .round {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 0 auto;
    width: 16px;
    height: 16px;
    border-radius: 10px;
    border: 1px solid #ccc;
    margin-right: 15px;
  }

  .round.active {
    border: 1px solid #E2C78F;
  }

  .round b {
    display: flex;
    width: 14px;
    height: 14px;
    border-radius: 8px;
    background-color: #ccc;
  }

  .round b.active {
    background-color: #E2C78F;
  }

  footer {
    position: fixed;
    bottom: 0;
    left: 0;
    width: 100%;
    height: 58px;
    background: #3F434F;
    z-index: 100;
  }

  .left {
    width: 58%;
    display: flex;
  }

  .left .round {
    margin: 20px 14px 20px 40px;
  }

  .left span {
    font-size: 16px;
    color: white;
    line-height: 58px;
  }

  .rightBtn {
    position: absolute;
    right: 0;
    height: 100%;
    width: 33%;
    top: 0;
  }

  .rightBtn button {
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
    display: block;
    height: 40px;
    padding: 0 28px;
    background: linear-gradient(45deg, rgba(226, 199, 143, 1), rgba(213, 174, 97, 1));
    box-shadow: 1px 3px 7px 1px rgba(223, 174, 72, 0.2);
    border-radius: 4px;
    font-size: 14px;
    color: white;
  }

  .topCheck {
    position: relative;
  }

  .timeIcon {
    display: block;
    width: 11px;
    height: 12px;
    float: right;
    margin-left: 4px;
  }

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
    max-height: 76vh;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;

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

  .nullBox {
    display: flex;
    position: relative;
    margin-bottom: 6px;
  }

  .allListMask ul li.active {
    color: #BB9F61
  }

  .officeMask ul li {
    font-size: 12px;
    color: black;
    padding: 10px;
    border-top: 1px solid #F5F5F5;
  }

  .officeMask ul li.xtActive {
    color: #DDB971;
    font-size: 12px;
  }

  .jiDuMask {
    width: 100%;
    position: absolute;
    top: 62px;
    z-index: 105;
  }

  .jiDuMask ul {
    height: 60vh;
    background: white;
  }

  .lineI {
    display: block;
    width: 2px;
    height: 16px;
    background: #F5F5F5;
  }

  .jiDuMask ul li {
    font-size: 12px;
    color: black;
    padding: 10px;
    border-bottom: 1px solid #F5F5F5;
  }

  .jiDuMask ul li.xtActive {
    color: #DDB971;
    font-size: 12px;
  }

</style>
