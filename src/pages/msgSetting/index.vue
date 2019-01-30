<template>
  <div class="msgSetting">
    <div class="top">
      <div class="left">
        <b>当前登录微信昵称 {{weXinName}}</b>
        <p><span>已绑定微信</span><span class="weXinName">{{WeChatName!=''?WeChatName:'空'}}</span></p>
      </div>
      <div class="right">
        <button type="button" v-if='WeChatName==""' @click="bindClick()">立即绑定</button>
        <button type="button" class="replace" v-if='WeChatName!=""' @click="replaceClick()">替换绑定</button>
      </div>
    </div>
    <div class="msgContent">
      <h5>设置消息接受状态</h5>
      <ul>
        <li v-if='AccessId==5||AccessId==4||AccessId==3'>
          <p>新建家装公司</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsCompanyMsg}"  @click="toggle(1)">
            <i class="si" v-if='IsCompanyMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li v-if='AccessId==3||AccessId==4'>
          <p>新建小组</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsGroupMsg}"  @click="toggle(2)">
            <i class="si" v-if='IsGroupMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li>
          <p>授权审批</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsAuthMsg}"  @click="toggle(3)">
            <i class="si" v-if='IsAuthMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li>
          <p>合同审批</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsContractMsg}"  @click="toggle(4)">
            <i class="si" v-if='IsContractMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li>
          <p>申诉审批</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsStateMsg}"  @click="toggle(5)">
            <i class="si" v-if='IsStateMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li v-if='AccessId!=3'>
          <p>续签审批</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsRenewMsg}"  @click="toggle(6)">
            <i class="si" v-if='IsRenewMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li v-if='AccessId==5'>
          <p>合同到期</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsContractExpireMsg}" 
            @click="toggle(7)">
            <i class="si" v-if='IsContractExpireMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li v-if='AccessId==5'>
          <p>公司评级</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsCompanyGradeMsg}"  @click="toggle(8)">
            <i class="si" v-if='IsCompanyGradeMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
        <li>
          <p>公告通知</p>
          <span class="weui-switch" :class="{'weui-switch-on' : IsNoticeMsg}"  @click="toggle(9)">
            <i class="si" v-if='IsNoticeMsg'>{{yesText}}</i>
            <i class="fou" v-else>{{noText}}</i>
          </span>
        </li>
      </ul>
    </div>
    <div class="msgMask" v-if='bindFlag'>
      <div class="maskContent">
        <div class="box">
          <p>绑定后，该账号相关消息推送都将发送到该微信中！</p>
          <div class="msgBtn">
            <button type="button" class="noBind" @click='noBindClick()'>否</button>
            <button type="button" class="binding" @click='msgSetting()'>是</button>
          </div>
        </div>
      </div>
    </div>
    <!-- 替换绑定 -->
    <div class="msgMask" v-if='replaceFlag'>
      <div class="maskContent">
        <div class="box">
          <p>替换绑定后，该账号相关消息推送都将发送到该微信中！</p>
          <div class="msgBtn">
            <button type="button" class="noBind" @click='noReplace()'>否</button>
            <button type="button" class="binding" @click='replace()'>是</button>
          </div>
        </div>
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
    name: 'MsgSetting',
    data() {
      return {
        IsCompanyMsg: false,
        IsGroupMsg: false,
        IsAuthMsg: false,
        IsContractMsg: false,
        IsStateMsg: false,
        IsRenewMsg: false,
        IsContractExpireMsg: false,
        IsCompanyGradeMsg: false,
        IsNoticeMsg: false,
        value: true,
        yesText: '开',
        noText: '关',
        weXinName: '',
        WeChatName: '',
        bindFlag: false,
        replaceFlag: false,

      }
    },
    computed: {
      ...mapGetters([
        'AccessId'
      ])
    },
    created() {
      this.weXinConfig()
      this.weXinName = sessionStorage.getItem('WeChatName')
      console.log(this.weXinName)
    },
    methods: {
      // wexin配置
      weXinConfig() {
        axios({
          url: this.getHost() + '/UserInfo/GetWeChatMsgConfig',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token')
          })
        }).then(res => {
          console.log(res)
          this.IsAuthMsg = res.data.Data.IsAuthMsg
          this.IsCompanyGradeMsg = res.data.Data.IsCompanyGradeMsg
          this.IsCompanyMsg = res.data.Data.IsCompanyMsg
          this.IsContractExpireMsg = res.data.Data.IsContractExpireMsg
          this.IsContractMsg = res.data.Data.IsContractMsg
          this.IsGroupMsg = res.data.Data.IsGroupMsg
          this.IsNoticeMsg = res.data.Data.IsNoticeMsg
          this.IsRenewMsg = res.data.Data.IsRenewMsg
          this.IsStateMsg = res.data.Data.IsStateMsg
          this.WeChatName = res.data.Data.WeChatName

        })
      },
      // 替换绑定
      replaceClick() {
        this.replaceFlag = true
      },
      // 是
      replace() {
        this.msgSetting()
        this.replaceFlag = false
      },
      // 否
      noReplace() {
        this.replaceFlag = false
      },
      // 立即绑定
      bindClick() {
        console.log(this.bindFlag)
        this.bindFlag = true
      },
      // 否
      noBindClick() {
        this.bindFlag = false
      },
      // 是
      msgSetting() {
        axios({
          url: this.getHost() + '/UserInfo/SaveUserWx',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            OpenID: sessionStorage.getItem('OpenID'),
            WeChatName: sessionStorage.getItem('WeChatName'),
            HeadImage: sessionStorage.getItem('HeadImage')
          })
        }).then(res => {
          if (res.data.Status == 1) {
            this.weXinConfig()
            this.bindFlag = false
          }
        })
      },
      // 消息设置
      settingMsg() {
        axios({
          url: this.getHost() + '/UserInfo/WeChatMsgEdit',
          method: 'post',
          data: qs.stringify({
            UserId: getCookie('UserId'),
            token: getCookie('token'),
            IsCompanyMsg: this.IsCompanyMsg,
            IsGroupMsg: this.IsGroupMsg,
            IsAuthMsg: this.IsAuthMsg,
            IsContractMsg: this.IsContractMsg,
            IsContractExpireMsg: this.IsContractExpireMsg,
            IsCompanyGradeMsg: this.IsCompanyGradeMsg,
            IsNoticeMsg: this.IsNoticeMsg,
            IsRenewMsg: this.IsRenewMsg,
            IsStateMsg: this.IsStateMsg,

          })
        }).then(res => {
          console.log(res)
        })
      },
      toggle(num) {
        if (num == 1) {
          this.IsCompanyMsg = !this.IsCompanyMsg;

        }
        if (num == 2) {
          this.IsGroupMsg = !this.IsGroupMsg;

        }
        if (num == 3) {
          this.IsAuthMsg = !this.IsAuthMsg;

        }
        if (num == 4) {
          this.IsContractMsg = !this.IsContractMsg;

        }
        if (num == 5) {
          this.IsStateMsg = !this.IsStateMsg;

        }
        if (num == 6) {
          this.IsRenewMsg = !this.IsRenewMsg;

        }

        if (num == 7) {
          this.IsContractExpireMsg = !this.IsContractExpireMsg;

        }
        if (num == 8) {
          this.IsCompanyGradeMsg = !this.IsCompanyGradeMsg;
        }

        if (num == 9) {
          this.IsNoticeMsg = !this.IsNoticeMsg;
        }
        this.settingMsg()




      },
    }

  }

