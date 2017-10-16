<template>
  <div class="livepush">
    <div id="livepush">
  		<div class="con-left">
  			<h2>直播推流</h2>
  			<div class="con-userid">酷酷号： <span v-show="userId!=='null'">{{userId}}</span></div>
  			<div class="con-title">
  				<span>直播标题：</span>
  				<input type="text" id="title" v-model="title" />
  				<span>（必填）</span>
  			</div>
  			<div class="con-choose">
  				<label >竖屏<input type="radio" name="con-type"  checked="checked"/><span></span></label>
  				<label >横屏<input type="radio" name="con-type" /><span></span></label>
  			</div>
  			<div class="con-adress">
  				<span>定位经纬度：</span>
  				<input type="text" id="adress" v-model="adress"/>
  				<span>（选填）格式如：116.46406,39.95687 <a href="http://lbs.amap.com/console/show/picker" target="_blank">地图定位</a></span>
  			</div>
  			<div class="start-push" @click="startBtn && startPush(1)" :class="{ btncolor : !startBtn }" >{{startInfo}}</div>
  			<div class="close-push" @click="overBtn && startPush(0)" :class="{ btncolor : !overBtn }" >{{overInfo}}</div>
  			<div class="obs-down-win" @click="obsDownWin">Win下载OBS</div>
  			<div class="obs-down-mac" @click="obsDownMac">Mac下载OBS</div>
  		</div>
  		<div class="message">
  			<h2>操作步骤：</h2>
  			<p>1.设置标题（必填）和方向（选填）;</p>
  			<p>2.点击"开播获取地址",复制地址;</p>
  			<p>3.在obs等推流工具中配置地址,即可在直播间观看到推送内容;</p>
  			<p>4.在推流结束后,点击"结束直播"完成直播,请务必及时结束直播,否则直播间将处于黑屏影响观看,并且会影响下次开播.</p>
  		</div>
  		<p class="push-adress" v-show="errMessage!==null">{{errMessage}}</p>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  export default {
    props: {
      userId: {
        type: String
      }
    },
    data () {
      return {
        errMessage: null,
        title: null,
        adress: null,
        roomname: null,
        lat: null,
        lng: null,
        adressArr: null,
        flag: false,
        startBtn: true,
        overBtn: true,
        startInfo: '获取推流地址',
        overInfo: '结束直播推流',
        pushAdress: null,
        pushStart: false
      };
    },
    created () {
    },
    methods: {
      startPush (on) {
        if (this.userId === null || this.userId === 'null') {
          this.errMessage = '请先登录';
          console.log(this.userId);
          return;
        } else if (on === 1) {
          this.roomname = this.title;
          this.adressArr = this.adress.split(',');
          this.lat = this.adressArr[1];
          this.lng = this.adressArr[0];
        } else if (on === 0) {
          this.roomname = null;
          this.lat = null;
          this.lng = null;
        }
        this.$http.get(
          'http://192.168.0.103:8080/kukuzhibo/pc/updateKF.do',
          {
            params: {
              userid: this.userId,
              lat: this.lat,
              lng: this.lng,
              roomname: this.roomname,
              online: on
            }
          })
          .then((response) => {
            response = response.body;
            console.log(response);
            if (on === 1) {
              if (response.code === '20000') {
                this.flag = true;
                this.startBtn = false;
                this.overBtn = true;
                this.startInfo = '推流已开启';
              } else if (response.code === '10000') {
                this.errMessage = response.msg;
              } else {
                this.errMessage = '推流开启失败请稍后重新开启';
              }
            } else if (on === 0) {
              if (response.code === '30000') {
                this.errMessage = response.msg;
                this.overBtn = false;
                this.startBtn = true;
              } else {
                this.errMessage = '结束推流失败请稍后进行操作';
              }
            }
          });
        this.$http.get(
            'http://www.kukuwangluo.com/kkzb/mobile?methodno=MCastRoomDetail&deviceid=pc&device=pc&id=' + this.userId + '&debug=1'
          )
          .then((response) => {
            response = response.body;
            console.log(response);
            this.errMessage = response.retnMessage.pushstream_;
          });
      },
      obsDownMac () {
        window.location.href = 'http://www.kukuwangluo.com/kuku/obs-mac-20.0.1-installer.pkg';
      },
      obsDownWin () {
        window.location.href = 'http://www.kukuwangluo.com/kuku/OBS-Studio-20.0.1-Full-Installer-windows.exe';
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .livepush
    width: 1100px
    height: 700px
  #livepush
    position: absolute
    top: 0
    left: 0
    bottom: 0
    right: 0
    margin: auto
    width: 100%;
    height: 500px
    overflow: hidden;
    font-family: "microsoft yahei"
    .message
      float: right
      padding: 30px
      margin-top: 20px
      width: 340px
      height: auto
      border-radius: 6px
      background: #f0f0f0
      h2
        line-height: 40px
        color: #444444
        font-size:16px
      p
        line-height: 26px
        color: #666666
        text-indent: 2em
    .con-left
      width:700px
      height:auto
      float: left
      & > h2
        line-height: 40px
        color: #444444
        font-size: 16px
      & > div
        color: #666666
        margin: 10px 0
      .con-userid
        height: 30px
	      line-height: 30px
      .con-title
        height: 40px
        line-height: 40px
        span
          float: left
        input
          float: left
          width: 250px
          height: 30px
          border: 1px solid gray
          border-radius: 4px
          margin-top: 5px
      .con-choose
        height:40px
        label
          display: inline-block
          margin-top: 5px
          position: relative
          margin-right: 100px
          cursor: pointer
          width: 60px
          height: 30px
          line-height: 30px
        input[type = "radio"]
          appearance: none
          -webkit-appearance: none
          outline: none
          display: none
        input[type = "radio"] + span
          position: absolute
          right: 0
          top: 5px
          width: 24px
          height: 20px
          display: inline-block
          background: url(../../common/img/inputradio.gif)  no-repeat
          background-position: -24px 0px
        input[type = "radio"]:checked + span
          background-position: 0 0
      .con-adress
        height: 40px
        line-height: 40px
        a
          color: rgba(102,193,98,0.8)
        span
          float: left
        input
          float: left
          width: 250px
          height: 30px
          border: 1px solid gray
          border-radius: 4px
          margin-top: 5px
      .start-push,.close-push,.obs-down-win,.obs-down-mac
        width: 130px
        height: 40px
        text-align: center
        line-height: 40px
        border-radius: 5px
        margin-top: 30px
        cursor: pointer
        color: #ffffff
        float: left
        margin-right: 30px
        background-color: rgba(102,193,98,0.8)
        &.btncolor
          background-color: rgba(200, 200, 200, 1)
    .push-adress
      float: left
      margin-top: 20px
      width: 1080px
      height: 40px
      padding: 10px
      background: #f0f0f0
      border-radius: 10px
      color: #666666
      line-height: 40px
      overflow: hidden
      white-space: nowrap
</style>
