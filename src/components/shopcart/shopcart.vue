<template>
  <div class="shopcart">
    <div class="content" @click="toggleList">
      <div class="content-left">
        <div class="logo-wrapper">
          <div class="logo" :class="{'highlight': totalCount > 0}">
            <i class="icon-shopping_cart" :class="{'highlight': totalCount > 0}"></i>
          </div>
          <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
        </div>
        <div class="price" :class="{'highlight': totalPrice > 0}">¥{{totalPrice}}</div>
        <div class="description">另需配送费￥{{deliveryPrice}}</div>
      </div>
      <div class="content-right" :class="{'enough': totalPrice >= minPrice, 'not-enough': totalPrice < minPrice}"
           @click.stop="pay">
        <span class="pay">
          {{payDesc}}</span>
      </div>
    </div>
    <div class="ball-container">
      <transition name="drop" v-on:before-enter="beforeEnter" v-on:enter="enter" v-on:after-enter="afterEnter"
                  v-for="(litterBall,indexBall) in balls" :key="indexBall">
        <div v-show="litterBall.show" class="ball">
          <div class="inner inner-hook" :class="indexBall"></div>
        </div>
      </transition>
    </div>
    <transition name="fold">
      <div class="shopcart-list" v-show="listShow">
        <div class="list-header">
          <h1 class="title">购物车</h1>
          <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content" ref="listContent">
          <ul>
            <li class="food border-1px food-list-hook" v-for="food in selectFoods" :key="food.index">
              <span class="name">{{food.name}}</span>
              <div class="price">
                <span>¥{{food.price * food.count}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food"></cartcontrol>
              </div>
            </li>
          </ul>
        </div>
      </div>
    </transition>
    <transition name="fade">
      <div class="list-mask" v-show="listShow" @click="toggleList"></div>
    </transition>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import cartcontrol from '../carcontrol/cartcontrol';

  export default {
    components: {
      cartcontrol
    },
    props: {
      'select-foods': {
        type: Array,
        default() {
          return [];
        }
      },
      'delivery-price': {
        type: Number,
        default: 0
      },
      'min-price': {
        type: Number,
        default: 0
      }
    },
    data() {
      return {
        balls: [
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          },
          {
            show: false
          }
        ],
        dropBalls: [],
        fold: true
      };
    },
    computed: {
      totalPrice() {
        let total = 0;
        this.selectFoods.forEach((food) => {
          total += food.price * food.count;
        });
        return total;
      },
      totalCount() {
        let count = 0;
        this.selectFoods.forEach((food) => {
          count += food.count;
        });
        return count;
      },
      payDesc() {
        if (this.totalPrice === 0) {
          return `￥${this.minPrice}元起送`;
        } else if (this.minPrice > this.totalPrice) {
          return `还差￥${this.minPrice - this.totalPrice}起送`;
        } else {
          return '去结算';
        }
      },
      listShow() {
        if (!this.totalCount) {
          return false;
        }
        let show = !this.fold;
        return show;
      }
    },
    watch: {
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
        }
        if (this.listShow) {
          this.$nextTick(() => {
            if (!this.cartScroll) {
              this.cartScroll = new BScroll(this.$refs.listContent, {
                click: true
              });
            } else {
              this.cartScroll.refresh();
            }
          });
        }
      }
    },
    /**
     * param el catAdd的DOM对象
     */
    methods: {
      drop(el) {
        for (let i = 0; i < this.balls.length; i++) {
          let ball = this.balls[i];
          if (!ball.show) {
            ball.show = true;
            ball.el = el;
            this.dropBalls.push(ball);
            return;
          }
        }
      },
      beforeEnter(el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect(); // 获取添加按钮位置
            let x = rect.left - 32; // 获取x轴偏移
            let y = -(window.innerHeight - rect.top - 22); // 获取y轴偏移
            // el.style.display = '';
            el.style.opacity = 1;
            el.style.webktiTransform = `translate3d(0, ${y}px, 0)`;
            el.style.transform = `translate3d(0, ${y}px,0)`;

            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px,0, 0)`;
            inner.style.transform = `translate3d(${x}px, 0, 0)`;
          }
        }
      },
      enter(el) {
        let rf = el.offsetHeight; // 触发浏览器重绘
        this.$nextTick(() => {
          el.style.webktiTransform = 'translate3d(0, 0, 0)';
          el.style.transform = 'translate3d(0, 0,0)';

          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = 'translate3d(0,0, 0)';
          inner.style.transform = 'translate3d(0, 0, 0)';
        });
      },
      afterEnter(el) {
        let ball = this.dropBalls.shift();
        if (ball) {
          ball.show = false;
          el.style.display = 'none';
        }
      },
      toggleList() {
        if (!this.totalCount) {
          return;
        }
        this.fold = !this.fold;
      },
      empty() {
        this.selectFoods.forEach((food) => {
          food.count = 0;
        });
      },
      pay () {
        if (this.totalPrice < this.minPrice) {
          return;
        }
        window.alert(`支付 ￥${this.totalPrice}元`);
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .shopcart
    position fixed
    left 0
    bottom 0
    width 100%
    height 48px
    z-index 100
    .content
      display flex
      background #141d27
      font-size 0
      color rgba(255, 255, 255, 0.4)
      .content-left
        flex 1
        .logo-wrapper
          display inline-block
          position relative
          top -10px
          margin 0 18px
          padding 6px
          width 56px
          height 56px
          box-sizing border-box
          vertical-align top
          border-radius 50%
          background #141d27
          .logo
            width 100%
            height 100%
            border-radius 50%
            background #2b343c
            text-align center
            &.highlight
              background rgb(0, 160, 220)
            .icon-shopping_cart
              line-height 44px
              font-size 24px
              &.highlight
                color #fff
          .num
            position absolute
            top 0
            right 0
            width 24px
            height 16px
            line-height 16px
            text-align center
            border-radius 16px
            font-size 9px
            font-weight 700
            color #fff
            background rgb(240, 20, 20)
            box-shadow 0 4px 8px 0 rgba(0, 0, 0, 0.4)
        .price
          display inline-block
          vertical-align top
          margin-top 12px
          line-height 24px
          padding-right 12px
          box-sizing border-box
          border-right 1px solid rgba(255, 255, 255, 0.1)
          font-size 16px
          font-weight 700
          &.highlight
            color rgb(255, 255, 255)
        .description
          display inline-block
          vertical-align top
          margin 12px 0 0 12px
          line-height 24px
          font-weight 700
          font-size 10px
      .content-right
        flex 0 0 105px
        width 105px
        background #2b333b
        text-align center
        &.not-enough
          background #2b333b
        &.enough
          background #00b43c
          color #fff
        .pay
          height 48px
          line-height 48px
          font-size 12px
          font-weight 700
    .ball-container
      .ball
        position fixed
        left 32px
        bottom 22px
        z-index: 50
        transform translate3d(0, 0, 0)
        transition all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41) // 贝斯尔曲线
        .inner
          width 16px
          height 16px
          border-radius 50%
          background rgb(0, 160, 220)
          transition all 0.4s linear
          transform translate3d(0, 0, 0)
    .shopcart-list
      position absolute
      top 0
      left 0
      z-index -1
      width 100%
      transform translate3d(0, -100%, 0)
      &.fold-enter-active, &.fold-leave-active
        transition all 0.3s linear
        transform translate3d(0, -100%, 0)
      &.fold-enter, &.fold-leave-active
        transform translate3d(0, 0, 0)
      .list-header
        height 40px
        line-height 40px
        padding 0 18px
        background #f3f5f7
        border-bottom 1px solid rgba(7, 17, 27, 0.1)
        .title
          float left
          font-size 14px
          color rgb(7, 17, 27)
        .empty
          float right
          font-size 12px
          color rgb(0, 160, 220)
      .list-content
        padding 0 18px
        max-height 217px
        overflow hidden
        background #fff
        .food
          position relative
          padding 12px 0
          box-sizing border-box
          border-1px(rgba(7, 17, 27, 0.1))
          .name
            line-height 24px
            font-size 14px
            color rgb(7, 17, 27)
          .price
            position absolute
            right 90px
            bottom 12px
            line-height 24px
            font-size 14px
            font-weight 700
            color rgb(240, 20, 20)
          .cartcontrol-wrapper
            position absolute
            right 0
            bottom 16px
    .list-mask
      position fixed
      left 0
      top 0
      height 100%
      width 100%
      z-index -2
      background rgba(7, 17, 27, 0.6)
      -webkit-backdrop-filter blur(10px)
      &.fade-enter-active, &.fade-leave-active
        transition all 0.5s linear
      &.fade-enter, &.fade-leave-active
        opacity 0
</style>
