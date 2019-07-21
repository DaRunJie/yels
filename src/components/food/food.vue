<template>
  <transition name="move">
    <div v-show="foodShow" class="food" ref="foodWrapper">
      <div class="food-content">
        <div class="image-header">
          <img class="img-h" v-lazy="food.image"/>
          <div class="icon-back" @click="show">
            <i class="icon-close"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="count">月销{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="currentPrice">¥{{food.price}}</span>
            <span v-show="food.oldPrice" class="oldPrice">¥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :food="food" ref="cartcontrol"></cartcontrol>
          </div>
        </div>
        <div v-show="food.info">
          <split-line></split-line>
          <div class="discription">
            <h1 class="title">商品介绍</h1>
            <p class="info">{{food.info}}</p>
          </div>
        </div>
        <split-line v-show="food.ratings"></split-line>
        <div class="ratings-control">
          <h1 class="title">商品评价</h1>
          <ratingselect :ratings="food.ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc"
                        v-on:rating-type="currentType" v-on:content-type="currentContent"></ratingselect>
        </div>
        <div class="ratings-wrapper">
          <ul v-show="food.ratings && food.ratings.length">
            <li v-show="selectShow(rating.rateType, rating.text)" v-for="rating in food.ratings" :key="rating.index"
                class="rating-item border-1px">
              <div class="time">{{rating.rateTime | formatDate}}</div>
              <p class="text">
                <i class="icon"
                   :class="{'icon-thumb_up': rating.rateType === 0, 'icon-thumb_down': rating.rateType === 1}"></i>{{rating.text}}
              </p>
              <div class="user">
                <span class="username">{{rating.username}}</span>
                <img width="12" height="12" class="avatar" v-lazy="rating.avatar"/>
              </div>
            </li>
          </ul>
          <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from '../../common/js/date';
  import cartcontrol from '../carcontrol/cartcontrol';
  import split from '../split/split';
  import ratingselect from '../ratingselect/ratingselect';

  const ALL = 2;

  export default {
    components: {
      cartcontrol,
      'split-line': split,
      ratingselect
    },
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        foodShow: false,
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      };
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    methods: {
      show() {
        this.foodShow = !this.foodShow;
        this.selectType = ALL;
        this.onlyContent = true;
        this.$nextTick(() => {
          if (!this.foodScroll) {
            this.foodScroll = new BScroll(this.$refs.foodWrapper, {
              click: true
            });
          } else {
            this.foodScroll.refresh();
          }
        });
      },
      selectShow(rateType, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return rateType === this.selectType;
        }
      },
      currentType(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.foodScroll.refresh();
        });
      },
      currentContent(type) {
        this.onlyContent = type;
        this.$nextTick(() => {
          this.foodScroll.refresh();
        });
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .food
    position fixed
    left 0
    top 0
    bottom 48px
    width 100%
    background #fff
    z-index 30
    &.move-enter-active, &.move-leave-active
      transition all 0.2s linear
      transform translate3d(0, 0, 0)
    &.move-enter, &.move-leave-active
      transform translate3d(100%, 0, 0)
    .image-header
      position relative
      height 0
      width 100%
      padding-top 100%
      .img-h
        position absolute
        top 0
        left 0
        height 100%
        width 100%
      .icon-back
        position absolute
        top 10px
        right 10px
        border-radius 50%
        background rgba(0, 0, 0, 0.4)
        .icon-close
          display block
          padding 10px
          font-size 20px
          color #fff
    .content
      padding 18px
      .title
        font-size 14px
        font-weight 700
        color rgb(7, 17, 27)
        line-height 14px
      .detail
        line-height 10px
        font-size 10px
        color rgb(147, 153, 159)
        margin-top 8px
        .count
          margin-right 12px
      .price
        margin-top 18px
        font-size 14px
        font-weight normal / 700
        line-height 24px
        .currentPrice
          margin-right 8px
          font-size 14px
          color rgb(240, 20, 20)
        .oldPrice
          font-size 10px
          text-decoration line-through
          color rgb(147, 153, 159)
      .cartcontrol-wrapper
        position absolute
        right 12px
        margin-top -30px
    .discription
      padding 18px
      .title
        font-size 14px
        color rgb(7, 17, 27)
        line-height 14px
        margin-bottom 6px
      .info
        font-size 12px
        font-weight 200
        color rgb(77, 85, 93)
        line-height 12px
        padding 0 8px
    .ratings-control
      margin-top 18px
      .title
        margin-left 18px
        font-size 14px
        color rgb(7, 17, 27)
        line-height 14px
        margin-bottom 6px
    .ratings-wrapper
      padding 0 18px
      .rating-item
        padding 16px 0
        position relative
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom 0
        .time
          font-size 10px
          color rgb(147, 153, 159)
          line-height 12px
          margin-bottom 6px
        .text
          font-size 12px
          line-height 16px
          color rgb(7, 17, 27)
          .icon
            margin-right 4px
            line-height 24px
            &.icon-thumb_up
              color rgb(0, 160, 220)
            &.icon-thumb_down
              color rgb(147, 153, 159)
        .user
          position absolute
          top 16px
          right 0
          line-height 12px
          font-size 0
          .username
            display inline-block
            vertical-align top
            font-size 10px
            color rgb(147, 153, 159)
            margin-right 6px
          .avatar
            border-radius 50%
      .no-rating
        padding 16px 0
        font-size 12px
        color rgb(147, 153, 159)
</style>
