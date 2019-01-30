<template>
  <div id="home" :class='{app:appClass}'>
    <!-- 首页 -->
    <!-- 头部信息 -->
    <div class="home_top">
      <div class="userImg">
        <img src="./userP.png" alt="">
      </div>
      <div class="info">
        <p>{{Name}}</p>
        <p>{{Organization}}</p>
      </div>
      <span @click="PsdMask(true)">账号管理</span>
    </div>
    <!-- 消息中心 -->
    <div class="home_message">
      <div class="msg_top">
        <div class="msgList" @click='showSearch()'>
          <a href="javascript:void(0)">
            <div class="msgbg0">
              <img src="./index111.png" alt="">
            </div>
            <p>公司检索</p>
          </a>
        </div>
        <div class="msgList" v-for="(item,index) in msgList" :key="index">
          <router-link :to="item.Src">
            <div class="msgbg" :class="item.Image">
              <span>{{item.Number}}</span>
              <i></i>
            </div>
            <p>{{item.Name}}</p>
          </router-link>
        </div>
      </div>
      <div class="msg_bottom">
        <router-link to="/announce" class="announce" v-if="annou">发布公告</router-link>
        <router-link to="/addUser" class="addUser" v-if="addUser">新建用户</router-link>
        <router-link to="/addCompany" class="addCompany" v-if="action">新建公司</router-link>
        <router-link to="/appealHome" href="javascript:;" class="appeal" v-if="action">我要申诉</router-link>
      </div>

    </div>
    <div class="dataBox">
      <h5 class="dataTitle">数据报表统计</h5>
      <div class="dataModel">
        <div class="business" @click='yeWu()'>
          <div class="text">
            <h6>业务统计</h6>
            <p class="clickDetail">点击查看业务统计详情</p>
          </div>
          <img class="sanJiao" src="./sanJiao.png" alt="">

        </div>
        <div class="sale" @click='xiaoShou()'>
          <div class="text">
            <h6>销售统计</h6>
            <p class="clickDetail">点击查看销售统计详情</p>
          </div>
          <img class="sanJiao" src="./sanJiao.png" alt="">
        </div>
      </div>
    </div>
    <!-- 最新公告 -->
    <div class="home_newAnnounce">
      <i></i>
      <div class="annouText">
        <p class="time">{{oneAnnounce.CreateDate}}</p>
        <p class="announceMsg">{{oneAnnounce.Content}}</p>
        <p v-if='oneAnnounceFlag' class="annouEmpty">暂无公告</p>

      </div>
      <router-link to="/announceList" class="more">更多</router-link>
    </div>
    <!-- 不同角色的入口模块 -->
    <!-- 重点关注 -->
    <div class="home_content" v-if="buniess">
      <div class="focus" v-if="List.length">
        重点关注
      </div>
      <div>
        <focusList :list="List" :Action="action" :IsEmphasis=true></focusList>
      </div>
      <div class="lookMore" v-if="List.length>=3">
        <a href="javascript:;" @click="lookMore">查看全部</a>
      </div>
    </div>

    <!-- 最新申诉 -->
    <appealList v-if="appeal" :list="applyList" :AccessId="AccessId" @refresh="getNewApply" @refreshNaav="getNavInfo"></appealList>
    <!-- 账号管理遮罩 -->
    <div id="mask" v-if="hasPsdMask">
      <div class="maskContain">
        <router-link to="/modifyPsd" class="modifyPsd">修改密码</router-link>
        <a href="javascript:;" class="loginOut msg" @click="msgSetting">消息推送设置</a>
        <a href="javascript:;" class="loginOut" @click="logout">退出登录</a>
        <a href="javascript:;" class="cancel" @click="PsdMask(false)">取消</a>
      </div>
    </div>
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
            <li v-for="(item,index) in allStateText" :key="index" :class="{active:isStatus.indexOf(item.ID)>-1}" @click="stateCheck(item)">{{item.Name}}</li>
          </ul>

        </div>
        <div class="btnSearch">
          <button type="button" class="sureSearch" @click='sureSearch()'>我要搜索</button>
        </div>
      </div>
    </div>
    <!-- 消息绑定 -->
    <div class="msgMask" v-if='bindFlag'>
      <div class="msgContent">
        <div class="box">
          <h5>消息推送绑定</h5>
          <p>你好，请确认系统消息推送是否与当前登录微信号绑定？</p>
          <i>否则，您将接收不到相关微信消息推送。</i>
          <div class="msgBtn">
            <button type="button" class="noBind" @click='noBindClick()'>暂不绑定</button>
            <button type="button" class="binding" @click='msgSetting()'>立即绑定</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import eventVue from '../../js/eventVue.js'
  import {
    mapGetters,
    mapMutations
  } from 'vuex'
  import focusList from "../../components/focusList";
  import appealList from "../../components/appealList";
  import qs from 'qs'
  import axios from "axios";

  export default {
    name: 'Home',
    data() {
      return {
        appClass: false,
        bindFlag: false,
        //最新申诉显示
        appeal: false,
        //行动
        action: false,
        //重点关注
        List: [],
        //导航
        msgList: [],

        hasPsdMask: false,
        // 重点关注显示3条
        maxHeight: true,
        words: "查看全部",
        //登陆信息
        Name: '',
        Organization: '',
        oneAnnounce: {
          CreateDate: '',
          Content: ''
        },
        oneAnnounceFlag: false,
        // annouTextFlag:true,
        buniess: false,
        applyList: [],
        addUser: false,
        annou: false,
        emptyInfo: false,
        weXinMsg: '',
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
        allClassify: true, //全部公司分类
        guiShuClass: true,
        isStyle: [],
        isStatus: [],
        // 公司类型列表
        Style: [],
        isGuiShu: -1,
        searchFlag: false,
        statusId: '', //公司状态
        typeId: '', //公司归属
        categoryId: '', //公司类型
        keyword: '', //关键字
      }
    },
    components: {
      focusList,
      appealList
    },
    created() {
      this.move()
      console.log(sessionStorage.getItem('msgId'), 'msgId')

      this.authorized()

      this.getStyleList()
      //业务员隐藏最新申诉
      if (this.AccessId == 5) {
        this.appeal = false
        this.action = true
        this.buniess = true
        this.getImportant()
      } else if (this.AccessId == -1) {
        this.$router.push({
          path: "/adminIndex"
        })
      } else {
        this.action = false
        this.appeal = true
        this.buniess = false
        this.getNewApply()
      }
      if (this.AccessId == 2 || this.AccessId == 3) {
        this.addUser = true
      }
      if (this.AccessId == 2 || this.AccessId == 1) {
        this.annou = true
      }
      //获取姓名
      this.Name = getCookie("Name");
      this.Organization = getCookie("Organization");
      //获取首页信息
      this.getNavInfo()
      this.getOneAnnounce()


    },
    computed: {
      ...mapGetters([
        'AccessId'
      ])
    },
    methods: {
       /***滑动限制***/
       stop(){
        var mo=function(e){e.preventDefault();};
        document.body.style.overflow='hidden';
        document.addEventListener("touchmove",mo,false);//禁止页面滑动
      },
      /***取消滑动限制***/
      move(){
        var mo=function(e){e.preventDefault();};
        document.body.style.overflow='';//出现滚动条
        document.removeEventListener("touchmove",mo,false);
      },
      PsdMask(bool) {
        this.hasPsdMask = bool

      },
      logout() {
        this.setAccessId('')
        this.delCookie('token')
        this.delCookie('UserId')
        this.delCookie('Name')
        this.delCookie('Organization')
        location.replace('/')
      },
      lookMore() {
        this.$router.push({
          path: '/companyList'
        })
      },
      //获取导航列表
      getNavInfo() {
        axios({
            url: this.getHost() + '/Home/GetHomeButton',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.msgList = res.data.Data
            } else if (res.data.Status < 0) {
              this.delCookie("UserId")
              this.delCookie("token")
              this.setAccessId('')
              location.replace('/')
            } else {
              this.getToast(res.data.Message, 'warn')
            }
          })
          .catch(res => {
            this.getToast(res.data.Message, 'warn')
          })
      },
      //获取单挑公告
      getOneAnnounce() {
        axios({

            url: this.getHost() + '/Home/LatestNiotice',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.oneAnnounce = res.data.Data

            } else if (res.data.Status < 0) {
              this.getToast("登录失效，请重新登录", 'warn')
              setTimeout(() => {
                this.delCookie("UserId")
                this.delCookie("token")
                this.setAccessId('')
                location.replace('/')
              }, 2000);
            } else {
              this.oneAnnounceFlag = true

            }
          })

      },
      //获取重点关注
      getImportant() {
        axios({
            url: this.getHost() + '/Home/Emphasis',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.List = res.data.Data.list
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
      getNewApply() {
        axios({
            url: this.getHost() + '/Home/Appeal',
            method: 'post',
            data: qs.stringify({
              UserId: getCookie('UserId'),
              token: getCookie('token')
            })
          })
          .then(res => {
            if (res.data.Status === 1) {
              this.applyList = res.data.Data.list
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
      // 判断是否授权
      authorized() {
        axios({
          url: this.getHost() + '/Home/GetUserOpenId',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token')
          })
        }).then(res => {
          console.log(res)
          if (res.data.Status == 403) {
            if (sessionStorage.getItem('msgId')) {
              this.bindFlag = true
            }

          }
          this.weXinMsg = res.data.Message

        })
      },
      // 消息设置
      msgSetting() {
        sessionStorage.removeItem('msgId')
        // this.$router.push({
        //   path: "/msgSetting"
        // })
        console.log(this.weXinMsg)
        window.location.href = this.weXinMsg

      },
      noBindClick() {
        this.bindFlag = false
        sessionStorage.removeItem('msgId')
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
        this.appClass = true
        this.stop()
      },
      // 关闭搜索弹层
      hideSearch() {
        this.searchFlag = false
        this.appClass = false
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
        console.log()
      },
      // 点击归属
      allGuiShu() {
        this.guiShuClass = true
        this.isGuiShu = -1
      },
      guiShuCheck(item, index) {
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
        if (indexId < 0) {
          this.isStyle.push(id);
          this.categoryId = this.isStyle.join(',')
        } else {
          if (this.isStyle.length > 1) {
            this.isStyle.splice(indexId, 1)
            this.categoryId = this.isStyle.join(',')
          }
        }
      },
      // 确定搜索
      sureSearch() {
        if (this.keyword == '') {
          this.getToast("请输入关键字", 'warn')
        } else {
          this.$router.push({
            path: '/companyRetrieval',
            query: {
              typeId: this.typeId,
              statusId: this.statusId,
              categoryId: this.categoryId,
              keyword: this.keyword
            }
          })
        }

      },
      delValue(){
        this.keyword=''
      },
      //跳转业务统计
      yeWu() {
        this.$router.push({
          path: '/businessStatistics',

        })
      },
      //跳转销售统计
      xiaoShou() {
        this.$router.push({
          path: '/saleStatistics',

        })
      },
      ...mapMutations({
        setAccessId: 'SET_ACCESSID'
      })
    },
  }

</script>

<style scoped>
  @import '../../common/focusList.css';
  @import '../../common/mask.css';

  .app {
    height: 100vh;
    overflow: hidden;
  }

  #home {
    width: 100%;
    box-sizing: border-box;
  }

  /* 头部信息 */

  .home_top {
    display: flex;
    align-items: center;
    background: #fff;
    padding: 0 15px;
    height: 85px;
  }

  .userImg {
    width: 43px;
    height: 43px;
    margin-right: 10px;
  }

  .userImg img {
    width: 100%;
    height: 100%;
  }

  .info {
    width: 0;
    flex-grow: 1;
  }

  .info p:first-child {
    font-weight: bold;
    margin-bottom: 5px;
  }

  .info p:nth-child(2) {
    font-size: 12px;
  }

  .home_top span {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 75px;
    height: 30px;
    background-color: #E2C78F;
    font-size: 12px;
    color: #fff;
    border-radius: 4px;
  }

  /* 消息中心 */

  .home_message {
    background-color: #fff;
    padding: 0 20px;
    margin-bottom: 10px;
    padding-bottom: 10px;
  }

  .msg_top {
    display: flex;
    align-items: center;
    flex-wrap: wrap;
    box-sizing: border-box;
    border-top: 1px solid #f0efef;
    border-bottom: 1px solid #f0efef;
    padding-bottom: 10px;
  }

  .msgList {
    width: 25%;
    height: 110px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
  }

  .msgList a {
    color: #000;
  }

  .msgbg0 img {
    display: block;
    width: 64px;
    height: 64px;
    margin: 10px;
  }

  .msgbg {
    width: 64px;
    height: 64px;
    margin: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
    color: #EFBF5F;
    font-size: 20px;
  }

  .msgbg i {
    display: inline-block;
    width: 15px;
    height: 1px;
    background-color: #EFBF5F;
  }

  .msgbg1 {
    background: url(/static/1.png) no-repeat center/contain
  }

  .msgbg2 {
    background: url(/static/2.png) no-repeat center/contain
  }

  .msgbg3 {
    background: url(/static/3.png) no-repeat center/contain
  }

  .msgbg4 {
    background: url(/static/4.png) no-repeat center/contain
  }

  .msgbg5 {
    background: url(/static/5.png) no-repeat center/contain
  }

  .msgbg6 {
    background: url(/static/6.png) no-repeat center/contain
  }

  .msgbg7 {
    background: url(/static/7.png) no-repeat center/contain
  }

  .msgbg8 {
    background: url(/static/8.png) no-repeat center/contain
  }

  .msgbg9 {
    background: url(/static/9.png) no-repeat center/contain
  }

  .msgbg11 {
    background: url(/static/11.png) no-repeat center/contain
  }

  .msgbg10 {
    background: url(/static/10.png) no-repeat center/contain
  }

  .msgList p {
    text-align: center;
  }

  .msg {
    background-color: white !important;
    border: 1px solid #989898;
  }

  .msg_bottom {
    width: 100%;
    /* height: 80px; */
    display: flex;
    align-items: center;
    padding: 5px 0;
  }

  .msg_bottom a {
    flex: 1;
    height: 46px;
    display: flex;
    justify-content: center;
    align-items: center;
    margin-right: 10px;
    border-radius: 4px;
  }

  .msg_bottom a:last-child {
    margin-right: 0;
  }

  .announce,
  .addUser {
    background-image: linear-gradient(45deg, #E2C78F, #D5AE61);
    color: #fff
  }

  .addCompany,
  .appeal {
    border: 1px solid #E2C78F;
    color: #E2C78F;
    box-sizing: border-box;
  }

  /* 公告 */

  .home_newAnnounce {
    width: 100%;
    height: 55px;
    display: flex;
    justify-content: center;
    align-items: center;
    background-color: #fff;
    padding: 6px 0;
  }

  .home_newAnnounce i {
    width: 50px;
    height: 35px;
    background: url(./announce.png) no-repeat center/contain;
    margin: 0 5px 0 10px
  }

  .home_newAnnounce .annouText {
    width: 0;
    flex-grow: 1;
    border-left: 1px solid #f0efef;
    padding-left: 15px;

  }

  .home_newAnnounce>div p {
    width: 100%;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .home_newAnnounce .time {
    font-size: 12px;
    color: #ccc;
    margin-bottom: 5px;
  }

  .home_newAnnounce .more {
    margin: 0 20px;
    color: #333;
    font-weight: bold;
  }

  /* 重点关注列表显示三条 */

  .height {
    /* 一个154px */
    max-height: 462px;
    overflow: hidden;
  }


  /* 暂无公告 */

  .annouEmpty {
    color: #ccc;
    font-size: 28px;
    font-weight: 600;
    text-align: center;
  }

  .msgMask {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    position: fixed;
    top: 0;
    left: 0;
    z-index: 10000;
  }

  .msgContent {
    width: 78%;
    margin: 0 auto;
    background: white;
    margin-top: 20vh;
    border-radius: 4px;
  }

  .box {
    width: 80%;
    margin: 0 auto;
    padding-top: 20px;
    padding-bottom: 15px;
  }

  .msgContent h5 {
    font-size: 18px;
    color: #404040;
    text-align: center;
    font-weight: 550;
    margin-bottom: 22px;
  }

  .msgContent p {
    font-size: 16px;
    color: #5E5E5E;
    line-height: 20px;
    margin-bottom: 20px;
  }

  .msgContent i {
    font-size: 10px;
    color: #989898;
  }

  .msgContent button {
    border: none;
    background: none;
    display: block;
    padding: 0 24px;
    font-size: 13px;
    line-height: 34px;
    border-radius: 2px;
  }

  .msgBtn {
    width: 100%;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
    padding-top: 30px;
  }

  .msgBtn .noBind {
    color: #989898;
    background: #EDECEB;
  }

  .msgBtn .binding {
    color: white;
    background: #D7B878;
  }

  /* 公司检索 */
  .searchMask {
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    position: fixed;
    bottom: 0;
    left: 0;
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

  .dataBox {
    padding: 30px 0;
    margin-bottom: 10px;
    background: white;
  }

  .dataTitle {
    font-size: 20px;
    color: #404040;
    padding-left: 10px;
    border-left: 2px solid #D5AE61;
    font-weight: 550;
    width: 88%;
    margin: 0 auto;
    margin-bottom: 24px;
  }

  .dataModel {
    width: 88%;
    margin: 0 auto;
  }

  .business {
    width: 100%;
    height: 90px;
    background: url(/static/dataBg1.png) no-repeat center/contain;
    /* background-size: 100% 100%;
    background-position: center; */
    margin-bottom: 14px;
    position: relative;
  }

  h6 {
    font-size: 18px;
    color: #4C4C4C;
    font-weight: 550;
    margin-bottom: 8px;
  }

  .clickDetail {
    font-size: 12px;
    color: #B1B1B1;
  }

  .sale {
    width: 100%;
    height: 90px;
    background: url(/static/dataBg2.png) no-repeat center/contain;
    /* background-size: 100% 100%;
    background-position: center; */
    position: relative;
  }

  .text {
    position: absolute;
    right: 0;
    width: 56%;
    top: 26px;
  }

  .sanJiao {
    position: absolute;
    width: 9px;
    height: 16px;
    right: 16px;
    top: 0;
    bottom: 0;
    margin: auto;
  }

</style>
