<template>
  <div class="seller" ref="sellerWrapper">
    <div>
      <div class="seller-content">
        <h1 class="name">{{seller.name}}</h1>
        <div class="score border-1px">
          <star class="star" :size="36" :score="seller.rankRate / 20"></star>
          <span class="ratingCount">({{seller.ratingCount}})</span>
          <span class="sellCount">月销{{seller.sellCount}}单</span>
        </div>
        <ul class="seller-delivery">
          <li class="block">
            <h2 class="title">起送价</h2>
            <div class="content">
              <span class="num">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">商家配送</h2>
            <div class="content">
              <span class="num">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2 class="title">平均配送时间</h2>
            <div class="content">
              <span class="num">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="notice">
        <h1 class="title">公告与活动</h1>
        <p class="text">{{seller.bulletin}}</p>
        <ul class="supports">
          <li class="supports-item" v-for="support in seller.supports" :key="support.index">
            <span class="icon" :class="classList[support.type]"></span>
            <span class="supports-text">{{support.description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="seller-pics">
        <h1 class="title">商家实景</h1>
        <div ref="picsWrapper">
          <ul class="seller-pics" ref="picsList">
            <li class="pics" v-for="pic in seller.pics" :key="pic.index">
              <img height="90" width="120" v-lazy="pic"/>
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="infos">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info border-1px" v-for="info in seller.infos" :key="info.index">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import star from '../star/star';
  import split from '../split/split';

  export default {
    name: 'v-seller',
    components: {
      star,
      split
    },
    props: {
      seller: {
        type: Object
      }
    },
    created() {
      this.classList = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
    },
    mounted() {
      this.$nextTick(() => {
        this._initSeller();
        this._initPics();
      });
    },
    methods: {
      _initSeller() {
        this.$nextTick(() => {
          if (!this.sellerScroll) {
            this.sellerScroll = new BScroll(this.$refs.sellerWrapper, {});
          } else {
            this.sellerScroll.refresh();
          }
        });
      },
      _initPics() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          this.$refs.picsList.style.width = width + 'px';
          this.$nextTick(() => {
            if (!this.picScroll) {
              this.picScroll = new BScroll(this.$refs.picsWrapper, {
                scrollX: true,
                eventPassthrough: 'vertical'
              });
            } else {
              this.picScroll.refresh();
            }
          });
        }
      }
    }
  }
  ;
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .seller
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .seller-content
      padding-top 18px
      .name
        font-size 14px
        line-height 14px
        margin-bottom 8px
        padding 0 18px
        color rgb(7, 17, 27)
      .score
        margin 0 18px
        line-height 18px
        padding-bottom 18px
        border-1px(rgba(7, 17, 27, 0.1))
        .star
          display inline-block
          vertical-align top
          margin-right 8px
        .ratingCount, .sellCount
          display inline-block
          vertical-align top
          font-size 10px
          color rgb(77, 85, 93)
        .ratingCount
          margin-right 12px
      .seller-delivery
        display flex
        text-align center
        .block
          flex 1
          display inline-block
          margin 18px 0
          border-right 1px solid rgba(7, 17, 27, 0.1)
          &:last-child
            border-right none
          .title
            font-size 10px
            line-height 10px
            color rgb(147, 153, 159)
            margin-bottom 4px
          .content
            font-size 10px
            font-weight 200
            line-height 24px
            color rgb(7, 17, 27)
            .num
              font-size 24px
    .notice
      padding 18px
      .title
        font-size 14px
        line-height 14px
        margin-bottom 8px
        color rgb(7, 17, 27)
      .text
        font-size 12px
        font-weight 200
        line-height 24px
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        margin 0 12px
        padding-bottom 16px
        color rgb(240, 20, 20)
      .supports
        .supports-item
          padding 16px 12px
          border-bottom 1px solid rgba(7, 17, 27, 0.1)
          .icon
            display inline-block
            vertical-align top
            height 16px
            width 16px
            background-size 16px 16px
            background-repeat no-repeat
            margin-right 6px
            bg-images("4")
          .supports-text
            display inline-block
            vertical-align top
            font-size 12px
            font-weight 200
            line-height 16px
            color rgb(7, 17, 27)
    .seller-pics
      padding 18px
      .title
        font-size 14px
        line-height 14px
        margin-bottom 12px
        color rgb(7, 17, 27)
      .seller-pics
        .pics
          display inline
          overflow hidden
          white-space nowrap
          font-size 0
          margin-right 6px
          &:last-child
            margin 0
    .infos
      padding 18px
      .title
        font-size 14px
        line-height 14px
        padding-bottom 12px
        color rgb(7, 17, 27)
        border-1px(rgba(7, 17, 27, 0.1))
      .info
        font-size 12px
        font-weight 200
        line-height 16px
        padding 16px 12px
        color rgb(7, 17, 27)
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
           border-none()
</style>
