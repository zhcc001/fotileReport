<!-- 年累计销额经销商排行 -->
<!-- 销额公司排行 -->
<template>
  <div class="companyRanking">
    <div class="container">
      <h5>销额公司排行</h5>
      <!-- <p>小程序端最多显示前50名排名，PC端可查看所有公司排行</p> -->
      <!-- <div class="scroll-list-wrap">
                  <cube-scroll ref="scroll" :data="trList" :options="options" @pulling-up="onPullingDown"> -->
      <table>
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
          <tr v-for='(item,index) in trList' :key='index'>
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
      <!-- </cube-scroll>
            </div> -->
    </div>
  </div>
</template>
<script>
  export default {
    name: 'CompanyRanking',
    data() {
      return {
        trList: [],
        leijiTime:false,
        leiji:false,
        timeText:''
      }
    },
    // beforeRouteEnter(to, from, next) {
    //   if (from.name == 'saleStatistics') {
    //       document.body.scrollTop = 0;
    //       document.documentElement.scrollTop = 0;
    //   }
    //   next();
    // },
    created() {
      document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
      this.leiji=this.$route.query.leiji
      this.leijiTime=this.$route.query.leijiTime
      this.timeText=this.$route.query.timeText
      console.log(this.timeText,'this.timeText')
      this.trList = JSON.parse(sessionStorage.getItem('hieTrList'))
      console.log(JSON.parse(sessionStorage.getItem('hieTrList')))
    },
    computed: {
    //   options() {
    //     return {
    //       pullUpLoad: this.pullUpLoadObj,
    //       scrollbar: true
    //     }
    //   },
    },
   
    methods: {
      //     onPullingUp() {
      //     // 更新数据
      //     if (this.pageCount >= this.page) {
      //       this.getList(this.page)
      //     } else {
      //       this.$refs.scroll.forceUpdate()
      //     }
      //   },
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
    padding-bottom: 80px;

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

</style>
