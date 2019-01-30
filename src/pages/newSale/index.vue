<!-- 待续签 -->
<template>
    <div class="newSale">
      <div class="container">
        <h5>新增公司业务员排行</h5>
        <!-- <p>小程序端最多显示前50名排名，PC端可查看所有公司排行</p> -->
        <table>
          <thead>
            <tr>
              <td>名下排行</td>
              <td>分公司排行</td>
              <td>业务员</td>
              <td>新增洽谈中</td>
              <td>新增签约中</td>
              <td>新增签约</td>
            </tr>
          </thead>
          <tbody>
            <tr v-for='(item,index) in trList' :key='index' >
              <td><span>{{item.NewRn}}</span></td>
                    <td><span>{{item.Rn}}</span></td>
                    <td><span>{{item.Name}}</span></td>
                    <td><span>{{item.QTCount}}</span></td>
                    <td><span>{{item.QYCount}}</span></td>
                    <td><span>{{item.YQYCount}}</span></td>
            </tr>
          </tbody>
        </table>
       
      </div>
    </div>
  </template>
  <script>
    export default {
      name: 'NewSale',
      data() {
        return {
          trList: []
        }
      },
    //   beforeRouteEnter(to, from, next) {
    //   if (from.name == 'businessStatistics') {
    //       document.body.scrollTop = 0;
    //       document.documentElement.scrollTop = 0;
    //   }
    //   next();
    // },
      created() {
        document.body.scrollTop = 0;
      document.documentElement.scrollTop = 0;
        this.trList = JSON.parse(sessionStorage.getItem('newSaleTrList'))
        console.log(JSON.parse(sessionStorage.getItem('newSaleTrList')))
      },
      methods: {
       
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
  