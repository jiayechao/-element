<template>
  <div class="footer" @click="toggleList">
    <div class="shopcart">
      <div class="content">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{highlight: totalCount > 0}">
              <span class="icon-shopping_cart"></span>
            </div>
            <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
          </div>
          <div class="price" :class="{highlight: totalCount > 1}">￥{{totalPrice}}</div>
          <div class="desc">另需配送费￥{{deliveryPrice}}</div>
        </div>
        <div class="content-right">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
        <div class="ball-container">
          <div v-for="ball in balls">
            <transition name="drop" @before-enter="beforeDroping" @enter="dropping" @after-enter="afterDrop">
              <div class="ball" v-show="ball.show">
                <div class="inner inner-hook"></div>
              </div>
            </transition>
          </div>
        </div>
        <transition name="fade">
          <div class="shopcart-list" v-show="listShow">
            <div class="list-header">
              <h1 class="title">购物车</h1>
              <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list-content" ref="list-content">
              <ul>
                <li class="food" v-for="food in selectFoods">
                  <span class="name">{{food.name}}</span>
                  <div class="price">
                    ￥<span>{{food.price * food.count}}</span>
                  </div>
                  <div class="cartcontrol-wrapper">
                    <cartcontrol :food="food" @cartAdd="addFood"></cartcontrol>
                  </div>
                </li>
              </ul>
            </div>
          </div>
        </transition>
      </div>
    </div>
    <transition name="op">
      <div class="list-mask" v-show="listShow"></div>
    </transition>
  </div>
</template>

<script>
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  export default {
    props: {
      deliveryPrice: {
        type: Number,
        default: 0
      },
      minPrice: {
        type: Number,
        default: 0
      },
      selectFoods: {
        type: Array,
        default() {
          return [];
        }
      }
    },
    components: {
      cartcontrol
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
          }],
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
        } else if (this.totalPrice < this.minPrice) {
          let diff = this.minPrice - this.totalPrice;
          return `还需￥${diff}起送`;
        } else {
          return `去结算`;
        }
      },
      payClass() {
        if (this.totalPrice < this.minPrice) {
          return 'not-enough';
        } else {
          return 'enough';
        }
      },
      listShow() {
        if (!this.totalCount) {
          this.fold = true;
          return false;
        }
        let show = !this.fold;
        if (show) {
          this.$nextTick(() => {
            if (!this.scroll) {
              this.scroll = new BScroll(this.$refs['list-content'], {
                click: true
              });
            } else {
              this.scroll.refresh();
            }
          });
        }
        return show;
      }
    },
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
      beforeDroping(el) {
        let count = this.balls.length;
        while (count--) {
          let ball = this.balls[count];
          if (ball.show) {
            let rect = ball.el.getBoundingClientRect();
            let x = rect.left - 32;
            let y = -(window.innerHeight - rect.top - 22);
            el.style.display = '';
            el.style.webkitTransform = `translate3d(0,${y}px,0)`;
            el.style.transform = `translate3d(0,${y},0)`;
            let inner = el.getElementsByClassName('inner-hook')[0];
            inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
            inner.style.transform = `translate3d(${x}px,0,0)`;
          }
        }
      },
      dropping(el, done) {
        /* eslint-disable no-unused-vars */
        // 启动重绘
        let rf = el.offsetHeight;
        this.$nextTick(() => {
          el.style.webkitTransform = `translate3d(0,0,0)`;
          el.style.transform = `translate3d(0,0,0)`;
          let inner = el.getElementsByClassName('inner-hook')[0];
          inner.style.webkitTransform = `translate3d(0,0,0)`;
          inner.style.transform = `translate3d(0,0,0)`;
        });
        el.addEventListener('transitionend', done);
      },
      afterDrop(el) {
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
      addFood(target) {
        this._drop(target);
      },
      _drop(target) {
        // 体验优化，动画异步执行
        this.$nextTick(() => {
          this.drop(target);
        });
      }
    }
  };
</script>

