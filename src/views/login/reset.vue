<template>
  <div class="reset-page" v-wechat-title="$route.meta.title" img-set="./static/img/favicon.ico">
    <div class="content">
      <div class="logo" v-show="false">
        <!-- <img src="../../assets/img/nfys_logo.png" /> -->
      </div>
      <div class="item-input">
        <input type="tel" v-model="moblie" maxlength="11" placeholder="请输入手机号码" />
        <div class="clear-logo" v-show="moblie" @click="moblie=''">
          <img src="../../assets/img/clear_input.png" />
        </div>
        <div
          class="sendMessage"
          :class="{hadsend:randomCodeNumState}"
          @click="timeStart()"
        >{{randomContent}}</div>
      </div>
      <div class="item-input">
        <input type="tel" v-model="codeMessage" maxlength="6" placeholder="请输入验证码" />
        <div class="clear-logo" v-show="codeMessage" @click="codeMessage=''">
          <img src="../../assets/img/clear_input.png" />
        </div>
        <!-- <div class="eye-logo" @click="changeType()">
          <img :src="openeye" />
        </div>-->
      </div>

      <div class="item-input">
        <input :type="pwdType" v-model="password" maxlength="12" placeholder="请输入密码(6-12位字母/数字/符号)" />
        <div class="clear-logo" v-show="password" @click="password=''">
          <img src="../../assets/img/clear_input.png" />
        </div>
        <div class="eye-logo" @click="changeType()">
          <img :src="openeye" />
        </div>
      </div>

      <div class="buttom-box">
        <span @click="resetPassword" :class="{disableC:!codeMessage||!moblie||!password}">重置密码</span>
      </div>
      <div class="reset-register">
        <span @click="tapToOther('/loginBypassword')">返回登录</span>
      </div>
    </div>
    <!-- <div class="other-login">
      <span @click="tapToOther('/loginBypassword')">账号密码登录</span>
    </div>-->
  </div>
</template>

