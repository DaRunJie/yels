<template>
  <div class="star" :class="starType">
    <span v-for="itemClass in itemClasses" :key="itemClass.index" :class="itemClass" class="star-item"></span>
  </div>
</template>

<script type="text/ecmascript-6">
  const LENGTH = 5;
  const GLS_ON = 'on';
  const GLS_HALF = 'half';
  const GLS_OFF = 'off';
    export default {
      props: {
        size: {
          type: Number
        },
        score: {
          type: Number
        }
      },
      computed: {
        starType () {
          return 'star-' + this.size;
        },
        itemClasses () {
          let result = [];
          let score = Math.floor(this.score * 2) / 2;
          let hasDecimal = score % 1 !== 0;
          let integer = Math.floor(score);
          for (let i = 0; i < integer; i++) {
            result.push(GLS_ON);
          }
          if (hasDecimal) {
            result.push(GLS_HALF);
          }
          while (result.length < LENGTH) {
            result.push(GLS_OFF);
          }
          return result;
        }
      }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"
  .star
    font-size 0
    .star-item
       display inline-block
       background-repeat no-repeat
    &.star-36
       .star-item
         width 15px
         height 15px
         margin-right 6px
         background-size 15px 15px
         &:last-child
           margin-right 0
         &.on
           bg-image('star36_on')
         &.half
           bg-image('star36_half')
         &.off
           bg-image('star36_off')
    &.star-24
       .star-item
         width 10px
         height 10px
         margin-right 3px
         background-size 10px 10px
         &:last-child
           margin-right 0
         &.on
           bg-image('star24_on')
         &.half
           bg-image('star24_half')
         &.off
           bg-image('star24_off')
    &.star-48
       .star-item
         width 20px
         height 20px
         margin-right 22px
         background-size 20px 20px
         &:last-child
           margin-right 0
         &.on
           bg-image('star48_on')
         &.half
           bg-image('star48_half')
         &.off
           bg-image('star48_off')
</style>
