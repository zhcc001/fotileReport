<!-- 销额公司排行 -->
<template>
  <div class="distriRanking">
    <div class="container">
      <h5>年累计销额经销商排行</h5>
      <!-- <p>小程序端最多显示前50名排名，PC端可查看所有公司排行</p> -->
      <!-- <div class="scroll-list-wrap">
            <cube-scroll ref="scroll" :data="trList" :options="options" @pulling-up="onPullingDown"> -->
      <table>
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
         
          <tr v-for='(item,index) in trList' :key='index'>
            <td><span>{{item.Rn}}</span></td>
                <td><span>{{item.Name}}</span></td>
                <td><span>{{item.CompanyCount}}</span></td>
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
    name: 'DistriRanking',
    data() {
      return {
        trList: []
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
      this.trList = JSON.parse(sessionStorage.getItem('disTrList'))
      console.log(JSON.parse(sessionStorage.getItem('disTrList')))
    },
    computed:{
    //     options() {
    //     return {
    //       pullUpLoad: this.pullUpLoadObj,
    //       scrollbar: true
    //     }
    //   },
    },
    methods:{
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
  table tr td:nth-child(2){
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
