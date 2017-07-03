<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <section class="tab">
      <router-link to="/goods" class="tab-item">商品</router-link>
      <router-link to="/ratings" class="tab-item">评论</router-link>
      <router-link to="/seller" class="tab-item">商家</router-link>
    </section>
    <keep-alive>
      <router-view :seller="seller"></router-view>
    </keep-alive>
  </div>
</template>

<script>
import Header from './components/header/Header.vue'
import axios from 'axios'
import {urlParse} from './assets/js/util.js'
const ERR_NO = 0
export default {
  name: 'app',
  data () {
    return {
      seller: {
        id: (() => {
          let queryParam = urlParse()
          return queryParam.id
        })()
      }
    }
  },
  created () {
    axios.get('/api/seller?id=' + this.seller.id, JSON).then((response) => {
      if (response.data.errno === ERR_NO) {
        // this.seller = response.data.seller
        this.seller = Object.assign({}, this.seller, response.data.seller)
      }
    })
  },
  components: {
    'v-header': Header
  }
}
</script>

<style lang="less" scoped>
@import "./assets/styles/index.less";

</style>
