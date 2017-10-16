<template>
  <div  id="mask-reg">
    <div class="reg">
      <transition name="err">
			  <span class="mask-mess" v-if="errshow">{{errmessage}}</span>
      </transition>
			<div class="mask-head">
				<div class="mask-title">注册酷酷</div>
				<div class="mask-close" @click="closeMask"></div>
			</div>

			<div class="mask-phone">
				<!-- <h2>请输入手机号码</h2> -->
				<input type="text" value="" v-model="input1" placeholder="请输入手机号"/>
        <div class="get-yzm" @click="touchdown && getYzm()">{{yzmmsg}}</div>
			</div>
      <div class="mask-yzm">
        <input type="text" value="" v-model="input3" placeholder="请输入验证码"/>
      </div>
			<div class="mask-pass">
				<!-- <h2>请输入密码</h2> -->
				<input type="password" value="" v-model="input2" placeholder="请输入（6-12）位字符"/>
			</div>
			<div class="mask-btn" @click="regBtn">注册</div>
      <h3>已注册？ <a href="javascript:;" @click="loginShow">立即登录</a> </h3>
		</div>
  </div>
</template>

<script type="text/ecmascript-6">
  const ERR_OK = 0;

  export default {
    props: {
      registerState: {
        type: Boolean
      }
    },
    data () {
      return {
        input1: null,
        input2: null,
        input3: null,
        yzm: null,
        errshow: false,
        errmessage: '',
        falg: true,
        loginfalg: true,
        timer: null,
        touchdown: true,
        yzmmsg: '获取验证码',
        yzmDom: document.getElementsByClassName('get-yzm')[0],
        colorYzm: '#ccc'
      };
    },
    methods: {
      closeMask () {
        // this.loginState = false;
        this.$emit('getregistershow', [false, false]);
      },
      loginShow () {
        this.$emit('getregistershow', [false, true]);
      },
      getYzm () {
        var i = 90;
        var self = this;
        if (this.phone === null || this.phone === '') {
          this.errmessage = '请输入手机号码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.falg = false;
        }
        if (this.falg) {
          var timer = setInterval(function () {
            if (i <= 0) {
              self.touchdown = true;
              self.yzmmsg = '获取验证码';
              self.$set([self.touchdown], 'touchdown', true);
              self.$set([self.yzmmsg], 'yzmmsg', '获取验证码');
              clearInterval(timer);
            } else {
              self.yzmmsg = `已获取${i}s`;
              self.$set([self.yzmmsg], 'yzmmsg', `已获取${i}s`);
              i--;
            }
          }, 1000);
          this.$http.get(
            'http://192.168.0.103:8080/kukuzhibo/pc/getMessage.do',
            {
              params: {
                account: this.input1
              }
            })
            .then((response) => {
              response = response.body;
              // console.log(response);
              if (response.mobile_code) {
                this.yzm = response.mobile_code;
                this.touchdown = false;
              } else {
                this.errmessage = response.msg;
                this.errshow = true;
                setTimeout(() => {
                  this.errshow = false;
                }, 1);
              }
            });
        }
      },
      regBtn () {
        if (this.input1 === null || this.input1 === '') {
          this.errmessage = '请输入手机号码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.loginfalg = false;
        }
        if (this.input3 === null || this.input3 === '') {
          this.errmessage = '请输入手机验证码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.falg = false;
        } else if (this.yzm !== Number(this.input3)) {
          this.errmessage = '手机验证码输入错误';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.loginfalg = false;
        }
        if (this.input2 === null || this.input2 === '') {
          this.errmessage = '请输入登录密码';
          this.errshow = true;
          setTimeout(() => {
            this.errshow = false;
          }, 1);
          this.loginfalg = false;
        }
        if (this.loginfalg) {
          this.$http.get(
            'http://www.kukuwangluo.com/kukuzhibo/pc/registered.do',
            {
              params: {
                account: this.input1,
                password: this.input2,
                mobile_code: this.input3
              }
            })
            .then((response) => {
              response = response.body;
              console.log(response);
              if (response.code === '10000') {
                this.$emit('getregistershow', [false, true]);
              } else if (response.code === '20000') {
                this.errmessage = response.msg;
                this.errshow = true;
                setTimeout(() => {
                  this.errshow = false;
                }, 1);
              } else if (response.code === '30000') {
                this.errmessage = response.msg;
                this.errshow = true;
                setTimeout(() => {
                  this.errshow = false;
                }, 1);
              } else if (response.code === '40000') {
                this.errmessage = response.msg;
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
#mask-reg
  position: fixed
  left: 0
  top: 0
  width: 100%
  height: 100%
  background: rgba(0, 0, 0, 0.5)
  z-index: 100000
  .reg
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
    .mask-phone
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
      .get-yzm
        width: 120px
        height: 40px
        float: right
        text-align: center
        background-color: rgba(102,193,98,0.8)
        font-size: 14px
        color: #fff
        line-height: 40px
        cursor: pointer
        &.coloryzm
          background-color: #ccc
    .mask-yzm,.mask-pass
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
      margin: 10px auto
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
