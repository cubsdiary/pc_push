<template>
  <div>
    <div id="header">
      <div class="header">
        <div class="logo">
  				<div class="logo-icon"><img src="../../common/img/logo@2x.png" alt="" /></div>
  				<div class="logo-font"><img src="../../common/img/logofont@2x.png" alt=""/></div>
  			</div>
  			<div class="login">
          <a href="#" @click.stop.prevent="showLogin" v-show="loginInfo.state!=='1'">登录</a>
          <div class="userinfo" v-show="loginInfo.state==='1'">
            <div class="headimg">
              <img v-show="String(loginInfo.head_img)!=='null'" :src="'http://app.kukuwangluo.com/kkzb/download?id='+loginInfo.head_img" alt="">
              <img v-show="String(loginInfo.head_img)==='null'" src="../../common/img/headimg.png" alt="">
            </div>
            <div class="nick-name">{{loginInfo.nick_name}}</div>
            <div class="go-out" @click="goOut">退出登录</div>
          </div>
  			</div>
      </div>
    </div>
    <div class="box"></div>
    <login v-show="loginState" @getloginshow="getLoginShow" :login-state="loginState" @getlogininfo="getLoginInfo"></login>
    <register v-show="registerState" :register-state="registerState" @getregistershow="getRegisterShow" ></register>
  </div>

</template>

<script type="text/ecmascript-6">
  import login from '../../components/login/login.vue';
  import register from '../../components/register/register.vue';
  export default {
    data () {
      return {
        loginState: false,
        registerState: false,
        loginInfo: {
          nick_name: null,
          head_img: null,
          userid: null,
          state: null,
          zhubo: null
        },
        errMessage: null
      };
    },
    created () {
      this.loginInfo.nick_name = sessionStorage.getItem('nick_name');
      this.loginInfo.head_img = sessionStorage.getItem('head_img');
      this.loginInfo.userid = sessionStorage.getItem('userid');
      this.loginInfo.state = sessionStorage.getItem('state');
      this.loginInfo.zhubo = sessionStorage.getItem('zhubo');
      this.$emit('zhuboinfo', [this.loginInfo.zhubo, this.loginInfo.userid]);
      console.log(this.loginInfo);
    },
    methods: {
      showLogin () {
        this.loginState = true;
      },
      hideLogin () {
        this.loginState = false;
      },
      showRegister () {
        this.reigsterState = true;
      },
      hideRegister () {
        this.reigsterState = false;
      },
      getRegisterShow (arr) {
        this.registerState = arr[0];
        if (arr[1] === true) {
          this.loginState = arr[1];
        }
      },
      getLoginShow (arr) {
        this.loginState = arr[0];
        if (arr[1] === true) {
          this.registerState = arr[1];
        }
      },
      getLoginInfo (data) {
        console.log(data.nick_name);
        this.$emit('zhuboinfo', [data.zhubo, data.userid]);
        if (data.state === '1') {
          this.loginState = false;
        } else {
          this.loginState = true;
        }
        this.loginInfo = data;
      },
      goOut () {
        sessionStorage.clear();
        this.loginInfo.state = '0';
        this.$emit('zhuboinfo', [null, null]);
      }
    },
    components: {
      login,
      register
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  #header
    position: fixed
    top: 0
    width: 100%
    height: 90px
    border-bottom: 1px solid #ECF0F1
    box-shadow: 0 0 2px 0 #ECF0F1
    background-color: #ffffff
    z-index: 100
    .header
      width: 1100px
      height: 90px
      margin: 0 auto
      overflow: hidden
      line-height: 90px
      .logo
        width: 230px
        height: 60px
        float: left
        margin-top: 15px
        .logo-icon
          width: 60px
          height: 60px
          float: left
          img
            display: block
            width:100%
            height:100%
        .logo-font
          width: 156px
          height: 26px
          margin-top: 17px
          float: right
          img
            display: block
            width:100%
            height:100%
      .login
        float: right
        & > a
          font-size: 16px
          padding: 8px 15px
          &:hover
            color: #fff
            background-color: #ccc
            border-radius: 3px
        .userinfo
          width: 220px
          height: 60px
          float: right
          margin-top: 15px
          .headimg
            width: 60px
            height: 60px
            border-radius: 50%
            overflow: hidden
            float: left
            margin-right: 20px
            img
              display: block
              width: 60px
              height: 60px
          .nick-name
            width: 140px
            height: 30px
            line-height: 30px
            font-size: 14px
            float: right
            text-align: center
            white-space: nowrap
            overflow: hidden
            text-overflow: ellipsis
          .go-out
            width: 130px
            height: 30px
            text-align: center
            line-height: 30px
            border-radius: 4px
            float: right
            margin-right: 5px
            cursor: pointer
            font-size: 14px
            &:hover
              background: #ccc
  .box
    width: 100%
    height: 91px
</style>
