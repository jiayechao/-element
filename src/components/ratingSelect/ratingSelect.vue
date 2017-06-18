<template>
  <div class="ratingSelect">
    <div class="rating-type border-1px">
      <span @click="select(2, $event)" class="block positive" :class="{active:selectType===2}">{{desc.all}} <span class="count">{{ratings.length}}</span> </span>
      <span @click="select(0, $event)" class="block positive" :class="{active:selectType===0}">{{desc.positive}}<span class="count">{{positives.length}}</span></span>
      <span @click="select(1, $event)" class="block negative" :class="{active:selectType===1}">{{desc.negative}}<span class="count">{{negatives.length}}</span></span>
    </div>
    <div class="switch" :class="{on:onlyContent}" @click="toggleContent($event)">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script>
  const POSITIVE = 0;
  const NEGATIVE = 1;
  const ALL = 2;
  export default {
    props: {
      ratings: {
        type: Array,
        default() {
          return [];
        }
      },
      selectType: {
        type: Number,
        default: ALL
      },
      onlyContent: {
        type: Boolean,
        default: false
      },
      desc: {
        type: Object,
        default() {
          return {
            all: '全部',
            positive: '满意',
            negative: '不满意'
          };
        }
      }
    },
    methods: {
      select(type, event) {
        if (!event._constructed) {
          return;
        }
        this.$emit('ratiingType', type);
      },
      toggleContent(event) {
        if (!event._constructed) {
          return;
        }
        this.$emit('content', !this.onlyContent);
      }
    },
    computed: {
      positives() {
        const arr = this.ratings.filter(function(v) {
          return v.rateType === POSITIVE;
        });
        return arr;
      },
      negatives() {
        return this.ratings.filter(function(v) {
          return v.rateType === NEGATIVE;
        });
      }
    }
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
  @import '../../common/sass/mixin.scss';
  .ratingSelect{
    .rating-type{
      padding: 18px 0;
      margin: 0 18px;
      @include border-1px(rgba(7,17,27,0.1));
      font-size: 0;
      .block{
        display: inline-block;
        padding: 8px 12px;
        line-height: 16px;
        border-radius: 2px;
        margin-right: 8px;
        color: rgb(77,85,93);
        font-size:12px;
        &.active{
          color: #fff;
        }
        .count{
          margin-left: 2px;
          font-size: 8px;
        }
        &.positive{
          background: rgba(0,160,220,0.2);
          &.active{
            background: rgba(0,160,220,1);
          }
        }
        &.negative{
          background: rgba(77,85,93,0.2);
          &.active{
            background: rgba(77,85,93,1);
          }
        }
      }
    }
  }
  .switch{
    padding: 12px 8px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7,17,27,0.1);
    color: rgb(147,153,159);
    font-size: 0;
    &.on{
      .icon-check_circle{
        color: #00c850;
      }
    }
    .icon-check_circle{
      display: inline-block;
      font-size: 24px;
      margin-right: 4px;
      vertical-align: top;
    }
    .text{
      display: inline-block;
      font-size: 12px;
      vertical-align: top;
    }
  }
</style>
