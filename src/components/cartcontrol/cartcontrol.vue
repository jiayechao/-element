<template>
<div class="cartcontrol">
  <transition name="move">
	<div class="cart-decrease" v-show="food.count>0" @click.stop="decreaseCart($event)">
   <div class="inner icon-remove_circle_outline"></div>
  </div>
  </transition>
	<div class="cart-count">{{food.count>0?food.count:''}}</div>
	<div class="cart-add" @click.stop="addCart($event)">
   <div class="inner icon-add_circle"></div>
  </div>
</div>
</template>

<script>
export default {
  props: {
    food: {
      type: Object
    }
  },
  methods: {
    decreaseCart(event) {
      if (!event._constructed) {
        return;
      };
      this.food.count--;
    },
    addCart(event) {
      if (!event._constructed) {
        return;
      };
      if (!this.food.count) {
        this.$set(this.food, 'count', 1);
      } else {
        this.food.count++;
      }
      this.$emit('cartAdd', event.target);
    }
  }
};
</script>

<style lang="scss">
.cartcontrol{
  font-size: 0;
  .cart-decrease,.cart-add{
    display: inline-block;
    padding: 6px;
    .inner{
      font-size: 24px;
      line-height: 24px;
      color: rgb(0,160,220);
    }
  }
  .cart-decrease{
    opacity: 1;
    transform: translate3d(0,0,0);
    .inner{
      transform: rotate(-360deg);
    }
    &.move-enter-active,&.move-leave-active{
      transition: all 0.5s linear;
      .inner{
        transition: all 0.5s linear;
      }
    }
    &.move-leave-to,&.move-enter {
      opacity: 0;
      transform: translate3d(48px,0,0);
      .inner{
        transform: rotate(0);
      }
    }
  }
  .cart-count{
    display: inline-block;
    vertical-align: top;
    width: 12px;
    padding-top: 6px;
    line-height: 24px;
    text-align: center;
    font-size: 10px;
    color: rgb(147,153,159);
  }
  .cart-add{

  }
}
</style>
