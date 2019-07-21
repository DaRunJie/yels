<template>
  <div class="ratings-select">
    <div class="ratings-type border-1px">
      <span @click="select(2, $event)" class="block ratings-positive" :class="{'active': currentType === 2}">
        {{desc.all}}<span class="count">{{ratings.length}}</span></span>
      <span @click="select(0, $event)" class="block ratings-positive" :class="{'active': currentType === 0}">
        {{desc.positive}}<span class="count">{{count(0)}}</span></span>
      <span @click="select(1, $event)" class="block ratings-negative" :class="{'active': currentType === 1}">
        {{desc.negative}}<span class="count">{{count(1)}}</span></span>
    </div>
    <div @click="toggleContent" class="switch">
      <i class="icon-check_circle" :class="{'on': currentContent === true}"></i>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;
  export default {
    props: {
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    data() {
      return {
        currentType: this.selectType,
        currentContent: this.onlyContent
      };
    },
    methods: {
      count(type) {
        let count = 0;
        this.ratings.forEach((rating) => {
          if (rating.rateType === type) {
            count++;
          }
        });
        return count;
      },
      select(type, event) {
        if (!event._constructed) {
          return;
        }
        this.currentType = type;
        this.$emit('rating-type', type);
      },
      toggleContent(event) {
        if (!event._constructed) {
          return;
        }
        this.currentContent = !this.currentContent;
        this.$emit('content-type', this.currentContent);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .ratings-select
    .ratings-type
      padding 18px 0
      margin 0 18px
      border-1px(rgba(7, 17, 27, 0.1))
      .block
        display inline-block
        padding 8px 12px
        font-size 12px
        line-height 16px
        margin-right 8px
        border-radius 1px
        color rgb(77, 85, 93)
        &.active
          color rgb(255, 255, 255)
        .count
          font-size 8px
          margin-left 2px
        &.ratings-positive
          background rgba(0, 160, 220, 0.2)
          &.active
            background rgb(0, 160, 220)
        &.ratings-negative
          background rgba(77, 85, 93, 0.2)
          &.active
            background rgb(77, 85, 93)
    .switch
      padding 12px 18px
      line-height 24px
      border-bottom 1px solid rgba(7, 17, 27, 0.1)
      color rgb(147, 153, 159)
      font-size 0
      .icon-check_circle
        display inline-block
        vertical-align middle
        font-size 24px
        &.on
          color rgb(0, 200, 80)
      .text
        display inline-block
        vertical-align middle
        font-size 12px
        margin-left 4px
</style>
