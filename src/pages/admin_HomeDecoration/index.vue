<template>

  <!-- 家装公司管理 -->
  <div class="HomeDecoration companyTop" :class="{active:Mask}">
    <!-- 上部 -->
    <div class="top">
      <div class="topBtn">
        <button type="button">洽谈中
          <span>{{count.Negotiation}}</span>
        </button>
        <button type="button">签约中
          <span>{{count.Progressive}}</span>
        </button>
        <button type="button">已签约
          <span>{{count.Complete}}</span>
        </button>
        <button type="button">已过期
          <span>{{count.Expired}}</span>
        </button>

      </div>
      <div class="search">
        <input type="text" placeholder="请搜索公司名称关键词" v-model="keyword">
        <span @click="getList(1)">搜索</span>
      </div>
      <div class="FilterConditions ">
        <div class="filter" @click="maskStatus(0)" v-if='postShow'>
          <div>
            <span class="filterResult" v-if='gangWei' :class="{active:hasMask[0]}">{{statusSelect}}</span>
            <span v-if='!gangWei' :class="{active:hasMask[0]}">{{salesText}}({{salesNum}})</span>
            <img src="./2.png" class="icon" alt="" v-if='hasMask[0]'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
        <i v-if='postShow'></i>
        <div class="filter" @click="maskStatus(2)">
          <div>
            <span class="filterResult" :class="{active:hasMask[2]}">{{personSelect}}</span>
            <span v-if='moreNum!=0' :class="{active:hasMask[2]}">({{moreNum}})</span>
            <img src="./2.png" class="icon" alt="" v-if='hasMask[2]'>
            <img src="./1.png" class="icon" alt="" v-else>
          </div>
        </div>
        <div class="mask" v-show="hasMask[0]"></div>
        <div class="mask" v-show="hasMask[2]"></div>
      </div>

      <div class="allMask">


        <div class="allListMask" v-show="hasMask[0]">

          <div>
            <div class="allListMask" v-show="hasMask[0]">
              <div>
                <div class="leftUl  widthUL grayF9">
                  <ul class="leftList" v-show='showDistrib'>
                    <li v-for='(item,index) in leftList' @click='leftClick(item,index)' :class='{active:index==leftActive}'>{{item.Name}}</li>
                    <li  :class='{active:leftTextColor}' v-html='rightAllText' @click='allClick&&leftAllClick()'></li>
                  </ul>
                </div>
                <!-- 业务员列表 -->
                <div class="scroll-list-wrap saleWarp" v-show="showDistrib">
                  <cube-scroll ref="scrollSale" :data="Status" :options="saleOptions">
                    <ul class="rightList white" v-show='showDistrib'>
                      <li @click='rightAllClick()' :class='{active:textColor}' v-html='leftAllText' ></li>
                      <li v-for="(item,index) in Status" :key='index' :dataZM='item.ZM' @click='clickFlag&&index!=rightActive&&item.list.length>0&&rightClick(item,index)'
                        :class='{active:index==rightActive}'>
                        <span>{{item.Name}}</span>
                        <div class="checkIcon" @click='checkSales(item)' v-if='checkBox'>
                          <i class="emptyBox" :class="{emptyBox:true,checkBoxIcon:isFoodActive.indexOf(item.ID)>-1||boxFlag}"></i>
                        </div>
                      </li>
                    </ul>
                  </cube-scroll>
                </div>
              </div>
              <div class="stateBtn">
                <button type="button" class="gray" @click='rightAllClick()'>重 置</button>
                <button type="button" class="yellow" @click='sureSales()'>确 认</button>
              </div>
            </div>
          </div>

        </div>
        <!-- 高级筛选 -->
        <div class="stateContent" v-show="hasMask[2]">
          <div class="stateUl">
            <!-- 全部状态 -->
            <h3>全部状态</h3>
            <ul class="allState">
              <li :class='{active:allStatus}' @click='checkAllStatus()'>全部</li>
              <li v-for="(item,index) in allStateText" :key="index" :class="{active:isStatus.indexOf(item.ID)>-1}"
                @click="stateCheck(item)">{{item.Name}}</li>
            </ul>
            <!-- 公司分类 -->
            <h3>公司分类</h3>
            <ul class="companyList">
              <li :class='{active:allClassify}' @click='checkClassify()'>全部分类</li>
              <li v-for="(item,index) in Style" :key="index" :class="{active:isStyle.indexOf(item.ID)>-1}" @click="companyCheck(item)">{{item.Name}}</li>
            </ul>
          </div>
          <div class="stateBtn">
            <button type="button" class="gray" @click='resetState(allStateText)'>重 置</button>
            <button type="button" class="yellow" @click='sureState()'>确 认</button>
          </div>
        </div>
      </div>
      <div class="letterSort" v-if="hasMask[0]&&sortArr.length>0">
        <p v-for='(item,index) in sortArr' @click='namePosition(item,index)' :class='{active:index==zmFlag}'>{{item}}</p>
      </div>
    </div>
    <div class="listWrap">
      <div class="scroll-list-wrap">
        <cube-scroll ref="scroll" :data="List" :options="options" @pulling-up="onPullingUp" @pulling-down='onPullingDown'>
          <!-- 公司列表 -->
          <div class="comList">
            <focusList :list="List" id="list" v-if="!admin" :Action="active"></focusList>
            <empty v-if='emptyFlag'></empty>
            <div>
              <div class="contentList" v-for="(item,index) in List" :key="index" v-if="admin" @click="jump(item.ID)">
                <div class="contentListTop">
                  <p class="firstLine">
                    <span :class="{qiaTan:item.Status==1,qianYue:item.Status==2,qianYued:item.Status==3,fangQi:item.Status==-1,xinJian:item.Status==0,guoQi:item.Status==-3}">{{item.StatusName}}</span>
                    <span v-if="item.Status==1||item.Status==2">剩余保护期:{{item.EndDate}}天</span>
                    <span v-if="item.Status==3||item.Status==-3">合同剩余时间:{{item.EndTime}}天</span>
                  </p>
                  <p class="twoLine">
                    <a href="javascript:;" class="round" :class="{'active':checkBoxs[index]}" @click.stop="check(index,item.ID)"><b
                        :class="{'active':checkBoxs[index]}"></b></a>
                    <!-- 公司评级 -->
                    <a href="javascript:;" class="bigIcon" v-if='item.Rate!=null'><span class="smallIcon"><b>{{item.Rate}}</b></span></a>
                    <a href="javascript:;" class="name">{{item.Name}}</a>
                  </p>
                  <p class="downLine"><span class="zuZhi">{{item.SalesManName?item.SalesManName:'暂无业务员'}}</span><b>({{item.Organization?item.Organization:' '}})</b></span><span
                      class="address">{{item.Address}}</span></p>
                  <i v-if="item.IsEmphasis"></i>
                </div>
                <div class="contentListBottom">
                  <i v-if="!item.CreateDate==''">{{item.CreateDate}}</i>
                  <p v-if="!item.CreateDate==''">{{item.Content}}</p>
                  <b v-if="!item.CreateDate==''">{{item.UserName}}</b>
                  <i v-if="item.CreateDate==''">暂无跟单消息</i>
                </div>
              </div>
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
    </div>

    <div class="bottom" v-if="admin">
      <span class="round" :class="{active:checkAllBox}" @click="checkAll(List)"><b :class="{active:checkAllBox}"></b></span>
      <span class="content">已选择{{idList.length}}个家装公司</span>
      <span class="btn" @click="distribution">分配业务员</span>
    </div>
  </div>
