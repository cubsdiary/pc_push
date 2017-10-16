<template>
  <div>
      <v-header @zhuboinfo="zhuBoInfo"></v-header>

      <div id="content">
        <div class="rz_info" v-show="anchorState==='1'">
          <div class="info_con">
            <img src="./common/img/rz_s.png" alt="">
            <span>{{anchorInfo}}</span>
          </div>
        </div>
        <livepush v-show="showLivepush" :user-id="userId"></livepush>
        <anchor v-show="showAnchor" @getanchor="getAnchor" class="anchor"></anchor>

      </div>
      <v-footer></v-footer>
  </div>
</template>

<script type="text/ecmascript-6">
  import header from './components/header/header.vue';
  import footer from './components/footer/footer.vue';
  import livepush from './components/livepush/livepush.vue';
  import anchor from './components/be_anchor/anchor.vue';

  export default {
    data () {
      return {
        zhuBo: null,
        userId: null,
        showAnchor: false,
        showLivepush: true,
        anchorInfo: null,
        anchorState: null
      };
    },
    methods: {
      zhuBoInfo (arr) {
        this.userId = String(arr[1]);
        this.$set([this.userId], 'userId', arr[1]);
        if (arr[0] === '0') {
          this.showAnchor = true;
          this.showLivepush = false;
        } else {
          this.showAnchor = false;
          this.showLivepush = true;
        }
      },
      getAnchor (res) {
        if (res.state === '1') {
          this.showAnchor = false;
          this.showLivepush = false;
          this.anchorInfo = res.msg;
          this.anchorState = res.state;
        }
        setTimeout(() => {
          this.anchorState = '0';
          this.showLivepush = true;
        }, 3200);
      }
    },
    components: {
      'v-header': header,
      'v-footer': footer,
      livepush,
      anchor
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    #content
      position: relative
      width: 1100px
      padding-top: 10px
      margin: 0 auto
      .rz_info
        position: relative
        width: 1100px
        height: 700px
        .info_con
          position: absolute
          left: 0
          right: 0
          top: 0
          bottom: 0
          margin: auto
          width: 460px
          height:100px
          img
            display: inline-block
            width: 123px
            height: 100px
            float: left
          span
            float: left
            line-height: 100px
            font-size: 20px
            color: #4CAF50
            margin-left: 50px
</style>