<style lang="scss" >
  @import '../../common/sass/mixin.scss';
  .shopcart {
    position: fixed;
    bottom: 0;
    left: 0;
    z-index: 50;
    width: 100%;
    height: 48px;
    .content {
      display: flex;
      background: #141d27;
      font-size: 0;
      .content-left {
        flex: 1;
        .logo-wrapper {
          display: inline-block;
          position: relative;
          top: -10px;
          margin: 0 12px;
          padding: 6px;
          width: 56px;
          height: 56px;
          box-sizing: border-box;
          vertical-align: top;
          border-radius: 50%;
          background: #141d27;
          .logo {
            width: 100%;
            height: 100%;
            border-radius: 50%;
            background: #2b343c;
            text-align: center;
            .icon-shopping_cart {
              line-height: 44px;
              font-size: 24px;
              color: #80858a;
            }
            &.highlight {
              background: rgb(0, 160, 220);
              .icon-shopping_cart {
                color: #fff;
              }
            }
          }
        }
        .num {
          position: absolute;
          top: 0;
          right: 0;
          width: 24px;
          height: 16px;
          line-height: 16px;
          text-align: center;
          border-radius: 16px;
          font-size: 9px;
          font-weight: 700;
          color: #fff;
          background: rgb(240, 20, 20);
          box-shadow: 0 4px 8px 0 rgba(240, 20, 20, 0.4)
        }
        .price {
          display: inline-block;
          line-height: 24px;
          margin-top: 12px;
          box-sizing: border-box;
          padding-right: 12px;
          border-right: 1px solid rgba(255, 255, 255, 0.1);
          font-size: 16px;
          font-weight: 700;
          &.highlight {
            color: #fff;
          }
        }
        .desc {
          display: inline-block;
          vertical-align: top;
          margin: 12px 0 0 12px;
          line-height: 24px;
          color: rgba(255, 255, 255, 0.4);
          font-size: 10px;
        }
      }
      .content-right {
        flex: 0 0 105px;
        width: 105px;
        .pay {
          line-height: 48px;
          height: 48px;
          text-align: center;
          font-size: 12px;
          color: rgba(255, 255, 255, 0.4);
          font-weight: 700;
          &.not-enough {
            background: #2b333b;
          }
          &.enough {
            background: #00b43c;
            color: #fff;
          }
        }
      }

    }
    .ball-container {
      .ball {
        position: fixed;
        left: 32px;
        bottom: 22px;
        z-index: 200;
        transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41);
        .inner {
          width: 16px;
          height: 16px;
          border-radius: 50%;
          background: rgb(0, 160, 220);
          transition: all 0.4s linear;
        }
      }
    }
    .shopcart-list{
      position: absolute;
      left: 0;
      top: 0;
      z-index: -1;
      width: 100%;
      transform: translate3d(0, -100%, 0);
      &.fade-enter-active,&.fade-leave-active{
        transition: all 0.5s;
      }
      &.fade-enter,&.fade-leave-to{
        transform: translate3d(0,0,0);
      }
      .list-header{
        height: 40px;
        line-height: 40px;
        padding:  0 18px;
        background: #f3f5f7;
        border-bottom: 1px solid rgba(7, 17, 27, 0.1);
        .title{
          float: left;
          font-size: 14px;
          color: rgb(7,17,27);
        }
        .empty{
          font-size: 12px;
          float: right;
          color: rgb(0,100,200);
        }
      }
      .list-content{
        padding: 0 18px;
        max-height: 217px;
        background: #fff;
        overflow: hidden;
        .food{
          position: relative;
          padding: 12px 0;
          box-sizing: border-box;
          @include border-1px(rgba(7,17,27,0.1));
          .name{
            line-height: 24px;
            font-size: 14px;
            color: rgb(7,17,27);
          }
          .price{
            position: absolute;
            right: 90px;
            bottom: 12px;
            line-height: 24px;
            color: rgb(240,20,20);
            font-weight: 700;
            font-size: 14px;
          }
          .cartcontrol-wrapper{
            position: absolute;
            right: 0;
            bottom: 6px;
          }
        }
      }
    }
  }
  .list-mask{
    position: fixed;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: 40px;
    -webkit-backdrop-filter: blur(10px);
    background: rgba(7,17,27,0.6);
    opacity: 1;
    &.op-enter-active,&.op-leave-active{
      transition: all 0.5s;
    }
    &.op-enter,&.op-leave-to{
      opacity: 0;
      background: rgba(7,17,27,0);
    }
  }
</style>