<script>
import Vue from "vue";
import { Toast } from "vant";
export default {
  name: "reset",
  data() {
    return {
      openeye: require("@/assets/img/hide_input_btn.png"),
      moblie: "",
      codeMessage: "",
      pwdType: "password",
      password: "",
      randomCodeNumState: false,
      randomContent: "获取验证码",
      timer: null
    };
  },
  components: {},
  methods: {
    changeType() {
      //是否明文看密码
      this.pwdType = this.pwdType === "password" ? "text" : "password";
      this.openeye =
        this.openeye == require("@/assets/img/display_input_btn.png")
          ? require("@/assets/img/hide_input_btn.png")
          : require("@/assets/img/display_input_btn.png");
    },
    timeStart() {
      if (!this.moblie) {
        Toast("请填写手机号码");
        return false;
      }
      if (!/^1[3456789]\d{9}$/.test(this.moblie)) {
        Toast("请输入正确的手机号码");
        return false;
      }
      if (this.randomCodeNumState) {
        return false;
      }
      var _this = this;
      var TIME_COUNT = 60;

      this.$common.sendMesForReg("", 3, this.moblie).then(e => {
        if (e) {
          if (!_this.timer) {
            _this.count = TIME_COUNT;
            _this.timer = setInterval(function() {
              if (_this.count > 0 && _this.count <= TIME_COUNT) {
                _this.randomCodeNumState = true;
                _this.count--;
                _this.randomContent = _this.count + "s后重发";
              } else {
                clearInterval(_this.timer);
                _this.timer = null;
                _this.randomContent = "获取验证码";
                _this.randomCodeNumState = false;
              }
            }, 1000);
          }
        }
      });
    },
    tapToOther(routerName) {
      this.$router.back();
    },
    resetPassword() {
      if (!this.moblie) {
        Toast("请输入手机号码");
        return false;
      }
      if (!/^1[3456789]\d{9}$/.test(this.moblie)) {
        Toast("请输入正确的手机号码");
        return false;
      }
      if (!this.codeMessage) {
        Toast("请输入验证码");
        return false;
      }
      if (!this.password) {
        Toast("请输入密码");
        return false;
      }
      if (
        !/^(?![0-9]+$)(?![a-z]+$)(?![A-Z]+$)(?!([^(0-9a-zA-Z)])+$)^.{6,12}$/.test(
          this.password
        )
      ) {
        Toast("密码规则为6-12位的字母/数字/符号组合");
        return false;
      }
      let params = {
        header: {},
        body: {
          phone: this.moblie,
          code: this.codeMessage,
          password: this.password
        }
      };
      this.$api.resetPassword(params).then(res => {
        console.log(res);
        if (res.code == 0) {
          Toast("密码重置成功");
          setTimeout(() => {
            this.$router.back();
          }, 1500);
        } else {
          Toast(res.message);
        }
      });
    }
  },
  mounted() {},
  activated() {}
};
</script>
<style lang="less">
.reset-page {
  width: 100%;
  height: 100vh;
  background-color: #fff;
  position: relative;
  .content {
    width: 100%;
    display: flex;
    align-items: center;
    flex-direction: column;
    & .logo {
      width: 160px;
      height: 160px;
      font-size: 0;
      padding-top: 80px;
      padding-bottom: 101px;
    }
    & .item-input {
      width: 630px;
      height: 88px;
      background-color: #ffffff;
      box-shadow: 0px 1px 0px 0px #ebebeb;
      display: flex;
      align-items: center;
      & input {
        height: 34px;
        line-height: 34px;
        flex-grow: 2;
        color: #333;
        padding-left: 3px;
      }
      & > div:nth-child(2) {
        flex-shrink: 0;
        width: 20px;
        height: 20px;
        font-size: 0;
        padding: 10px;
        padding-right: 32px;
      }
      & div:nth-child(3) {
        flex-shrink: 0;
        padding-left: 9px;
        padding-right: 0;
        width: 137px;
        text-align: right;
        height: 44px;
        font-size: 28px;
        white-space: nowrap;
        line-height: 44px;
        color: #3ac756;
      }
      & .hadsend:nth-child(3) {
        color: #a8a8a8;
      }
    }
    & .item-input:nth-child(2) {
      padding-top: 80px;
    }
    & .item-input:nth-child(3) {
      padding-top: 21px;
      margin-top: 1px;
    }
    & .item-input:nth-child(4) {
      padding-top: 21px;
      margin-top: 1px;
      & > div:nth-child(2) {
        flex-shrink: 0;
        width: 20px;
        height: 20px;
        font-size: 0;
        padding: 10px;
        padding-right: 32px;
      }
      & > div:nth-child(3) {
        flex-shrink: 0;
        padding-left: 8px;
        padding-right: 20px;
        width: 44px;
        height: 44px;
        font-size: 0;
      }
    }

    & .buttom-box {
      width: 630px;
      height: 72px;
      margin-top: 60px;
      & span {
        background-color: #3ac756;
        font-size: 32px;
        line-height: 72px;
        border-radius: 36px;
        color: #ffffff;
        width: 100%;
        display: block;
        text-align: center;
      }
      & .disableC {
        opacity: 0.5;
      }
    }
    & .reset-register {
      margin-top: 30px;
      display: flex;
      align-items: center;
      & span {
        line-height: 46px;
        padding: 0 10px;
        font-size: 28px;
        color: #333333;
      }
      & > span:nth-child(2) {
        font-size: 36px;
      }
    }
  }

  .other-login {
    width: 100%;
    height: 64px;
    text-align: center;
    font-size: 0;
    position: absolute;
    bottom: 60px;
    left: 0;
    & span {
      width: 220px;
      height: 64px;
      background-color: #fafafa;
      border-radius: 32px;
      display: inline-block;
      font-size: 28px;
      line-height: 64px;
      color: #333333;
      text-align: center;
    }
  }
}
</style>
