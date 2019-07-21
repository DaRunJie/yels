<template>
  <div class="ratings" ref="ratings">
    <div>
      <div class="scoreWrapper">
        <div class="left-score">
          <h1 class="score">{{seller.score}}</h1>
          <div class="desc">综合评分</div>
          <div class="rank">高于周边商家{{seller.rankRate}}%</div>
        </div>
        <div class="right-score">
          <div class="score-star">
            <span class="text">服务态度</span>
            <star class="star" :size="36" :score="seller.serviceScore"></star>
            <span class="score">{{seller.serviceScore}}</span>
          </div>
          <div class="score-star">
            <span class="text">美食评分</span>
            <star class="star" :size="36" :score="seller.foodScore"></star>
            <span class="score">{{seller.foodScore}}</span>
          </div>
          <div class="deliveryTime">
            <span class="title">送达时间</span>
            <span class="time">{{seller.deliveryTime}}分钟</span>
          </div>
        </div>
      </div>
      <split></split>
      <ratingselect :ratings="ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc"
                    v-on:rating-type="currentType" v-on:content-type="currentContent"></ratingselect>
      <div class="ratingsWrapper">
        <ul>
          <li v-show="selectShow(rating.rateType, rating.text)" v-for="rating in ratings" :key="rating.index"
              class="rating border-1px">
            <img height="28" width="28" class="avatar" v-lazy="rating.avatar"/>
            <div class="content">
              <div class="username">{{rating.username}}</div>
              <div class="star-wrapper">
                <star class="star" :size="24" :score="rating.score"></star>
                <span class="deliveryTime">{{rating.deliveryTime}}分钟送达</span>
              </div>
              <p class="text">{{rating.text}}</p>
              <div class="recommend">
                <i class="icon"
                   :class="{'icon-thumb_up': rating.rateType === 0, 'icon-thumb_down': rating.rateType === 1}"></i>
                <span class="item" v-for="item in rating.recommend" :key="item.index">{{item}}</span>
              </div>
            </div>
            <div class="rateTime">{{rating.rateTime | formatDate}}</div>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import {formatDate} from '../../common/js/date';
  import star from '../star/star';
  import split from '../split/split';
  import ratingselect from '../ratingselect/ratingselect';

  const ERR_OK = 0;
  const ALL = 2;
  export default {
    name: 'v-ratings',
    components: {
      ratingselect,
      star,
      split
    },
    props: {
      seller: {
        type: Object
      },
      ratings: {
        type: Array
      }
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    },
    data() {
      return {
        selectType: ALL,
        onlyContent: true,
        desc: {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        }
      };
    },
    created() {
      this.$nextTick(() => {
        if (!this.ratingsScroll) {
          this.ratingsScroll = new BScroll(this.$refs.ratings, {
            click: true
          });
        } else {
          this.ratingsScroll.refresh();
        }
      });
    },
    methods: {
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
          this.ratingsScroll.refresh();
        });
      },
      currentContent(type) {
        this.onlyContent = type;
        this.$nextTick(() => {
          this.ratingsScroll.refresh();
        });
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings
    position absolute
    top 174px
    bottom 0
    left 0
    width 100%
    overflow hidden
    .scoreWrapper
      display flex
      padding 18px 0
      .left-score
        flex 0 0 137px
        width 137px
        border-right 1px solid rgba(7, 17, 27, 0.2)
        text-align center
        padding 6px 0
        @media only screen and (max-width: 320px)
          flex 0 0 120px
          width 12 opx
        .score
          font-size 24px
          line-height 28px
          color rgb(255, 153, 0)
          margin-bottom 6px
        .desc
          font-size 12px
          line-height 12px
          color rgb(7, 17, 27)
          margin-bottom 8px
        .rank
          font-size 10px
          line-height 10px
          color rgb(147, 153, 159)
      .right-score
        flex 1
        padding 6px 24px
        @media only screen and (max-width: 320px)
          padding 6px 3px
        .score-star
          font-size 0
          line-height 18px
          margin-bottom 8px
          .text
            display inline-block
            vertical-align middle
            font-size 12px
            color rgb(7, 17, 27)
          .star
            display inline-block
            vertical-align middle
            margin 0 12px
          .score
            display inline-block
            vertical-align middle
            font-size 12px
            color rgb(255, 153, 0)
        .deliveryTime
          .title
            display inline-block
            vertical-align top
            font-size 12px
            line-height 18px
            color rgb(7, 17, 27)
          .time
            display inline-block
            vertical-align top
            line-height 18px
            font-size 12px
            color rgb(147, 153, 159)
    .ratingsWrapper
      .rating
        display flex
        padding 18px
        position relative
        border-1px(rgba(7, 17, 27, 0.1))
        .avatar
          flex 0 0 28px
          margin-right 12px
          border-radius 50%
        .content
          .username
            font-size 10px
            line-height 12px
            margin-bottom 4px
            color rgb(7, 17, 27)
          .star-wrapper
            margin-bottom 6px
            font-size 0
            .star
              display inline-block
              vertical-align middle
              margin-right 6px
            .deliveryTime
              display inline-block
              vertical-align middle
              font-size 10px
              font-weight 200
              line-height 12px
              color rgb(147, 153, 159)
          .text
            font-size 12px
            line-height 18px
            margin-bottom 8px
            color rgb(7, 17, 27)
          .recommend
            .icon
              display inline-block
              font-size 12px
              line-height 16px
              margin 0 8px 4px 0
              &.icon-thumb_up
                color rgb(0, 160, 220)
              &.icon-thumb_down
                color rgb(183, 187, 191)
            .item
              display inline-block
              font-size 9px
              line-height 16px
              border 1px solid rgba(7, 17, 0, 0.1)
              border-radius 1px
              margin 0 8px 4px 0
              color rgb(147, 153, 159)
        .rateTime
          position absolute
          top 18px
          right 18px
          font-size 10px
          font-weight 200
          line-height 12px
          color rgb(147, 153, 159)
</style>
