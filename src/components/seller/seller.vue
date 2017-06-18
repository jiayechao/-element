<template>
  <div class="seller" ref="seller">
    <div class="seller-wrapper">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc border-1px">
          <star :size="24" :score="seller.score"></star>
          <span class="text">（{{seller.ratingCount}}）</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="content">
              <span class="stress">{{seller.deliveryTime}}</span>分钟
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite($event)">
          <span class="icon icon-favorite" :class="{'active':favorite}"></span>
          <span class="text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="title">公告与活动</h1>
        <div class="content-wrapper border-1px">
          <p class="content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="supports">
          <li class="support-item border-1px" v-for="(item,index) in seller.supports">
            <span class="icon" :class="classMap[seller.supports[index].type]"></span>
            <span class="text">{{seller.supports[index].description}}</span>
          </li>
        </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" heigh="90" alt="">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="title border-1px">商家信息</h1>
        <ul>
          <li class="info-item border-1px" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
  import star from '../star/star.vue';
  import split from '../split/split.vue';
  import BScroll from 'better-scroll';
  import {saveToLocal, loadFormLocal} from '../../common/js/store';
  export default {
    created() {
      this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    },
    props: {
      seller: {
        type: Object
      }
    },
    data() {
      return {
        favorite: (() => {
          return loadFormLocal(this.seller.id, 'favorite', false);
        })()
      };
    },
    computed: {
      favoriteText() {
        return this.favorite ? '已收藏' : '收藏';
      }
    },
    watch: {
      'seller'() {
        this._initScroll();
        this._initPicScroll();
      }
    },
    // bscroll完全依赖DOM
    // 这个方法优先于watch
    mounted() {
      this._initScroll();
      this._initPicScroll();
    },
    components: {
      star,
      split
    },
    methods: {
      _initScroll() {
        if (!this.scroll) {
          this.scroll = new BScroll(this.$refs.seller, {
            click: true
          });
        } else {
          this.$nextTick(() => {
            this.scroll.refresh();
          });
        }
      },
      _initPicScroll() {
        if (this.seller.pics) {
          let picWidth = 120;
          let margin = 6;
          let width = (picWidth + margin) * this.seller.pics.length - margin;
          console.log(width);
          this.$refs.picList.style.width = width + 'px';
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              click: true,
              scrollX: true,
              eventPassthrough: 'vertical'
            });
          } else {
            this.$nextTick(() => {
              this.picScroll.refresh();
            });
          }
        }
      },
      toggleFavorite(event) {
        if (!event._constructed) {
          return;
        };
        this.favorite = !this.favorite;
        saveToLocal(this.seller.id, 'favorite', this.favorite);
      }
    }
  };
</script>
<style scoped lang="scss">
  @import '../../common/sass/mixin.scss';
  .seller{
    position: absolute;
    top:174px;
    bottom: 0;
    width: 100%;
    left: 0;
    overflow: hidden;
    .overview{
      padding: 18px;
      position: relative;
      .title{
        margin-bottom: 8px;
        line-height: 14px;
        color: rgb(7,17,27);
        font-size: 14px;
      }
      .desc{
        padding-bottom: 18px;
        @include border-1px(rgba(7,17,27,0.1));
        font-size: 0;
        .star{
          display: inline-block;
          margin-right: 0px;
          vertical-align: top;
        }
        .text{
          display: inline-block;
          margin-left: 12px;
          line-height: 18px;
          vertical-align: top;
          font-size: 12px;
          color: rgb(7,85,93);
          &:nth-of-type(1){
            margin-left: 0;
          }
        }
      }
      .remark{
        display: flex;
        padding-top: 18px;
        .block{
          flex: 1;
          text-align: center;
          border-right: 1px solid rgba(7,17,27,0.1);
          &:last-child{
            border: none;
          }
          h2{
            margin-bottom: 4px;
            line-height: 14px;
            font-size: 10px;
            color: rgb(147,153,159);
          }
          .content{
            line-height: 24px;
            font-size: 10px;
            color: rgb(7,17,27);
            .stress{
              font-size: 18px;
            }
          }
        }
      }
      .favorite{
        position: absolute;
        right: 11px;
        top: 10px;
        width: 30px;
        text-align: center;
        .icon{
          display: block;
          line-height: 24px;
          font-size: 24px;
          color: #d4d6d9;
          margin-bottom: 4px;
          &.active{
            color: rgb(240,20,20);
          }
        }
        .text{
          line-height: 10px;
          font-size: 10px;
          color: rgb(77,85,93);
        }
      }
    }
    .bulletin{
      padding: 18px 18px 0 18px;
      .title{
        margin-bottom: 8px;
        line-height: 14px;
        color: rgb(7,17,27);
        font-size: 14px;
      }
      .content-wrapper{
        padding: 0 12px 16px 12px;
        @include border-1px(rgba(7,17,27,0.1));
        .content{
          line-height: 24px;
          font-size: 12px;
          color: rgb(147,153,159);
        }
      }
      .supports{
        .support-item{
          padding: 16px 12px;
          @include border-1px(rgba(7,17,27,0.1));
          &:last-child{
            border: none;
          }
          font-size: 0;
          &:last-child{
            margin-bottom: 0;
          }
          .icon{
            display: inline-block;
            width: 16px;
            height: 16px;
            vertical-align: top;
            margin-right: 6px;
            background-size: 16px 16px;
            background-repeat: no-repeat;
            &.decrease{
              @include bg-image('decrease_4');
            };
            &.discount{
              @include bg-image('discount_4');
            };
            &.guarantee{
              @include bg-image('guarantee_4');
            };
            &.invoice{
              @include bg-image('invoice_4');
            };
            &.special{
              @include bg-image('special_4');
            };
          }
          .text{
            font-size: 12px;
            line-height: 16px;
            color: rgb(7,17,27)
          }
        }
      }
    }
    .pics{
      padding: 18px;
      .title{
        margin-bottom: 12px;
        line-height: 14px;
        color: rgb(7,17,27);
        font-size: 14px;
      }
      .pic-wrapper{
        width: 100%;
        overflow: hidden;
        white-space: nowrap;
        .pic-list{
          font-size: 0;
          .pic-item{
            display: inline-block;
            margin-right: 6px;
            width: 120px;
            height: 90px;
            &:last-child{
              margin-right: 0;
            }
          }
        }
      }
    }
    .info{
      padding: 18px 18px 0 18px;
      .title{
        padding-bottom: 12px;
        line-height: 14px;
        color: rgb(7,17,27);
        font-size: 14px;
        @include border-1px(rgba(7,17,27,0.1));
      }
      .info-item{
        padding: 16px 12px;
        line-height: 16px;
        @include border-1px(rgba(7,17,27,0.1));
        font-size: 12px;
        &:last-child{
          border: none;
        }
      }
    }
  }
</style>
