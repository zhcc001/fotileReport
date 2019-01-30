<!-- 待续签 -->
<template>
  <div class="waitXuQian">
    <div class="container">
      <h5>待续签列表</h5>
      <p>该列表一共有{{num}}家公司待续签，请做好工作安排（不包括续签申请通过的）</p>
      <div class="scroll-list-wrap">
        <cube-scroll ref="scroll" :data="trList" :options="options" @pulling-up="onPullingDown">
          <table>
            <thead>
              <tr>
                <td>序号</td>
                <td>公司名称</td>
                <td>合同剩余天数</td>
                <td>业务员</td>
                <td>经销商</td>
                <td>详情</td>
              </tr>
            </thead>
            <tbody>
              <tr v-for='(item,index) in trList' :key='index' >
                <td><span>{{item.rn}}</span></td>
                <td><span>{{item.Name}}</span></td>
                <td><span>{{item.DaySum}}</span></td>
                <td><span>{{item.UserName}}</span></td>
                <td><span>{{item.DealerName}}</span></td>
                <td @click='detailClick(item,index)'><span class="detailLink">></span></td>
              </tr>
            </tbody>
          </table>
        </cube-scroll>
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
  export default {
    name: 'WaitXuQian',
    data() {
      return {
        trList: [],
        pageCount: 0,
        timer: null,
        pageMax: 1,
        // page:0,
        pullUpLoad: false,
        pullUpLoadThreshold: 0,
        pullUpLoadMoreTxt: '--加载更多--',
        pullUpLoadNoMoreTxt: '--已经到底部了--',
        num:-1,
        
      }
    },
    // beforeRouteEnter(to, from, next) {
    //   if (from.name == 'businessStatistics') {
    //       document.body.scrollTop = 0;
    //       document.documentElement.scrollTop = 0;
    //   }
    //   next();
    // },
    created() {
      document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
      this.getTable()
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
        console.log(123)
        console.log(this.pageMax,this.pageCount)
        // 下拉刷新数据
        if (this.pageMax === this.pageCount) {
          this.$refs.scroll.forceUpdate()
        } else {
          // 增加过渡时间，解决多次触发bug
          clearTimeout(this.timer)
          this.timer = setTimeout(() => {
            this.getTable()
          }, 1000)
        }
      },
      getTable() {
        axios({
          url: this.getHost() + '/Statistics/WaitRenewCompanyList',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            page: this.pageCount+1
          })
        }).then(res => {
          console.log(res)
          if (res.data.Status == 1) {
            this.num = res.data.Data.rowCount
            this.pageMax = res.data.Data.pageCount
            this.pageCount++
            this.trList = this.trList.concat(res.data.Data.list)

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
      // 进入详情页
      detailClick(item, index) {
        this.$router.push({
          path: '/companyDetail',
          query: {
            id: item.ID
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
  .container {
    background: white;

  }

  h5 {
    font-size: 16px;
    color: #303030;
    text-align: center;
    padding: 16px 0;
    border-bottom: 1px solid #f5f5f5;
    margin-bottom: 16px;
  }

  p {
    font-size: 11px;
    color: #616161;
    padding-left: 10px;
    margin-bottom: 16px;

  }

  table {
    width: 96%;
    margin: 0 auto;
    border-collapse: collapse;
  }

  table thead tr td {
    font-size: 11px;
    color: #000;
    width: 34px;
    font-weight: bold;
  }

  table tr td:nth-child(2) {
    background: #f5f5f5
  }

  table tr td {
    border: 0;
    font-size: 10px;
    color: #505050;
    text-align: center;
    padding: 12px 0;
    border: 1px solid #afafaf;

  }

  table tr td span {
    line-height: 18px;

  }

  .scroll-list-wrap {
    height: calc(120vh - 210px);
    margin-bottom: 80px;
  }

</style>
