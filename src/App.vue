<template>
  <div id="app" :class='{app:appClass}'>
    <a href="javascript:;" @click="back" id="fixBack" v-show="needBack">
      <!-- v-show="needBack&&isminprograme" -->
      <span>返回</span>
    </a>
    <keep-alive>
      <router-view v-if="$route.meta.keepAlive">
      </router-view>
    </keep-alive>

    <router-view v-if="!$route.meta.keepAlive" />
    <!-- <router-view/> -->
  </div>
</template>

<script>
  import eventVue from './js/eventVue.js'
  import {
    mapGetters
  } from 'vuex'
  import AdminRouer from './router/adminRouter.js'
  import BusinessRouter from './router/businessRouter.js'
  import DealerRouter from './router/dealerRouter.js'
  import ManagerRouter from './router/managerRouter.js'

  export default {
    name: 'App',
    data() {
      return {
        isminprograme: false,
        needBack: false,
        isRouterAlive: true,
        appClass: false
      }
    },
    computed: {
      ...mapGetters([
        'AccessId'
      ])
    },
    watch: {
      'AccessId': function () {
        this.initRouter()
      },
      '$route': function (to, from) {
        // this.calc_transitionName(to, from)
        // 家装公司列表缓存

        if (from.path == '/companyList' && to.path == '/companyDetail') {
          from.meta.keepAlive = true
        }
        if (from.path == '/companyList' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/companyList' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        // 组员跟进列表
        if (from.path == '/CompanyFollow' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/CompanyFollow' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/CompanyFollow' && to.path == '/companyDetail') {
          from.meta.keepAlive = true
        }
        if (from.path == '/CompanyFollow' && to.path == '/SalesmanList') {
          from.meta.keepAlive = false
        }
        // 用户管理
        if (from.path == '/UserList' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/UserList' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/UserList' && to.path == '/editUser') {
          from.meta.keepAlive = true
        }

        // // 我要申诉
        // if(from.path=='/appealHome'&&to.path=='/home'){
        //   // from.meta.keepAlive=false
        //   location.reload()
        // }
        // if(from.path=='/appealHome'&&to.path=='/appealDetails'){
        //   from.meta.keepAlive=true
        // }

        // 消息中心
        if (from.path == '/messageCenter' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/messageCenter' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/messageCenter' && to.path == '/companyDetail') {
          from.meta.keepAlive = true
        }
        // 跟进审批
        if (from.path == '/appealManger' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/appealManger' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/appealManger' && to.path == '/companyDetail') {
          from.meta.keepAlive = true
        }

        // 申诉审批
        if (from.path == '/appeal' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/appeal' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/appeal' && to.path == '/appealDetails') {
          from.meta.keepAlive = true
        }

        // 进入组员跟进列表不需要缓存
        if (from.path == '/CompanyFollow' && to.path == '/myGroup') {
          from.meta.keepAlive = false
        }
        if (from.path == '/CompanyFollow' && to.path == '/companyDetail') {
          from.meta.keepAlive = true
        }
        



       

         // 销售统计
         if (from.path == '/saleStatistics' && to.path == '/notRating') {
          from.meta.keepAlive = true
        }
        if (from.path == '/saleStatistics' && to.path == '/distriRanking') {
          from.meta.keepAlive = true
        }
        if (from.path == '/saleStatistics' && to.path == '/companyRanking') {
          from.meta.keepAlive = true
        }

        if (from.path == '/saleStatistics' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/saleStatistics' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }

        // 业务统计
        if (from.path == '/businessStatistics' && to.path == '/noQianYue') {
          from.meta.keepAlive = true
        }
        if (from.path == '/businessStatistics' && to.path == '/newSale') {
          from.meta.keepAlive = true
        }
        if (from.path == '/businessStatistics' && to.path == '/waitXuQian') {
          from.meta.keepAlive = true
        }
        if (from.path == '/businessStatistics' && to.path == '/home') {
          // from.meta.keepAlive=false
          location.reload()
        }
        if (from.path == '/businessStatistics' && to.path == '/adminIndex') {
          // from.meta.keepAlive=false
          location.reload()
        }



        if (to.name === 'Home' || to.name === 'Login' || to.name === 'AdminIndex') {
          this.needBack = false
        } else {
          this.needBack = true
        }
      }
    },
    created() {
      this.move()
      let that = this

      function ready() {
        if (window.__wxjs_environment === 'miniprogram') {
          that.isminprograme = true
        }
      }
      /* eslint-disable no-undef */
      if (!window.WeixinJSBridge || !WeixinJSBridge.invoke) {
        document.addEventListener('WeixinJSBridgeReady', ready, false)
      } else {
        ready()
      }
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
      afterEnter() {},
      back() {
        this.appClass=false
        this.code = this.getSearchParam('code')
        console.log(this.code)
        if (this.code) {
          location.href = "/#/home"
          // window.location.href='/Home'
        }else{
          this.$router.back()

        }
      },
      getSearchParam(key) {
        var ret = location.search.match(new RegExp('(\\?|&)' + key + '=(.*?)(&|$)'))
        return ret && decodeURIComponent(ret[2])
      },
      initRouter() {
        let AccessId = this.AccessId
        //-1管理员
        if (AccessId == -1) {
          this.$router.addRoutes(AdminRouer)
          //业务员5
        } else if (AccessId == 5) {
          this.$router.addRoutes(BusinessRouter)
          //经销商4
        } else if (AccessId == 4) {
          this.$router.addRoutes(DealerRouter)
          //区域经理3 部门经理2 分总1
        } else if (AccessId == 3 || AccessId == 2 || AccessId == 1) {
          this.$router.addRoutes(ManagerRouter)
        }
        this.$router.addRoutes([{
          path: '*',
          redirect: '/404'
        }])
      }
    },

    mounted() {
      if (this.AccessId) {
        this.initRouter()
      }
    }
  }

</script>

<style>
  .app {
    overflow: hidden;
  }

  #app {
    height: 100vh;
    font-size: 14px;
    -webkit-overflow-scrolling: touch;
    -webkit-user-select: auto;
    -moz-user-select: auto;
  }

  #uploadContract .cube-upload .cube-upload-file,
  .cube-upload-btn {
    margin: 0;
    padding: 20px;
    box-sizing: border-box;
  }

  #uploadContract .cube-upload-file-def {
    width: 100% !important;
    height: 60vh !important;
  }

  #uploadContract .cube-upload-btn-def {
    width: 100% !important;
    height: 60vh !important;
    background-color: #f0f0f0;
  }

  .cube-upload-file+.cube-upload-btn {
    margin-top: -200px;
    opacity: 0;
  }

  #fixBack {
    position: fixed;
    left: 0;
    top: 11vh;
    width: 50px;
    height: 40px;
    background-color: rgba(0, 0, 0, .3);
    z-index: 999;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 0 4px 4px 0px;
    color: #fff
  }

  .HomeDecoration .cube-pullup-wrapper .before-trigger {
    padding: 10px 0 !important;
  }

  /* loading */

  .cube-loading {
    position: absolute !important;
    top: 0 !important;
    left: 0 !important;
    bottom: 0 !important;
    right: 0 !important;
    margin: auto !important;

  }

  .cube-loading-spinners {
    position: absolute !important;
    display: block !important;
    width: 1em !important;
    height: 1em !important;
    top: 0 !important;
    left: 0 !important;
    right: 0 !important;
    bottom: 0 !important;
    margin: auto !important;
  }

  .swiper-container {
    margin: 14% auto !important;
  }



  .cov-date-body[data-v-a9dd0b1a] {
    display: inline-block;
    background: #3F51B5;
    overflow: hidden;
    font-size: 16px;
    font-family: 'Roboto';
    font-weight: 400;
    position: fixed;
    display: block;
    width: 400px;
    max-width: 100%;
    z-index: 999;
    top: 0 !important;
    left: 0 !important;
    bottom: 0 !important;
    transform: none !important;
    box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.2);
  }




  .cube-btn {
    display: block;
    margin: 0 !important;
    padding: 0 !important;
    text-align: center !important;
    white-space: nowrap !important;
    cursor: pointer !important;
    font-size: 16px !important;
    line-height: 1 !important;
    background: none !important;
    outline: none !important;
    border: none !important;
    border-radius: 2px !important;
    -webkit-box-sizing: border-box !important;
    box-sizing: border-box !important;
    background-color: white !important;
    outline: none !important;
    cursor: pointer;
  }


  .timeChang .cube-btn::after {
    border: none !important;
    outline: none !important;
  }

  #button {
    text-align: center;
  }

  .saleWarp .cube-scroll-content {
    z-index: 300 !important;
  }

  .cube-image-preview-item .cube-image-preview-img {
    margin: 0 auto;
    max-width: 80% !important;
    border-radius: 12px !important;

  }

  .saleWarp .cube-scroll-wrapper {
    background: white;
  }

  .xeLineImg #echarts {
    width: 350px !important;
    height: 350px !important;
    margin: 0 auto;
  }

  .xeLineImg #echarts>div:nth-child(1) {
    width: 350px !important;
    height: 350px !important;
  }
  #echarts>div:nth-child(2){
    z-index: 200 !important;
  }
  .xeLineImg #echarts>div>canvas {
    width: 350px !important;
    height: 350px !important;
  }

  .xeLineImg #echarts div span:nth-child(2) {
    background-color: #8A8D98 !important;
  }

  .xeLineImg #echarts div span:nth-child(4) {
    background-color: #DDB971 !important;
  }

  .gradeBingImg #echarts {
    width: 160px !important;
    height: 160px !important;
  }

  .gradeBingImg #echarts>div:nth-child(1) {
    width: 160px !important;
    height: 160px !important;
  }

  .gradeBingImg #echarts>div>canvas {
    width: 160px !important;
    height: 160px !important;
  }

  /* .waitXuQian .cube-scroll-list-wrapper,.noQianYue .cube-scroll-list-wrapper{
    min-height: 500px !important;
  } */
</style>
