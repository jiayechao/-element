<template>
  <transition name="slide">
  <div v-show="showFlag" class="food" ref="food">
    <div>
    <div class="food-content">
      <div class="image-header">
        <img :src="food.image" alt="">
        <div class="back" @click="showFlag = false">
          <i class="icon-arrow_lift"></i>
        </div>
      </div>
      <div class="content">
        <h1 class="title">{{food.name}}</h1>
        <div class="detail">
          <span class="sell-count">月售{{food.sellCount}}</span>
          <span class="rating">好评率{{food.rating}}</span>
        </div>
        <div class="price">
          <span class="now">￥{{food.price}}</span>
          <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        </div>
      </div>
      <div class="cartcontrol-wrapper">
        <cartcontrol :food="food"></cartcontrol>
      </div>
      <div class="buy" v-show="!food.count || food.count === 0" @click="addFirst($event)">加入购物车</div>
    </div>
    <split v-show="food.info"></split>
    <div class="info" v-show="food.info">
      <h1 class="title">商品信息</h1>
      <p class="text">{{food.info}}</p>
    </div>
    <split></split>
    <div class="rating">
      <h1 class="title">商品评价</h1>
      <ratingSelect
        @content="contentToggle"
        @ratiingType="ratiingTypeSelect"
        :desc="desc"
        :select-type="selectType"
        :only-content="onlyContent"
        :ratings="food.ratings"></ratingSelect>
      <div class="rating-wrapper">
        <ul v-show="food.ratings && food.ratings.length">
          <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings" class="rating-item border-1px">
            <div class="user">
              <span class="name">{{rating.username}}</span>
              <img class="avatar" width="12" height="12" :src="rating.avatar" alt="">
            </div>
            <div class="time">{{rating.rateTime | formatDate}}</div>
            <p class="text">
              <span :class="{'icon-thumb_up':rating.rateType === 0,'icon-thumb_down':rating.rateType === 1}"></span>{{rating.text}}
            </p>
          </li>
        </ul>
        <div class="no-rating" v-show="!food.ratings || !food.ratings.length">
          没有评价
        </div>
      </div>
    </div>
    </div>
  </div>
  </transition>
</template>

<script>
//  const POSITIVE = 0;
//  const NEGATIVE = 1;
  const ALL = 2;
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol.vue';
  import split from '../split/split.vue';
  import ratingSelect from '../ratingSelect/ratingSelect.vue';
  import { formatDate } from '../../common/js/date.js';
  export default {
    props: {
      food: {
        type: Object
      }
    },
    data() {
      return {
        showFlag: false,
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
        return formatDate(date, 'yyyy-MM-dd HH:mm');
      }
    },
    components: {
      cartcontrol: cartcontrol,
      split: split,
      ratingSelect: ratingSelect
    },
    methods: {
      show() {
        // start初始化数据,ratingSelect可能被多个组件引用，所以初始化
        this.slectType = ALL;
        this.onlyContent = true;
        // end
        this.showFlag = true;
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.food, {
              click: true
            });
          } else {
            this.scroll.refresh();
          }
        });
      },
      addFirst(event) {
        if (!event._constructed) {
          return;
        };
        this.$emit('cartAdd', event.target);
        this.$set(this.food, 'count', 1);
      },
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      ratiingTypeSelect(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      contentToggle(content) {
        this.onlyContent = content;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      }
    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  @import '../../common/sass/mixin.scss';
  .food{
    position: fixed;
    top: 0;
    left: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background: #fff;
    .food-content{
      position: relative;
    }
    &.slide-enter-active, &.slide-leave-active{
      transition: 0.3s ease;
    }
    &.slide-enter, &.slide-leave-to{
      transform: translate3d(100%,0,0);
    }
    .image-header{
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img{
        position: absolute;
        top:0;
        left:0;
        width:100%;
        height:100%;
      }
      .back{
        position: absolute;
        top:10px;
        left:0;
        .icon-arrow_lift{
          display: block;
          padding: 10px;
          font-size: 20px;
          color: #fff;
        }
      }
    }
    .content{
      padding: 18px;
      .title{
        line-height: 14px;
        margin-bottom: 8px;
        font-size:14px;
        font-weight:700;
        color: rgb(7,17,27);
      }
      .detail{
        margin-bottom: 18px;
        line-height: 10px;
        font-size: 0;
        height: 10px;
        .sell-count,.rating{
          font-size: 10px;
          color: rgb(149,153,159);
        }
        .sell-count{
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
    }
    .cartcontrol-wrapper{
      position: absolute;
      bottom: 12px;
      right: 12px;
    }
    .buy{
      position: absolute;
      right:18px;
      bottom: 18px;
      z-index: 10;
      font-size: 12px;
      height: 24px;
      line-height: 24px;
      padding: 0 24px;
      box-sizing: border-box;
      border-radius: 12px;
      background: rgb(0,160,200);
      color: #fff;
    }
    .info{
      padding: 18px;
      .title{
        line-height: 14px;
        margin-bottom: 6px;
        font-size: 14px;
        color: rgb(7,17,27);
      }
      .text{
        line-height: 14px;
        font-size: 12px;
        padding: 0 8px;
        color: rgb(77,85,93);
      }
    }
    .rating{
      padding-top: 18px;
      .title{
        line-height: 14px;
        margin-left: 18px;
        font-size: 14px;
        color: rgb(7,17,27);
      }
      .rating-wrapper{
        padding: 0 18px;
        .rating-item{
          position: relative;
          padding: 16px 0;
          @include border-1px('rgba(7,17,27,0,1)');
          .user{
            position: absolute;
            right: 0;
            top: 16px;
            line-height: 12px;
            font-size: 0;
            .name{
              display: inline-block;
              vertical-align: top;
              font-size: 10px;
              color: rgb(147,153,159);
              margin-right: 6px;
              .avatar{
                border-radius: 50%;
              }
            }
          };
          .time{
            margin-bottom: 6px;
            line-height: 12px;
            font-size: 10px;
            color: rgb(147,153,159);
          }
          .text{
            line-height: 16px;
            font-size: 12px;
            color: rgb(7,17,27);
            .icon-thumb_up,.icon-thumb_down{
              margin-right: 4px;
              line-height: 16px;
              font-size: 12px;
              color: rgb(0,160,2200);
            }
            .icon-thumb_down{
              color: rgb(147,153,159);
            }
          }
        }
        .no-rating{
          padding: 16px 0;
          font-size: 12px;
          color: rgb(147,153,159);
        }
      }
    }
  }
</style>
