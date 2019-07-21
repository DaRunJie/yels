<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item, index) in goods" :key="index" class="menu-item"
            :class="{current: index === currentIndex}"
            @click="selectMenu(index)">
          <span class="text border-1px">
            <span v-show="item.type > 0" class="icon" :class="classList[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" :key="item.index" class="food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="focusFood(food, $event)" v-for="food in item.foods" :key="food.index"
                class="food-item border-1px">
              <div class="icon">
                <img width="57px" height="57px" v-lazy="food.icon"/>
              </div>
              <div class="content">
                <h2 class="name">{{food.name}}</h2>
                <p class="description">{{food.description}}</p>
                <div class="extra">
                  <span class="count">月销{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                </div>
                <div>
                  <span class="price">¥{{food.price}}</span>
                  <span v-show="food.oldPrice" class="oldPrice">¥{{food.oldPrice}}</span>
                </div>
                <div class="control-wrapper">
                  <cartcontrol :food="food" v-on:cart-add="cartAdd"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart ref="shopcart"
              :select-foods="selectFood" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    <v-food :food="selectedFood" ref="Vfood"></v-food>
  </div>
</template>

<script type="text/ecmascript-6">
  import BScroll from 'better-scroll';
  import shopcart from '../shopcart/shopcart';
  import cartcontrol from '../carcontrol/cartcontrol';
  import food from '../food/food';

  const ERR_OK = 0;
  export default {
    name: 'goods',
    components: {
      cartcontrol,
      shopcart,
      'v-food': food
    },
    props: {
      seller: {
        type: Object
      },
      goods: {
        type: Array
      }
    },
    data() {
      return {
        listHeight: [],
        scrollY: 0,
        selectedFood: {}
      };
    },
    watch: {
      goods: function () {
        this.$nextTick(() => {
          this._initBScroll();
          this._initRightHeight();
          console.log('created:   ' + this.listHeight);
        });
      }
    },
    created() {
      this.classList = ['decrease', 'discount', 'guarantee', 'invoice', 'special'];
    },
    computed: {
      /**
       * 右侧菜单列表当前高度索引
       * @returns {number}
       */
      currentIndex() {
        for (let i = 0; i < this.listHeight.length; i++) {
          let height1 = this.listHeight[i];
          let height2 = this.listHeight[i + 1];
          if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
            return i;
          }
        }
        return 0;
      },
      /**
       * 向购物车中添加item
       * @returns {Array}
       */
      selectFood() {
        let foods = [];
        this.goods.forEach((good) => {
          good.foods.forEach((food) => {
            if (food.count) {
              foods.push(food);
            }
          });
        });
        return foods;
      }
    },
    methods: {
      /**
       * 滚动到指定元素
       * @param index
       */
      selectMenu(index) {
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        let el = foodList[index];
        this.rightBScroll.scrollToElement(el, 300);
      },
      _initBScroll() {
        this.leftBScrolll = new BScroll(this.$refs.menuWrapper, {
          click: true
        });
        this.rightBScroll = new BScroll(this.$refs.foodsWrapper, {
          click: true,
          probeType: 3
        });
        this.rightBScroll.on('scroll', (pos) => {
          this.scrollY = Math.abs(Math.round(pos.y));
        });
      },
      _initRightHeight() {
        let itemArray = [];
        let top = 0;
        itemArray.push(top);
        let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
        Array.prototype.slice.call(foodList).forEach(li => {
          top += li.clientHeight;
          itemArray.push(top);
        });
        this.listHeight = itemArray;
        console.log('_init:   ' + this.listHeight);
      },
      focusFood(food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectedFood = food;
        this.$refs.Vfood.show();
      },
      // 将target对象传给子组件,异步执行下落动画
      cartAdd(target) {
        this.$nextTick(() => {
          this.$refs.shopcart.drop(target);
        });
      }
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl";

  .goods
    display flex
    position absolute
    top 174px
    width 100%
    bottom 46px
    overflow hidden
    .menu-wrapper
      flex 0 0 80px
      width 80px
      background #f3f5f7
      .menu-item
        display table
        height 54px
        width 56px
        line-height 14px
        padding 0 12px
        .icon
          display: inline-block
          height: 12px
          width: 12px
          margin-right 2px
          background-size 12px 12px
          background-repeat no-repeat
          bg-images("3")
        .text
          display table-cell
          width 56px
          vertical-align middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size 12px
      .current
        position relative
        margin-top -1px
        background #fff
        font-weight 700
        .text
          border-none()
    .foods-wrapper
      flex 1
      .title
        padding-left: 14px
        height 26px
        line-height 26px
        border-left 2px solid #d9dde1
        font-size 12px
        color rgb(147, 153, 159)
        background #f3f5f7
      .food-item
        display flex
        margin 18px
        padding 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom 0
        .icon
          flex 0 0 57px
          margin-right 10px
        .content
          flex 1
          .name
            font-size 14px
            color rgb(7, 17, 27)
            height 10px
            line-height 14px
            margin 2px 0 8px 0
          .description, .extra
            font-size 10px
            color rgb(147, 153, 159)
          .description
            line-height 12px
            margin-bottom 8px
          .extra
            line-height 10px
            .count
              margin-right 12px
          .price, .oldPrice
            font-weight normal / 700
            line-height 24px
          .price
            margin-right 8px
            font-size 14px
            color rgb(240, 20, 20)
          .oldPrice
            font-size 10px
            text-decoration line-through
            color rgb(147, 153, 159)
          .control-wrapper
            position absolute
            right 0
            bottom 12px

</style>
