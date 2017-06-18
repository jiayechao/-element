<template>
  <div class="header">
    <div class="content-wrapper">
    	<div class="avator">
    		<img width="64" height="64" :src="seller.avatar">
    	</div>
    	<div class="content">
    		<div class="title">
    			<span class="brand"></span>
    			<span class="name">{{seller.name}}</span>
    		</div>
    		<div class="description">
    			{{seller.description}}/{{seller.deliveryTime}}分钟送达
    		</div>
    		<div v-if="seller.supports" class="support">
    			<span class="icon" :class="classMap[seller.supports[0].type]"></span>
    			<span class="text">{{seller.supports[0].description}}</span>
    		</div>
    	</div>
		<div v-if="seller.supports" class="support-count" @click="showDetail">
			<span class="count">{{seller.supports.length}}个</span>
			<i class="icon-keyboard_arrow_right"></i>
		</div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
    	<span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
    	<i class="icon-keyboard_arrow_right"></i>
    </div>
    <div class="background">
    	<img :src="seller.avatar" width="100%" height="100%" alt="">
    </div>
    <transition name="fade">
    <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <star :size="36" :score="seller.score"></star>
          </div>
          <div class="title">
            <div class="line"></div>
            <div class="text">优惠精选</div>
            <div class="line"></div>
          </div>
          <ul v-if="seller.supports" class="supports">
            <li class="support-item" v-for="(item,index) in seller.supports">
              <span class="icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
          <div class="title">
            <div class="line"></div>
            <div class="text">商家公告</div>
            <div class="line"></div>
          </div>
          <div class="bulletin">
            <p class="content">{{seller.bulletin}}</p>
          </div>
        </div>
      </div>
      <div class="detail-close" @click="hideDetail">
        <i class="icon-close"></i>
      </div>
    </div>
    </transition>
  </div>
</template>

<script>
import star from '../star/star.vue';
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data() {
    return {
      detailShow: false
    };
  },
  methods: {
    showDetail() {
      this.detailShow = true;
    },
    hideDetail() {
      this.detailShow = false;
    }
  },
  created() {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  components: {
    star: star
  }
};

</script>
<style scoped lang="scss">
@import '../../common/sass/mixin.scss';
.header{
	position: relative;
	color: #fff;
	background: rgba(7,17,27,0.5);
	overflow: hidden;
	.content-wrapper{
		position: relative;
		padding: 24px 12px 18px 24px;
		font-size: 0;
		.avator{
			display: inline-block;
			vertical-align: top;
			img{
				border-radius: 2px;
			}
		}
		.content{
			display: inline-block;
			margin-left: 16px;
			.title{
				margin: 2px 0 8px 0;
				.brand{
					display: inline-block;
					vertical-align: top;
					width: 30px;
					height: 18px;
					@include bg-image('brand');
					background-size: 100%;
				}
				.name{
					margin-left: 6px;
					font-size: 16px;
					line-height: 18px;
					font-weight: bold;
				}
			}
			.description{
				margin-bottom: 10px;
				line-height: 12px;
				font-size: 12px;
			}
			.support{
				.icon{
					display: inline-block;
					vertical-align: top;
					width: 12px;
					height: 12px;
					margin-right: 4px;
					background-size: 12px 12px;
					background-repeat: no-repeat;
					&.decrease{
						@include bg-image('decrease_1');
					};
					&.discount{
						@include bg-image('discount_1');
					};
					&.guarantee{
						@include bg-image('guarantee_1');
					};
					&.invoice{
						@include bg-image('invoice_1');
					};
					&.special{
						@include bg-image('special_1');
					};
				}
				.text{
					line-height: 12px;
					font-size: 12px;
				}
			}
		}
		.support-count{
			position: absolute;
			right: 12px;
			bottom: 14px;
			padding: 0 9px;
			height: 24px;
			line-height: 24px;
			border-radius: 14px;
			background-color: rgba(0,0,0,0.2);
			text-align: center;
			.count{
				font-size: 10px;
				vertical-align: top;
			}
			.icon-keyboard_arrow_right{
				margin-left: 2px;
				line-height: 24px;
				font-size: 10px;
			}
		}
	}
	.bulletin-wrapper{
		height: 28px;
		line-height: 28px;
		padding: 0 22px 0 12px;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		position: relative;
		background: rgba(7,17,27,0.2);
		.bulletin-title{
			display: inline-block;
			width: 22px;
			height: 12px;
			background-size: 100%;
			background-repeat: no-repeat;
			@include bg-image('bulletin');
			vertical-align: top;
			margin-top: 8px;
		}
		.bulletin-text{
			vertical-align: top;
			margin: 0 4px;
			font-size: 10px;
		}
		.icon-keyboard_arrow_right{
			position: absolute;
			top: 8px;
			right: 12px;
			font-size: 10px;
		}
	}
	.background{
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		z-index: -1;
		filter: blur(10px);
	}
	.detail{
		position: fixed;
		z-index: 100;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		overflow: auto;
		background: rgba(7,17,27,0.8);
    opacity: 1;
    backdrop-filter: blur(10px);
    &.fade-enter-active, &.fade-leave-active{
      transition: all 0.5s;
    }
    &.fade-enter,&.fade-leave-to{
      opacity: 0;
      background: rgba(7,17,27,0);
    }
    .detail-wrapper{
      min-height: 100%;
      width: 100%;
      .detail-main{
        margin-top: 64px;
        padding-bottom: 64px;
        .name{
          line-height: 16px;
          text-align: center;
          font-size: 16px;
          font-weight: 700;
        }
        .star-wrapper{
          margin-top: 18px;
          padding: 2px 0;
          text-align: center;
        }
        .title{
          display: flex;
          width: 80%;
          margin: 28px auto 24px auto;
          .line{
            flex: 1;
            position: relative;
            top: -6px;
            border-bottom: 1px solid rgba(255,255,255,0.3);
          }
          .text{
            padding: 0 12px;
            font-size: 14px;
          }
        }
        .supports{
          width: 80%;
          margin: 0 auto;
          .support-item{
            padding: 0 12px;
            margin-bottom: 12px;
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
                @include bg-image('decrease_2');
              };
              &.discount{
                @include bg-image('discount_2');
              };
              &.guarantee{
                @include bg-image('guarantee_2');
              };
              &.invoice{
                @include bg-image('invoice_2');
              };
              &.special{
                @include bg-image('special_2');
              };
            }
            .text{
              font-size: 12px;
              line-height: 16px;
            }
          }
        }
        .bulletin{
          width: 80%;
          margin: 0 auto;
          .content{
            padding: 0 12px;
            line-height: 24px;
            font-size: 12px;
          }
        }
      }
    }
    .detail-close{
      position: relative;
      width: 32px;
      height: 32px;
      margin: -64px auto 0;
      clear: both;
      font-size: 32px;
    }
	}
}
</style>
