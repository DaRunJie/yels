<template>
  <div class="v-header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" v-lazy="seller.avatar"/>
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="discription">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classList[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
      <img v-lazy="seller.avatar" width="100%" height="100%">
    </div>
    <transition name="fade">
      <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star_wrapper">
            <star :size="48" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠信息</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="item in seller.supports" :key="item.index">
              <span class="icon" :class="classList[item.type]"></span>
              <span class="text">{{item.description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="closeDetail">
        <i class="icon-close"></i>
      </div>
    </div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import star from '../star/star';
  export default {
    name: 'v-header',
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        detailShow: false
      };
    },
    methods: {
      showDetail () {
        this.detailShow = true;
      },
      closeDetail () {
        this.detailShow = false;
      }
    },
    created () {
      this.classList = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
    },
    components: {
      star
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .v-header
    position relative
    color aliceblue
    background rgba(7, 17, 27, 0.5)
    overflow hidden
    .content-wrapper
      position relative
      padding: 24px 12px 18px 24px
      .avatar
        display: inline-block
        vertical-align: top
        img
          border-radius: 2px
      .content
        display: inline-block
        font-size: 14px
        margin-left: 16px
        .title
          margin: 2px 0 8px 0
          .brand
            display inline-block
            vertical-align: top
            height: 18px
            width: 30px
            bg-image('brand')
            background-size: 30px 18px
            background-repert: no-repeat
          .name
            margin-left: 6px
            font-size: 16px
            line-weight: 18px
            font-weight: bold
        .discription
          margin-bottom: 10px
        .support
          .icon
            display: inline-block
            height: 12px
            width: 12px
            margin-right 4px
            background-size 12px 12px
            background-repeat no-repeat
            bg-images("1")
          .text
            display: inline-block
            vertical-align top
            line-height 12px
            font-size 10px
      .support-count
        position absolute
        right 12px
        bottom 18px
        padding 0 8px
        height 24px
        line-height 24px
        border-radius 14px
        background rgba(0, 0, 0, 0.2)
        .count
          font-size 10px
        .icon-keyboard_arrow_right
          display: inline-block
          vertical-align top
          line-height 24px
          margin-left 2px
          font-size 10px
    .bulletin-wrapper
      position relative
      height 28px
      line-height 28px
      padding 0 22px 0 12px
      white-space nowrap
      overflow hidden
      text-overflow ellipsis
      background rgba(7, 17, 27, 0.2)
      .bulletin-title
        display inline-block
        vertical-align top
        margin-top 8px
        width 22px
        height 12px
        bg-image('bulletin')
        background-size 22px 12px
        background-repeat no-repeat
      .bulletin-text
        vertical-align top
        margin 0 4px
        font-size 10px
      .icon-keyboard_arrow_right
        position absolute
        font-size 10px
        right 12px
        top 8px
    .background
      position absolute
      top 0
      left 0
      width 100%
      height 100%
      z-index -1
      filter blur(10px)
    .detail
      position fixed
      top 0
      left 0
      z-index 200
      width 100%
      height 100%
      overflow auto
      background rgba(7, 17, 27, 0.8)
      -webkit-backdrop-filter blur(10px)
      &.fade-enter-active, &.fade-leave-active
          transition all 0.5s ease
      &.fade-enter, &.fade-leave-active
          opacity 0
      .detail-wrapper
        width 100%
        min-height 100%
        .detail-main
          margin-top 64px
          padding-bottom 64px
          .name
            line-height 16px
            text-align center
            font-size 16px
            font-weight 700
            color rgb(255, 255, 255)
          .star_wrapper
            margin-top 18px
            padding 2px 0
            text-align center
          .title
            display flex
            width 80%
            margin 28px auto 24px auto
            .line
               flex 1
               position relative
               top -6px
               border-bottom 1px solid rgba(255, 255, 255, 0.2)
            .text
               padding 0 12px
               font-weight 700
               font-size 14px
          .supports
             width 80%
             margin 0 auto
             .support-item
                padding 0 12px
                margin-bottom 12px
                font-size 0
                &:last-child
                   margin-bottom 0
                .icon
                  display inline-block
                  width 16px
                  height 16px
                  margin-right 6px
                  background-size 16px 16px
                  vertical-align top
                  background-repeat no-repeat
                  bg-images("2")
                .text
                  line-height 12px
                  font-size 12px
                  font-weight 20
          .bulletin
             width 80%
             margin 0 auto
             .content
                padding 0 12px
                line-height 24px
                font-size 12px
      .detail-close
        position: relative
        width 32px
        height 32px
        margin -64px auto 0 auto
        clear both
        font-size 32px
</style>