</template>

<script>
  import qs from 'qs'
  import axios from "axios";
  import focusList from "../../components/focusList";
  import companyFilter from '../../components/companyFilter';
  import empty from "../../components/empty"
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  export default {
    name: 'HomeDecoration',
    data() {
      return {
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
        isFoodActive: [],
        salesMask: false, //业务员列表
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
        pageCount: 1,
        page: 1,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        admin: false,
        keyword: '',
        StatusID: '',
        TypeID: '',
        SaleID: '',
        count: {
          Negotiation: '',
          Progressive: '',
          Complete: ''
        },
        List: [1, 1, 1, 1],
        emptyFlag: false,
        Mask: false,
        // 公司状态列表
        salesmanLists: [],
        Status: [], //经销商
        // 公司类型列表
        Style: [],
        // 业务员
        Person: [],
        hasMask: [false, false, false],
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
        rightAllText:'',
        topHight: 0,
      }
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
    components: {
      focusList,
      empty,
      companyFilter
    },
    created() {
      this.getStyleList()
      this.getPersonList()
      this.getList()
      this.getTotalData()
      if (this.AccessId == -1 || this.AccessId == 4) {
        this.admin = true
      }
      if (this.AccessId == 5) {
        this.postShow = false
        this.active = true
      }
    },
    // mounted(){
    // },
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
          this.page=1
          this.getList(this.page)
          this.$refs.scroll.scrollTo(0, this.secondStop, 100)
        }, 1000)
      },
      distribution() {
        if (this.idList.length == 0) {
          this.getToast("请选择要分配的公司", 'warn')
        } else {
          localStorage.setItem("CompanyID", JSON.stringify(this.idList))
          this.$router.push({
            path: '/SalesmanList',
            query: {
              style: 1
            }
          })
        }
      },
      check(index, id) {
        if (this.checkBoxs[index]) {
          this.checkBoxs[index] = false
          this.checkAllBox = false
          for (let i = 0; i < this.idList.length; i++) {
            if (this.idList[i] == id) {
              this.idList.splice(i,1)
            }
          }
          for (let i = 0; i < this.checkBoxs.length; i++) {
            if (this.checkBoxs[i]) {
              this.checkAllBox = true
            }
          }
        } else {
          this.checkBoxs[index] = true
          this.idList.push(id)
          this.checkAllBox = true
        }
        this.idList = this.idList.slice()
        this.checkBoxs = this.checkBoxs.slice()
      },
      checkAll(List) {
        if (this.checkAllBox) {
          this.checkAllBox = false
          this.idList = this.idList.splice()
          for (let i = 0; i < List.length; i++) {
            this.checkBoxs[i] = false
          }
          this.checkBoxs = this.checkBoxs.slice()
        } else {
          this.checkAllBox = true
          for (let i = 0; i < List.length; i++) {
            this.checkBoxs[i] = true
            this.idList.push(List[i].ID)
          }
          this.checkBoxs = this.checkBoxs.slice()
          this.idList = this.idList.slice()
        }
      },
      jump(num) {
        this.$router.push({
          path: '/companyDetail',
          query: {
            id: num
          }
        })
      },
      getStyleList() {
        this.axiosloading()
        axios({
            url: this.getHost() + '/Notice/CompanyList',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.Style = res.data.Data.list
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
      getPersonList() {
        this.axiosloading()
        axios({
            url: this.getHost() + '/Company/SaleList',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
              Keyword: ''
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.salesmanLists = res.data.Data.list
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
      getList(page) {
        this.page = page || 1
        this.axiosloading()
        axios({
            url: this.getHost() + '/Company/CompanyList',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
              keyword: this.keyword,
              StatusID: this.StatusID, //	跟进状态ID
              TypeID: this.TypeID, //	公司类型ID
              SaleID: this.SaleID, //业务员ID
              DealerID: this.DealerID, //经销商ID
              page: page || 1
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.pageCount = res.data.Data.pageCount
              this.count.Negotiation = res.data.Data.Negotiation
              this.count.Progressive = res.data.Data.Progressive
              this.count.Complete = res.data.Data.Complete
              this.count.Expired = res.data.Data.Expired
              if (this.page == 1) {
                this.List = res.data.Data.list
              } else {
                if (this.List.length > 0 && this.page > 1) {
                  // 如果有新数据
                  // let newPage = _foods.slice(0, 5)
                  this.List = this.List.concat(res.data.Data.list)
                }
              }
              this.page++
              if (this.List.length == 0) {
                this.emptyFlag = true
              } else {
                this.emptyFlag = false
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
      getTotalData() {
        axios({
            url: this.getHost() + '/Company/Count',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.count = res.data.Data
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
      // 岗位筛选遮罩
      maskStatus(index) {
        if (this.hasMask[index] == true) {
          this.hasMask[index] = false
        } else {
          this.hasMask = [false, false, false]
          if (this.Status.length > 0) {
          } else {
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
            if (index == 0) {
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
                      if(t.JobID==5){
                        this.checkBox=true
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
                  }
                })
            }
          }
          this.hasMask[index] = true
        }
        this.Mask = this.hasMask[index] ? true : false
      },
      //右边全部按钮点击
      rightAllClick() {
        console.log(123)
        if (this.leftAllText == this.rightAllText) {
          return false
        }
        this.isFoodActive = []
        this.SaleID = ''

        if (this.salesNum != 0 && this.leftAllText == "全部业务员") {
          this.isFoodActive = []
          this.textColor = true
          this.rightAllText = this.leftAllText
          // this.statusSelect=this.leftAllText
          this.salesNum = 0
          return false
        }
        if (this.salesNum == 0 && this.leftAllText == "全部业务员") {
          this.isFoodActive = []

          this.textColor = true
          this.salesNum = 0
          // this.statusSelect=this.leftAllText
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
          this.isFoodActive = []
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
        this.isFoodActive = []
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
        if (this.salesNum > 0) {
          this.SaleID = this.isFoodActive.join(',')
          this.statusSelect = this.rightAllText
        } else {
          this.statusSelect = this.leftAllText
        }
        // this.statusSelect = this.quanBu + this.allText
        if(this.leftList.length == 0){
          this.DealerID=''
        }else{
          this.DealerID = this.leftList[this.leftList.length - 1].ID

        }
        this.page = 1
        this.getList()
        this.hasMask = [false, false, false]
        this.$refs.scroll.scrollTo(0, 0)
      },
      //点击全部分类
      checkAllStatus() {
        this.allStatus = true
        this.isStatus = []
        this.StatusID = ''
      },
      stateCheck(item) {
        this.allStatus = false
        let id = item.ID
        let indexId = this.isStatus.indexOf(id);
        if (indexId < 0) {
          this.isStatus.push(id);
          this.StatusID = this.isStatus.join(',')
        } else{
          if(this.isStatus.length>1){
            this.isStatus.splice(indexId,1)
          this.StatusID = this.isStatus.join(',')
          }
          
        }
        
      },
      //点击公司分类
      checkClassify() {
        this.allClassify = true
        this.isStyle = []
        this.TypeID = ''
      },
      companyCheck(item) {
        this.allClassify = false
        let id = item.ID
        let indexId = this.isStyle.indexOf(id);
        if (indexId < 0) {
          this.isStyle.push(id);
          this.TypeID = this.isStyle.join(',')
        } else{
          if(this.isStyle.length>1){
            this.isStyle.splice(indexId,1)
            this.TypeID = this.isStyle.join(',')
          }
        }
      },
      //高级筛选点击重置
      resetState() {
        this.isStyle = []
        this.isStatus = []
        this.TypeID = ''
        this.StatusID = ''
        this.allClassify = true
        this.allStatus = true
      },
      //高级筛选点击确认
      sureState() {
        this.moreNum = this.isStatus.length + this.isStyle.length
        this.page = 1
        this.getList()
        this.hasMask = [false, false, false]
        this.$refs.scroll.scrollTo(0, 0)
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
     
    },
  }

</script>
<style scoped>
  @import '../../common/filterH.css';
  @import '../../common/focusList.css';

  .allMask .saleWarp {
    width: 100%;
    height: 60vh;
    background-color: #F5F5F5;
    overflow-y: scroll !important;
    -webkit-overflow-scrolling: touch;
  }

  .saleWarp .cube-scroll-content {
    z-index: 800 !important;
  }

  .listWrap .scroll-list-wrap {
    height: calc(100vh - 210px);
  }

  .contentListTop {
    margin-left: 0px;
    padding-bottom: 10px
  }

  .contentListBottom {
    margin-left: 0px;
  }

  .firstLine span:nth-child(1) {
    margin-left: 30px;
  }

  .twoLine .round {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 16px;
    height: 16px;
    border-radius: 10px;
    border: 1px solid #ccc;
    margin-right: 15px;
  }

  .twoLine .round.active {
    border: 1px solid #E2C78F;
  }

  .twoLine .round b {
    display: flex;
    width: 14px;
    height: 14px;
    border-radius: 8px;
    background-color: #ccc;
  }

  .twoLine .round b.active {
    background-color: #E2C78F;
  }

  .HomeDecoration {
    width: 100%;
    overflow: hidden;
  }

  .top {
    width: 100%;
    overflow: hidden;
    margin: 0 auto;
    padding-top: 20px;
    background: white;
    margin-bottom: 12px;
  }

  .top .topBtn {
    width: 92%;
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #F0F0F0;
    padding-bottom: 14px;
    margin: 0 auto;
    margin-bottom: 12px;
  }

  .top .topBtn button {
    width: 24%;
    height: 38px;
    border-radius: 6px;
    font-size: 14px;
    line-height: 38px;
    text-align: center;
  }

  .top .topBtn button:nth-child(1) {
    background: #F6EAD4;
    color: #BB9F61;
  }

  .top .topBtn button:nth-child(2) {
    background: #FFDED6;
    color: #F26F53;
  }

  .top .topBtn button:nth-child(3) {
    background: #F3CF9D;
    color: #FFFFFF;
  }

  .top .topBtn button:nth-child(4) {
    background: #CDDFEE;
    color: #FFFFFF;
  }

  .top .search {
    width: 92%;
    height: 44px;
    margin: 0 auto;
    position: relative;
  }

  .top .search input {
    width: 80%;
    height: 96%;
    border: none;
    background: none;
    padding-left: 14px;
    border: 1px solid rgba(208, 208, 208, 1);
    border-radius: 4px;
  }

  .top .search span {
    display: block;
    width: 18%;
    height: 44px;
    background: rgba(182, 160, 121, 1);
    border-radius: 0px 4px 4px 0px;
    position: absolute;
    right: 0;
    top: 0;
    font-size: 14px;
    font-family: PingFangSC-Regular;
    color: rgba(255, 255, 255, 1);
    line-height: 44px;
    text-align: center;
  }

  .top .classList {
    width: 92%;
    overflow: hidden;
    margin: 0 auto;
    padding-top: 22px;
  }

  .top .classList ul {
    width: 100%;
    overflow: hidden;
    display: flex;
    justify-content: space-between
  }

  .top .classList ul li {
    width: 30%;
    height: 24px;
    overflow: hidden;
    position: relative;
  }

  .top .classList ul li p {
    display: block;
    width: 74%;
    height: 22px;
    border: 1px solid rgba(182, 160, 121, 1);
    ;
    border-radius: 4px;
    font-size: 12px;
    line-height: 22px;
    padding-left: 6px;
  }

  .top .classList ul li span {
    width: 20%;
    height: 24px;
    position: absolute;
    top: 0;
    right: 2px;
    background: rgba(182, 160, 121, 1);
    border-radius: 0px 4px 4px 0px;
  }

  .top .classList ul li span::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    margin: auto;
    width: 0;
    height: 0;
    border-left: 5px solid transparent;
    border-right: 5px solid transparent;
    border-top: 5px solid white;
  }

  .companyList {
    width: 88%;
    overflow: hidden;
    margin: 0 auto;
  }

  .companyList ul {
    width: 100%;
    overflow: hidden;
  }

  .companyList ul li {
    width: 100%;
    overflow: hidden;
    height: 116px;
    background: rgba(255, 255, 255, 1);
    border-radius: 8px;
    margin-bottom: 15px;
  }

  .comList {
    width: 92%;
    margin: 0 auto;
  }

  /* 底部 */
  .bottom {
    width: 100%;
    height: 55px;
    background: #3F434F;
    display: flex;
    justify-content: center;
    align-items: center;
    padding-left: 30px;
    padding-right: 15px;
    box-sizing: border-box;
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 99;
  }

  .bottom .content {
    flex: 1;
    width: 0;
    font-size: 16px;
    color: #fff;
  }

  .bottom .btn {
    width: 110px;
    height: 40px;
    background-image: linear-gradient(to right, #E2C78F, #D5AE61);
    border-radius: 4px;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #fff;
  }

  .bottom .round {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 16px;
    height: 16px;
    border-radius: 10px;
    border: 1px solid #ccc;
    margin-right: 15px;
  }

  .bottom .round.active {
    border: 1px solid #E2C78F;
  }

  .bottom .round b {
    display: flex;
    width: 14px;
    height: 14px;
    border-radius: 8px;
    background-color: #ccc;
  }

  .bottom .round b.active {
    background-color: #E2C78F;
  }

  .contentListTop i {
    position: absolute;
    top: 0;
    left: 0;
    width: 28px;
    height: 28px;
    border-radius: 6px 3px;
  }

</style>