</script>
<style scoped>
  .top {
    display: flex;
    justify-content: space-between;
    background: white;
    margin-bottom: 4px;

  }

  .left {
    padding: 20px 0;
    padding-left: 24px;
  }

  .right {
    padding: 20px 0;
    padding-right: 24px;
  }

  .left b {
    display: block;
    color: #4D4D4D;
    font-size: 14px;
    padding-bottom: 10px;

  }

  .left p {
    color: #6E6E6E;
    font-size: 12px;
  }

  .left .weXinName {
    color: #F26F53;
    margin-left: 4px;
  }

  .right {

    height: 100%;

  }

  .right button {
    display: block;
    padding: 0 24px;
    line-height: 28px;
    height: 28px;
    border-radius: 4px;
    background: #F26F53;
    color: #F5F5F5;
    font-size: 11px;

  }

  .right button.replace {
    background: #E2C78F;
  }

  .msgContent {
    background: white;
    width: 100%;
    padding-bottom: 80px;
  }

  h5 {
    color: #404040;
    font-size: 20px;
    padding: 22px 0 2px 24px;
    font-weight: 550;
  }

  li {
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid #F5F5F5;
    padding-bottom: 20px;
    padding-top: 22px;
  }

  li p {
    font-size: 15px;
    color: #404040;
    padding-left: 26px;
    line-height: 30px;

  }

  .weui-switch {
    display: block;
    position: relative;
    width: 72px;
    height: 32px;
    border: 1px solid #DFDFDF;
    outline: 0;
    border-radius: 16px;
    box-sizing: border-box;
    background-color: #DFDFDF;
    transition: background-color 0.1s, border 0.1s;
    cursor: pointer;
    margin-right: 16px;
  }

  .weui-switch:before {
    content: " ";
    position: absolute;
    top: 0;
    left: 0;
    width: 70px;
    height: 30px;
    border-radius: 15px;
    background-color: #FDFDFD;
    transition: transform 0.35s cubic-bezier(0.45, 1, 0.4, 1);
  }

  .weui-switch:after {
    content: " ";
    position: absolute;
    top: -1px;
    left: 0;
    width: 32px;
    height: 32px;
    border-radius: 15px;
    background-color: #FFFFFF;
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.4);
    transition: transform 0.35s cubic-bezier(0.4, 0.4, 0.25, 1.35);
  }

  .weui-switch-on {
    border-color: #d5ae62;
    background-color: #d5ae62;
  }

  .weui-switch-on:before {
    border-color: #d5ae62;
    background-color: #d5ae62;
  }

  .weui-switch-on:after {
    transform: translateX(40px);
  }

  .si {
    position: absolute;
    top: 0;
    left: 12px;
    color: white;
    font-style: normal;
    font-size: 16px;
    line-height: 30px;
  }

  .fou {
    position: absolute;
    top: 0;
    right: 12px;
    color: #CCCCCC;
    font-style: normal;
    font-size: 16px;
    line-height: 30px;
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

  .maskContent {
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

  .maskContent h5 {
    font-size: 18px;
    color: #404040;
    text-align: center;
    font-weight: 550;
    margin-bottom: 22px;
  }

  .maskContent p {
    font-size: 16px;
    color: #5E5E5E;
    line-height: 20px;
    margin-bottom: 20px;
  }

  .maskContent i {
    font-size: 10px;
    color: #989898;
  }

  .maskContent button {
    border: none;
    background: none;
    display: block;
    font-size: 13px;
    line-height: 34px;
    border-radius: 2px;
  }

  .msgBtn {
    width: 100%;
    margin: 0 auto;
    display: flex;
    justify-content: space-between;
  }

  .msgBtn .noBind {
    color: #989898;
    background: #EDECEB;
    padding: 0 40px;

  }

  .msgBtn .binding {
    color: white;
    background: #D7B878;
    padding: 0 56px;

  }

</style>
