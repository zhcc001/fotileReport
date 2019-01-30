<template>
  <!-- 公司检索 -->
  <div class="CompanyRetrieval">
    <div class="retrievaList">
      <div class="top">
        <div class="searchResult">
          <!-- <input type="text" placeholder="请输入家装公司" v-model="keyword">
          <span @click="getList">搜索</span> -->
          <div class="left">
            <ul>
              <li>
                <p>关键词 :</p>
                <p>{{keyword}}</p>
              </li>
              <li>
                <p>条件 :</p>
                <p>归属({{typeText}})／类型({{categoryId==''?'全部':categoryId.split(',').length}})／状态({{statusId==''?'全部':statusId.split(',').length}})</p>
              </li>
              <li>
                <p>结果 :</p>
                <p v-if='listCount>0'>共<span>{{listCount}}</span>个，已签约<span>{{Complete}}</span>个，签约中<span>{{Progressive}}</span>个，洽谈中<span>{{Negotiation}}</span>个，已过期<span>{{Expired}}</span>个</p>
                <p class="noResult" v-if='listCount==0'>暂无结果</p>
              </li>
            </ul>
          </div>
          <div class="right">
            <button type="button" class="againSearch" @click='showSearch()'>重新搜索</button>
          </div>
        </div>
      </div>
      <!-- :data="List" -->

      <div class="scroll-list-wrap" style="background: white;" v-if='flagList'>
        <cube-scroll ref="scroll" :data="List" :options="options" @pulling-up="onPullingDown">
          <!-- 检索公司列表 -->
          <div class="everSales">
            <ul>
              <li v-for='(item,index) in List' :key='index'>
                <!-- <span class="number">{{index=(index<9?('0'+(index+1)):(index+1))}}</span> -->
                <div class="listTop">
                  <button type="button" :class="{qiaTan:item.Status==1,qianYue:item.Status==2,qianYued:item.Status==3,fangQi:item.Status==-1,xinJian:item.Status==0,guoQi:item.Status==-3}">{{item.StatusName}}</button>
                  <i class="topI">{{item.StatusName}}／{{item.TypeID==1?'家装':'门店'}}／{{item.CategoryName}}</i>
                </div>

                <!-- <button type="button" >洽谈中</button> -->
                <div class="listMid">
                  <p class="name">{{item.Name}}</p>
                  <p class="person">{{item.UserName}}(业务)——{{item.DealerName}}(经销)——{{item.QYName}}(区域)</p>
                </div>
                <div class="listBottom">
                  <div class="xiaoE">
                    <p><i class="dot"></i><span>上月销额：{{item.MonthPrice}}元</span></p>
                    <p><i class="dot"></i><span>累计销额：{{item.CompanySalePrice}}元</span></p>
                  </div>
                  <p><i class="dot"></i><span>联系方式：{{(item.Mobile==null||item.Mobile=='')?'暂无':item.Mobile}}</span></p>
                  <p><i class="dot"></i><span>详细地址：{{item.PCAName
                      }}{{item.Address}}</span></p>
                </div>
              </li>

            </ul>
          </div>
        </cube-scroll>
      </div>
      <div class="weiJiansuo" v-if='flagWei'>
        <img src="./weiJiansuo.png" alt="">
        <p>搜索家装公司</p>
      </div>
      <empty v-if='emptyFlag'></empty>

      <!-- 公司检索弹层 -->
      <div class="searchMask" v-if='searchFlag'>
        <div class="stateContent">
          <div class="guanBiImg" @click='hideSearch()'>
            <img class="guanBi" src="./guanBi.png" alt="">
          </div>
          <div class="stateUl">
            <h3>公司检索</h3>
            <div class="search">
              <input type="text" placeholder="请输入关键词搜索" v-model="keyword">
              <img src="./del.png" alt="" @click='delValue()'>
            </div>
            <span class="tips">搜索范围：公司全称、公司简称、联系人、公司地址、业务员</span>
            <!-- 公司归属 -->
            <h3>公司归属</h3>
            <ul class="guiShuType">
              <li :class='{active:guiShuClass}' @click='allGuiShu()'>不限</li>
              <li v-for='(item,index) in guiShuType' :key='index' :class="{active:index==isGuiShu}" @click="guiShuCheck(item,index)">{{item.Name}}</li>
            </ul>
            <!-- 公司分类 -->
            <h3>公司类型</h3>
            <ul class="companyList">
              <li :class='{active:allClassify}' @click='checkClassify()'>全部</li>
              <li v-for="(item,index) in Style" :key="index" :class="{active:isStyle.indexOf(item.ID)>-1}" @click="companyCheck(item)">{{item.Name}}</li>
            </ul>
            <!-- 全部状态 -->
            <h3>状态筛选</h3>
            <ul class="allState">
              <li :class='{active:allStatus}' @click='checkAllStatus()'>全部</li>
              <li v-for="(item,index) in allStateText" :key="index" :class="{active:isStatus.indexOf(item.ID)>-1}"
                @click="stateCheck(item)">{{item.Name}}</li>
            </ul>

          </div>
          <div class="btnSearch">
            <button type="button" class="sureSearch" @click='sureSearch()'>我要搜索</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // import eventVue from '../../js/eventVue.js';
  import qs from 'qs';
  import axios from "axios";
  import empty from "../../components/empty"
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  export default {
    name: 'CompanyRetrieval',

    data() {
      return {
        List: [],
        // isActive:[],
        // List: [],
        idList: [],
        companyName: '',
        flagList: false,
        flagWei: true,
        emptyFlag: false,
        pageCount: 0,
        // page:0,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        admin: false,
        keyword: '',
        active: false,
        pageMax: 1,
        timer: null,
        statusId: '', //公司状态
        typeId: '', //公司归属
        typeText: '',
        categoryId: '', //公司类型
        comText: '不限',
        listCount: -1, //总数
        Negotiation: -1, //洽谈中
        Progressive: -1, //签约中
        Complete: -1, //已签约
        Expired: -1, //已过期
        searchFlag: false,
        allClassify: true, //全部公司分类
        guiShuClass: true,
        isStyle: [],
        isStatus: [],
        // 公司类型列表
        Style: [],
        isGuiShu: -1,
        allStatus: true, //全部公司状态
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
        //公司归属
        guiShuType: [{
          ID: 1,
          Name: '家装',
          check: false,
        }, {
          ID: 2,
          Name: '门店',
          check: false,
        }],
      }
    },
    created() {
      this.move()
      this.getStyleList()
      this.typeId = this.$route.query.typeId || ''
      this.guiShuClass = false
      if (this.typeId == 1) {
        this.typeText = '家装'
        this.isGuiShu = 0
      } else if (this.typeId == 2) {
        this.typeText = '门店'
        this.isGuiShu = 1
      } else if (this.typeId == '') {
        this.typeText = '不限'
        this.isGuiShu = -1
        this.guiShuClass = true

      }
      this.categoryId = this.$route.query.categoryId || ''
      if (this.categoryId != '') {
        let isCcategory = this.categoryId.split(',')
        this.allClassify = false
        isCcategory.forEach(item => {
          this.isStyle.push(parseInt(item))
        })
      }
      this.statusId = this.$route.query.statusId || ''
      if (this.statusId != '') {
        let isZhu = this.statusId.split(',')
        isZhu.forEach(item => {
          this.isStatus.push(parseInt(item))
        })
        this.allStatus = false
      }
      this.keyword = this.$route.query.keyword

      this.getList()
    },
    components: {
      empty,
    },
    computed: {
      options() {
        return {
          pullUpLoad: this.pullUpLoadObj,
          scrollbar: true
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
      onPullingUp() {
        // 更新数据
        if (this.pageCount >= this.page) {
          this.getList(this.page)
        } else {
          this.$refs.scroll.forceUpdate()
        }
      },
      onPullingDown() {
        // 下拉刷新数据
        if (this.pageMax === this.pageCount) {
          this.$refs.scroll.forceUpdate()
        } else {
          // 增加过渡时间，解决多次触发bug
          clearTimeout(this.timer)
          this.timer = setTimeout(() => {
            this.getMoreData()
          }, 1000)
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
      getList() {
        this.List = []
        this.pageCount = 0
        this.getMoreData()
      },
      getMoreData() {
        axios({
            url: this.getHost() + '/Company/CompanyListByName',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token'),
              keyword: this.keyword,
              statusId: this.statusId,
              typeId: this.typeId,
              categoryId: this.categoryId,
              // SaleID: this.SaleID,
              pageCount: this.pageCount + 1
            })
          })
          .then(res => {
            console.log(res)
            if (res.data.Status === 1) {
              this.pageMax = res.data.Data.pageCount
              this.listCount = res.data.Data.listCount
              this.Negotiation = res.data.Data.Negotiation
              this.Progressive = res.data.Data.Progressive
              this.Complete = res.data.Data.Complete
              this.Expired = res.data.Data.Expired
              this.pageCount++
              this.List = this.List.concat(res.data.Data.list)
              this.flagWei = false
              this.flagList = true
              if (this.List.length == 0) {
                this.emptyFlag = true
                this.flagWei = false
                this.flagList = false
              } else {
                this.emptyFlag = false
                this.flagList = true
              }
            } //else if (res.data.Status < 0) {
            //   this.delCookie("UserId")
            //   this.delCookie("token")
            //   this.setAccessId('')
            //   location.replace('/')
            // } 
            else {
              this.getToast(res.data.Message, 'warn')
            }
          })
      },

      contains(arr, obj) {
        var i = arr.length;
        while (i--) {
          if (arr[i].ID === obj) {
            return true;
          }
        }
        return false;
      },
      // 获取公司类型
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
      // 显示搜素弹层
      showSearch() {
        this.searchFlag = true
        // eventVue.$emit("newSearchShow", this.searchFlag)
        this.stop()
      },
      // 关闭搜索弹层
      hideSearch() {
        this.searchFlag = false
        // eventVue.$emit("newSearchHide", this.searchFlag)
        this.move()
      },
      //点击全部分类
      checkAllStatus() {
        this.allStatus = true
        this.isStatus = []
        this.statusId = ''
      },
      stateCheck(item) {
        this.allStatus = false
        let id = item.ID
        let indexId = this.isStatus.indexOf(id);
        if (indexId < 0) {
          this.isStatus.push(id);
          this.statusId = this.isStatus.join(',')
        } else {
          if (this.isStatus.length > 1) {
            this.isStatus.splice(indexId, 1)
            this.statusId = this.isStatus.join(',')
          }

        }

      },
      // 点击归属
      allGuiShu() {
        this.guiShuClass = true
        this.isGuiShu = -1
        this.typeText = '不限'
        this.typeId=''
      },
      guiShuCheck(item, index) {
        this.typeText = item.Name
        this.isGuiShu = index
        this.guiShuClass = false
        this.typeId = item.ID

      },
      //点击公司分类
      checkClassify() {
        this.allClassify = true
        this.isStyle = []
        this.categoryId = ''
      },
      companyCheck(item) {
        this.allClassify = false
        let id = item.ID
        let indexId = this.isStyle.indexOf(id);
        console.log(typeof this.isStyle)

        if (indexId < 0) {
          this.isStyle.push(id);
          this.categoryId = this.isStyle.join(',')
        } else {
          if (this.isStyle.length > 1) {
            this.isStyle.splice(indexId, 1)
            this.categoryId = this.isStyle.join(',')
          }
        }
        console.log(this.isStyle)
      },
      delValue() {
        this.keyword = ''
      },
      // 确定搜索
      sureSearch() {
        if (this.keyword == '') {
          this.getToast("请输入关键字", 'warn')
        } else {
          this.searchFlag = false
          this.getList()
        }

      },
      ...mapMutations({
        setAccessId: 'SET_ACCESSID',
      })
    }
  }

</script>

<style scoped>
  .scroll-list-wrap {
    height: calc(120vh - 210px);
  }

  .CompanyRetrieval {
    background-color: white;
  }

  .retrievaList {
    width: 100%;
    height: 100vh;
    overflow: hidden;
  }

  .top {
    width: 100%;
    background: white;
    padding-top: 14px;
    padding-bottom: 20px;
  }

  .searchResult {
    width: 86%;
    padding: 18px 14px 14px 14px;
    position: relative;
    background: #F7F7F7;
    display: flex;
    margin: 0 auto;
    border-radius: 20px;

  }

  .right {
    width: 30%;
    height: 100%;
  }

  .searchResult ul li {
    /* display: flex; */
    width: 100%;
    overflow: hidden;
  }

  .searchResult ul li p {
    float: left;
    line-height: 20px;
    color: #B0B0B0;
    font-size: 12px;
  }

  .searchResult ul li p.noResult {
    color: #F26F53;
  }

  .searchResult ul li p:nth-child(1) {
    width: 22%;

  }

  .searchResult ul li p:nth-child(2) {
    width: 76%;

  }

  .searchResult p span {
    color: #E2C78F;
  }

  .searchResult button {
    display: block;
    padding: 0 12px;
    height: 30px;
    border-radius: 14px;
    background: #E2C78F;
    line-height: 30px;
    font-size: 11px;
    color: white;
    position: absolute;
    top: 0;
    right: 12px;
    bottom: 0;
    margin: auto;
  }

  .everSales {
    width: 94%;
    margin: 0 auto;
  }

  .everSales ul {
    width: 100%;
    margin-top: 14px;

  }

  .everSales ul li {
    padding: 15px 18px;
    overflow: hidden;
    box-shadow: 0px -1px 34px -1px #EDEDED;
    border-radius: 8px;
    margin-bottom: 12px;
  }

  .everSales ul li .number {
    display: block;
    margin-bottom: 0;
    margin-top: 0;
    margin: auto;
    background: rgba(212, 166, 103, 1);
    border-radius: 2px;
    font-size: 10px;
    font-family: PingFangSC-Regular;
    color: rgba(251, 247, 238, 1);
    padding: 0 5px;
    height: 22px;
    line-height: 22px;
  }

  .everSales ul li .name {
    font-size: 16px;
    font-family: PingFangSC-Regular;
    color: rgba(51, 51, 51, 1);
    line-height: 24px;
    margin-bottom: 8px;
  }

  .everSales ul li .person {
    width: 100%;
    font-size: 10px;
    font-family: PingFangSC-Medium;
    color: #7D7D7D;
    background: #F4F4F4;
    padding: 6px;
    line-height: 16px;

  }

  .everSales ul li button {
    display: block;
    padding: 0 6px;
    border-radius: 3px;
    font-size: 10px;
    padding: 0 6px;
    height: 26px;
    line-height: 26px;
    color: white;
  }

  .listTop {
    display: flex;
    justify-content: space-between;
    margin-bottom: 8px;
  }

  .listMid {
    padding-bottom: 12px;
    border-bottom: 1px solid #F5F5F5;
  }

  /* 洽谈中1 */
  .negotiation {
    background: rgba(246, 234, 212, 1);
    color: rgba(187, 159, 97, 1)
  }

  /* 签约中2 */
  .contracting {
    background: rgba(251, 193, 180, 1);
    color: #F26F53;
  }

  /* 已签约3 */
  .contracted {
    background: rgba(207, 207, 207, 1);
    color: #FFFFFF;
  }

  /* 已新建0 */
  .new {
    background: rgba(207, 207, 207, 1);
    color: rgba(255, 255, 255, 1);
  }

  /* 已放弃-1 */
  .giveUp {
    background: rgba(207, 207, 207, 1);
    color: rgba(255, 255, 255, 1);
  }

  /* 已删除-2 */
  .deled {
    background: rgba(207, 207, 207, 1);
    color: rgba(255, 255, 255, 1);
  }

  .weiJiansuo {
    padding-top: 44px;
  }

  .weiJiansuo img {
    display: block;
    width: 201px;
    height: 158px;
    margin: 0 auto;
  }

  .weiJiansuo p {
    text-align: center;
    font-size: 15px;
    color: #b2b2b2;
    padding-top: 6px;
  }

  .fangQi {
    background: #CFCFCF;
  }

  .xinJian {
    background: #BFDBC3;
  }

  .qiaTan {
    background: #ECDBBD;
  }

  .qianYue {
    background: #F4B5A7;
  }

  .qianYued {
    background: #F3CF9D;
  }

  .guoQi {
    background: #CDDFEE;
  }

  .topI {
    color: #B5B5B5;
    font-size: 11px;
    font-style: normal;
    line-height: 26px;
  }

  .dot {
    display: block;
    width: 4px;
    height: 4px;
    background: #E2C78F;
    border-radius: 50%;
    margin-right: 6px;
    margin-top: 2px;
  }

  .xiaoE {
    display: flex;
  }

  .xiaoE>p {
    width: 50%;
  }

  .listBottom {
    padding-top: 12px;
  }

  .listBottom p {
    display: flex;
    margin-bottom: 12px;
  }

  .listBottom span {
    color: #666666;
    font-size: 12px;
    display: block;
  }

  /* 公司检索 */
  .searchMask {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 100;
  }

  .stateContent {
    width: 100%;
    height: 86%;
    overflow-y: auto;
    position: absolute;
    z-index: 300;
    -webkit-overflow-scrolling: touch;
    bottom: 0;
    background: white;
  }

  .stateUl {
    margin: 0 auto;
    padding: 22px 6px 0 6px;
    padding-bottom: 10px;
    max-height: 78%;
    overflow-y: scroll;
    -webkit-overflow-scrolling: touch;
  }

  .stateUl ul {
    width: 95%;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    padding-top: 14px;
    flex-wrap: wrap;
    margin-bottom: 14px;


  }

  .stateContent h3 {
    font-size: 16px;
    color: #171717;
    font-weight: 550;
    padding-left: 10px;
  }

  .stateContent li {
    padding: 0 16px;
    line-height: 24px;
    border-radius: 13px;
    border: 1px solid #CCCCCC;
    font-size: 12px;
    color: #808080;
    margin-bottom: 8px;
  }

  .stateContent li.active {
    border: 1px solid transparent;
    background: #E7C58B;
    color: white !important;
  }

  .companyList li:last-child {
    margin-right: 44%
  }

  .allState {
    margin-bottom: 20px;
  }

  .stateUl .guiShuType {
    justify-content: start;
    margin-bottom: 20px;
  }

  .stateUl .guiShuType li {
    margin-right: 14px;
    margin-bottom: 0;
  }

  .search {
    width: 100%;
    height: 40px;
    margin: 0 auto;
    border-radius: 20px;
    background: #F7F7F7;
    margin-top: 14px;
    margin-bottom: 12px;
    display: flex;
    justify-content: space-between;
  }

  .search input {
    width: 78%;
    display: block;
    border: none;
    background: none;
    padding-left: 16px;
    color: black;
    font-size: 14px;
  }

  .search img {
    display: block;
    width: 16px;
    height: 16px;
    float: right;
    margin-right: 12px;
    margin-top: 12px;
  }

  .search input::placeholder {
    color: #B0B0B0;
    font-size: 12px;
  }

  .tips {
    display: block;
    font-size: 10px;
    color: #9B9B9B;
    padding-left: 15px;
    margin-bottom: 30px;
  }

  .guanBiImg {
    width: 34px;
    height: 34px;
    position: absolute;
    top: 6px;
    right: 10px;
    z-index: 500;
  }

  .guanBi {
    display: block;
    width: 14px;
    height: 14px;
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    margin: auto;
  }

  .btnSearch {
    width: 100%;
    box-shadow: 2px -6px 5px 1px rgba(199, 199, 199, 0.12);
    padding: 18px 0;
    /* position: absolute;
    bottom: 0; */
  }

  .sureSearch {
    display: block;
    width: 80%;
    height: 40px;
    margin: 0 auto;
    color: white;
    text-align: center;
    line-height: 40px;
    background: #E2C78F;
    border-radius: 4px;
  }

  .left {
    width: 88%;
  }

</style>
