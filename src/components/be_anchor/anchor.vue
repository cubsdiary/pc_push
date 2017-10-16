<template>
  <div class="anchor">
    <div id="anchor">
  		<form id="upload"  enctype="multipart/form-data" >
        <div class="errmessage" v-show="!flag">{{errmessage}}</div>
  			<label for="realname" class="realname">真实姓名：</label><input type="text" id="realname" value="" v-model="realname" />
  			<br />
  			<label for="phone" class="phone">手机号码：</label><input type="text" id="phone" value="" v-model="phone" />
  			<br />
  			<label for="cardno" class="cardno">身份证号：</label><input type="text" id="cardno" value="" v-model="cardno" />
  			<br />
  			<div class="upload-img">
  				<input type="file" class="file" id="file_1" name="img1" ref="card_z" @change="changeImage_1()" accept="image/gif,image/jpeg,image/jpg,image/png"/>
  				<label for="file_1" id="label_1">+<img class="showPic" src="../../common/img/headimg.png" ref="img_1"/></label>
  		   	<input type="file" class="file" id="file_2" name="img2" ref="card_f" @change="changeImage_2()" accept="image/gif,image/jpeg,image/jpg,image/png"/>
  		    <label for="file_2"  id="label_2">+<img class="showPic" src="../../common/img/headimg.png" ref="img_2"/></label>

  		    <input type="file" class="file" id="file_3" name="img3" ref="handphoto" @change="changeImage_3()" accept="image/gif,image/jpeg,image/jpg,image/png"/>
  		    <label for="file_3"  id="label_3">+<img class="showPic" src="../../common/img/headimg.png" ref="img_3"/></label>
          <div class="img-info">
          	<div>身份证正面</div>
          	<div>身份证反面</div>
          	<div>手持身份证</div>
          </div>
  		   </div>
  		   <br />
  	   	<div class="agreement">
  	   		<input type="checkbox" id="myCheck" v-model="agreement" ><label for="myCheck"></label><a href="agreement.html" target="_blank">我已阅读主播协议</a>
  	   	</div>
  		  <div id="upload-submit" @click="uploadSubmit">酷酷认证</div>
  			<input type="submit"  id="submit" value="提交"/>
  		</form>
    </div>
  </div>

</template>

