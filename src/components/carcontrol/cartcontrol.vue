<template>
  <div class="cartControl">
    <transition name="move">
      <div class="cart-decrease" v-show="food.count > 0" @click.stop="decreaseCart">
        <span class="inner icon-remove_circle_outline"></span>
      </div>
    </transition>
    <div class="cart-count" v-show="food.count > 0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click.stop="addCart"></div>
  </div>
</template>

<script type="text/ecmascript-6">
  import Vue from 'vue';

  export default {
    props: {
      food: {
        type: Object
      }
    },
    created() {
    },
    methods: {
      addCart(event) {
        if (!this.food.count) {
          Vue.set(this.food, 'count', 1);
        } else {
          this.food.count++;
        }
        this.$emit('cart-add', event.target); // 提交事件给父组件
      },
      decreaseCart() {
        if (this.food.count) {
          this.food.count--;
        }
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .cartControl
    font-size 0
    margin-bottom -9px
    .cart-decrease
      display inline-block
      padding 6px
      transition: all 0.2s linear
      .inner
        display inline-block
        line-height 24px
        font-size 24px
        color rgb(0, 160, 220)
      &.move-enter-active
        transform translate3d(0, 0, 0)
        .inner
          transition: all 0.2s linear
          transform rotate(0)
      &.move-enter, &.move-leave-active
        transform translate3d(24px, 0, 0)
        .inner
          transition: all 0.2s linear
          transform rotate(180deg)
    .cart-count
      display inline-block
      vertical-align top
      font-size 10px
      width 12px
      height 24px
      line-height 24px
      padding-top 6px
      text-align center
      color rgb(147, 153, 159)
    .cart-add
      display inline-block
      padding 6px
      height 24px
      line-height 24px
      font-size 24px
      color rgb(0, 160, 220)
</style>
