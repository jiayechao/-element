<template>
<div class="goods">
	<div class="menu-wrapper" ref="menu-wrapper">
   <ul>
      <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index,$event)">
        <span class="text">
          <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
        </span>
      </li>
    </ul>
  </div>
	<div class="foods-wrapper" ref="foods-wrapper">
   <ul>
      <li v-for="item in goods" class="food-list food-list-hook">
        <h1 class="title">{{item.name}}</h1>
        <ul>
          <li @click="selectFood(food, $event)" v-for="food in item.foods" class="food-item border-1px">
            <div class="icon">
              <img width="57" height="57" :src="food.icon" alt="">
            </div>
            <div class="content">
              <h2 class="name">{{food.name}}</h2>
              <p class="desc">{{food.description}}</p>
              <div class="extra">
                <span>月售{{food.sellCount}}</span>
                <span>好评率{{food.rating}}</span>
              </div>
              <div class="price">
                <span class="now">￥{{food.price}}</span>
                <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
              </div>
              <div class="cartcontrol-wrapper">
                <cartcontrol :food="food" @cartAdd="addFood"></cartcontrol>
              </div>
            </div>
          </li>
        </ul>
      </li>
    </ul>
  </div>
  <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice" :select-foods="selectFoods"  ref="shopcart"></shopcart>
  <food :food="selectedFood" ref="food" @cartAdd="addFood"></food>
</div>

</template>

<script>
import axios from 'axios';
import BScroll from 'better-scroll';
import shopcart from '../shopcart/shopcart.vue';
import cartcontrol from '../cartcontrol/cartcontrol.vue';
import food from '../food/food.vue';
const ERR_OK = 0;
export default {
  props: {
    seller: {
      type: Object
    }
  },
  components: {
    shopcart: shopcart,
    cartcontrol: cartcontrol,
    food: food
  },
  data() {
    return {
      goods: [],
      listHeight: [],
      scrollY: '',
      selectedFood: {}
    };
  },
  computed: {
    // 左右联动的计算属性
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        };
      }
      return 0;
    },
    selectFoods() {
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
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    var _this = this;
    axios.get('/api/goods')
    .then(function (response) {
      response = response.data;
      if (response.errno === ERR_OK) {
        _this.goods = response.data;
        _this.$nextTick(function () {
          this._initScroll();
          this.calculateHeight();
        });
      }
    })
    .catch(function (error) {
      console.log(error);
    });
  },
  methods: {
    _initScroll() {
      this.menuScroll = new BScroll(this.$refs['menu-wrapper'], {
        // 默认会阻止点击事件
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs['foods-wrapper'], {
        click: true,
        probeType: 3
      });
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.round(Math.abs(pos.y));
      });
    },
    calculateHeight() {
      let foodList = this.$refs['foods-wrapper'].getElementsByClassName('food-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      };
      console.log(this.listHeight);
    },
    selectMenu(index, event) {
      if (!event._constructed) {
        return;
      };
      let foodList = this.$refs['foods-wrapper'].getElementsByClassName('food-list-hook');
      let el = foodList[index];
      this.foodsScroll.scrollToElement(el, 300);
    },
    addFood(target) {
      this._drop(target);
    },
    _drop(target) {
      // 体验优化，动画异步执行
      this.$nextTick(() => {
        this.$refs.shopcart.drop(target);
      });
    },
    selectFood(food, event) {
      if (!event._constructed) {
        return;
      };
      this.$refs.food.show();
      this.selectedFood = food;
    }
  }
};
</script>
<style scoped lang="scss">
@import '../../common/sass/mixin.scss';
@import '../../common/sass/base.scss';
.goods{
	position: absolute;
	top: 174px;
	width: 100%;
	bottom: 46px;
	display: flex;
	overflow: hidden;
	.menu-wrapper{
		flex: 0 0 80px;
		width: 80px;
		background: #f3f5f7;
    .menu-item{
      display:table;
      height: 54px;
      width: 56px;
      line-height: 14px;
      font-size: 14px;
      padding: 0 12px;
      &.current{
        position: relative;
        z-index: 10;
        margin-top: -1px;
        background: #fff;
        font-weight: 700;
        .text{
          @include border-none;
        }
      }
      .icon{
        display: inline-block;
        vertical-align: top;
        width: 12px;
        height: 12px;
        margin-right: 2px;
        background-size: 12px 12px;
        background-repeat: no-repeat;
        &.decrease{
          @include bg-image('decrease_3');
        };
        &.discount{
          @include bg-image('discount_3');
        };
        &.guarantee{
          @include bg-image('guarantee_3');
        };
        &.invoice{
          @include bg-image('invoice_3');
        };
        &.special{
          @include bg-image('special_3');
        };
      }
      .text{
        display: table-cell;
        width: 56px;
        vertical-align: middle;
        font-size: 12px;
        border-bottom: 1px solid #ccc
      }
    }
	}
	.foods-wrapper{
		flex: 1;
    .title{
      padding-left: 14px;
      height: 26px;
      line-height: 26px;
      border-left: 2px solid #d9dde1;
      font-size: 12px;
      background: #f3f5f7;
      color: rgb(147,153,159);
    }
    .food-item{
      display: flex;
      margin: 18px;
      padding-bottom: 18px;
      @include border-1px(#d9dde1);
      &:last-child{
        @include border-none;
        margin-bottom: 0;
      };
      .icon{
        flex: 0 0 57px;
        margin-right: 10px;
      }
      .content{
        flex: 1;
        .name{
          margin: 2px 0 8px 0;
          height: 14px;
          line-height: 14px;
          font-size: 14px;
          color: rgb(7,17,27);
        }
        .desc,.extra{
          line-height: 10px;
          font-size: 10px;
          color: rgb(147,153,159);
        }
        .desc{
          margin-bottom: 8px;
        }
        .extra{
          &.count{
            margin-right: 12px;
          }
        }
        .price{
          font-weight: 700;
          line-height: 24px;
          .now{
            margin-right: 8px;
            font-size: 14px;
            color: rgb(240,20,20)
          }
          .old{
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147,153,159);
          }
        }
        .cartcontrol-wrapper{
          position: absolute;
          right: 0;
          bottom: 12px;
        }
      }
    }
	}
}
</style>