<script type="text/ecmascript-6">
  export default {
    data () {
      return {
        realname: null,
        phone: null,
        cardno: null,
        agreement: false,
        flag: true,
        errmessage: null
      };
    },
    methods: {
      uploadSubmit () {
        this.flag = true;
        if (this.realname === null || this.realname === '') {
          this.flag = false;
          this.errmessage = '请输入身份证姓名';
          return;
        }
        if (this.phone === null || this.phone === '') {
          this.flag = false;
          this.errmessage = '请输入注册手机号码';
          return;
        }
        if (this.cardno === null || this.cardno === '') {
          this.flag = false;
          this.errmessage = '请输入身份证号码';
          return;
        }
        if (this.$refs.card_z.files.length === 0) {
          this.flag = false;
          this.errmessage = '请上传身份证正面照';
          return;
        }
        if (this.$refs.card_f.files.length === 0) {
          this.flag = false;
          this.errmessage = '请上传身份证反面照';
          return;
        }
        if (this.$refs.handphoto.files.length === 0) {
          this.flag = false;
          this.errmessage = '请上传手持身份证照';
          return;
        }
        if (this.agreement === false) {
          this.flag = false;
          this.errmessage = '请选择阅读主播协议';
          return;
        }
        var formData = new FormData();
        console.log(this.$refs.card_z.files[0]);
        console.log(this.$refs.card_f.files[0]);
        console.log(this.$refs.handphoto.files[0]);
        formData.append('card_z', this.$refs.card_z.files[0]);
        formData.append('card_f', this.$refs.card_f.files[0]);
        formData.append('handphoto', this.$refs.handphoto.files[0]);
        formData.append('realname', this.realname);
        formData.append('phone', this.phone);
        formData.append('cardno', this.cardno);
        let config = {
          headers: {'Content-Type': 'multipart/form-data'}
        };
        if (this.flag) {
          this.$http.post('http://192.168.0.103:8080/kukuzhibo/pc/applyZhuBo.do', formData, config)
          .then((response) => {
            response = response.body;
            console.log(response);
            if (response.state === '1') {
              this.$emit('getanchor', response);
            }
          });
        }
      },
      changeImage_1 () {
        var imgFile = this.$refs.card_z.files[0];
        var fr = new FileReader();
        var self = this;
        fr.onload = function () {
          self.$refs.img_1.style.display = 'block';
          self.$refs.img_1.src = fr.result;
        };
        fr.readAsDataURL(imgFile);
      },
      changeImage_2 () {
        var imgFile = this.$refs.card_f.files[0];
        var fr = new FileReader();
        var self = this;
        fr.onload = function () {
          self.$refs.img_2.style.display = 'block';
          self.$refs.img_2.src = fr.result;
        };
        fr.readAsDataURL(imgFile);
      },
      changeImage_3 () {
        var imgFile = this.$refs.handphoto.files[0];
        var fr = new FileReader();
        var self = this;
        fr.onload = function () {
          self.$refs.img_3.style.display = 'block';
          self.$refs.img_3.src = fr.result;
        };
        fr.readAsDataURL(imgFile);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .anchor
    width: 1100px
    height: 700px
  #anchor
    width: 1100px
    height: 600px
    #upload
      position: absolute
      top: 0
      bottom: 0
      left: 0
      right: 0
      margin: auto
      box-sizing: border-box
      width: 1100px
      height: 560px
      border: 1px solid #ccc
      border-radius: 10px
      box-shadow:0 0 10px #ccc
      padding: 30px
      .errmessage
        width: 1000px
        height: 40px
        line-height: 40px
        font-size: 12px
        text-align: center
        color: rgba(255, 0, 0, 0.5)
    #submit
      display: none
    #realname, #phone, #cardno
    	width:250px
    	height:30px
    	border:1px solid #ccc
    	border-radius: 4px
    	margin-top: 5px
    	margin: 10px 10px
    .realname, .phone, .cardno
    	color: #555555
    .upload-img
      margin-top: 5px
      overflow: hidden
      .file
        display: none
      #label_1, #label_2, #label_3
        position: relative
        width: 320px
        border: 1px dashed #ccc
        display: inline-block
        height: 180px
        line-height: 180px
        text-align: center
        font-size: 50px
        color: #ccc
        font-size: 100
        margin-right: 20px
        cursor: pointer
        border-radius: 5px
        overflow: hidden
        img
          display: none
          position: absolute
          top: 0
          left: 0
          width: 100%
          height: 100%
      .img-info
        div
        	width: 325px
        	height: 30px
        	line-height: 30px
        	text-align: center
        	float: left
        	margin-right:20px
        	color: #555555
    .agreement
      position: absolute
      bottom: 30px
      left: 100px
      width: 200px
      height: 40px
      line-height: 40px
      a
        color: #555555
      #myCheck
      	display: none
      label
        background-color: white
        border-radius: 15px
        border:1px solid #d3d3d3
        width:15px
        height:15px
        display: inline-block
        text-align: center
        vertical-align: middle
        line-height: 15px
        margin-right: 5px
        margin-top:-3px
        cursor: pointer
      #myCheck:checked + label:after
        content:"\2714"
        color: rgb(102,193,98)
    #upload-submit
    	width:130px
    	height:40px
    	text-align: center
    	line-height: 40px
    	border-radius: 5px
    	cursor: pointer
    	color: #ffffff
    	float: left
    	margin-left:485px
    	margin-top: 20px
    	background-color: rgba(102,193,98,0.8)

</style>
