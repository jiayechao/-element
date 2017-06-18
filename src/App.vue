<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab">
      <div class="tab-item"><router-link to="/goods">商品</router-link></div>
      <div class="tab-item"><router-link to="/ratings">评论</router-link></div>
      <div class="tab-item"><router-link to="/seller">商家</router-link></div>
    </div>
    <keep-alive>
    <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import header from './components/header/header.vue';
import axios from 'axios';
import {urlParse} from './common/js/urlParse.js';

const ERR_OK = 0;

export default {
  name: 'app',
  components: {
    'v-header': header
  },
  data() {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse();
          return queryParam;
        })()
      }
    };
  },
  created() {
    var _this = this;
    axios.get('/api/seller?id=' + this.seller.id)
    .then(function (response) {
      response = response.data;
      if (response.errno === ERR_OK) {
        _this.seller = Object.assign({}, _this.seller, response.data);
      }
    })
    .catch(function (error) {
      console.log(error);
    });
  }
};
</script>

<style scoped lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  .tab{
    display: flex;
    width: 100%;
    height: 40px;
    line-height: 40px;
    .tab-item{
      flex:1;
      text-align: center;
    }
    .active{
      color: rgb(240,20,20);
    }
  }
}
</style>
