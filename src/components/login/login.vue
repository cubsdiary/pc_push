<template>
  <div  id="mask-login">
    <div class="login">
      <transition name="err">
			  <span class="mask-mess" v-if="errshow">{{errmessage}}</span>
      </transition>
			<div class="mask-head">
				<div class="mask-title">登录酷酷</div>
				<div class="mask-close" @click="closeMask"></div>
			</div>

			<div class="mask-phone">
				<!-- <h2>请输入手机号码</h2> -->
				<input type="text" value="" v-model="input1" placeholder="请输入手机号码"/>
			</div>
			<div class="mask-pass">
				<!-- <h2>请输入密码</h2> -->
				<input type="password" value="" v-model="input2" placeholder="请输入密码"/>
			</div>
			<div class="mask-btn" @click="loginBtn">登录</div>
      <h3>未注册？  <a href="javascript:;" @click="regShow">立即注册</a> </h3>
		</div>
  </div>
</template>

<script type="text/ecmascript-6">
  const ERR_OK = 0;

  export default {
    props: {
      loginState: {
        type: Boolean
      },
      registerState: {
        type: Boolean
      }
    },
    data () {
      return {
        input1: null,
        input2: null,
        phone: null,
        password: null,
        errshow: false,
        errmessage: '',
        falg: true
      };
    },
    methods: {
      closeMask () {
        // this.loginState = false;
        this.$emit('getloginshow', [false, false]);
      },
      regShow () {
        this.$emit('getloginshow', [false, true]);
      },
      loginBtn () {
        this.phone = this.input1;
        this.password = this.input2;

        if (this.phone === null || this.phone === '') {
          this.errmessage = '请输入手机号码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.falg = false;
        }
        if (this.password === null || this.password === '') {
          this.errmessage = '请输入登录密码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.falg = false;
        }
        if (this.falg) {
          this.$http.get(
            'http://192.168.0.103:8080/kukuzhibo/pc/login.do',
            {
              params: {
                account: this.phone,
                password: this.password
              }
            })
            .then((response) => {
              response = response.body;
              console.log(response);
              if (response.state === '1') {
                this.$emit('getlogininfo', response);
                sessionStorage.setItem('nick_name', response.nick_name);
                sessionStorage.setItem('head_img', response.head_img);
                sessionStorage.setItem('userid', response.userid);
                sessionStorage.setItem('state', response.state);
                sessionStorage.setItem('zhubo', response.zhubo);
              } else if (response.state === '0') {
                this.errmessage = response.message;
                this.errshow = true;
                setTimeout(() => {
                  this.errshow = false;
                }, 1);
              } else if (response.state === '-1') {
                this.errmessage = response.message;
                this.errshow = true;
                setTimeout(() => {
                  this.errshow = false;
                }, 1);
              }
            });
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  #mask-login
    position: fixed
    left: 0
    top: 0
    width: 100%
    height: 100%
    background: rgba(0, 0, 0, 0.5)
    z-index: 100000
    .login
      position: absolute
      left: 0
      right: 0
      top: 0
      bottom: 0
      margin: auto
      width: 500px
      height: 300px
      background: #ffffff
      border-radius: 8px
      border: 4px solid rgba(0,0,0,0.3)
      .mask-head
        width: 100%
        height: 60px
        position: relative
        .mask-title
          height:60px
          line-height: 60px
          text-align: center
          font-size: 18px
          color: #666666
      .mask-close
        position: absolute
        top: 20px
        right: 20px
        width: 15px
        height: 15px
        background: url(../../common/img/close.png) no-repeat center center
        background-size: 100% 100%
        float: right
        cursor: pointer
      .mask-mess
        display: inline-block
        position: absolute
        left: 0
        right: 0
        top: 0
        bottom: 0
        margin: auto
        width: 300px
        height:40px
        border-radius: 4px
        text-align: center
        font-size: 14px
        line-height: 40px
        color: #ffffff
        background-color: rgba(255, 0, 0, 0.6)
        opacity: 1
        &.err-enter-active {
          transition: all 1s;
        }
        &.err-leave-active {
          transition: all 1s;
        }
        &.err-enter-to, &.err-leave{
          opacity: 1
        }
      .mask-phone,.mask-pass
        width:300px
        height: 40px
        margin: 10px auto
        border-radius: 5px
        overflow: hidden
        box-shadow:0 0 5px #ccc
        input
          box-sizing: border-box
          padding-left:15px
          width: 180px
          height: 40px
          float: left
          border: none
          outline: none
          font-size: 14px
      .mask-btn
        width: 300px
        height: 40px
        background-color: rgba(102,193,98,0.8)
        text-align: center
        line-height: 40px
        margin: 25px auto
        color: #ffffff
        border-radius: 4px
        cursor: pointer
      h3
        text-align: center
        font-size: 12px
        line-height: 12px
        a
          color: rgba(102,193,98,0.8)
</style>
