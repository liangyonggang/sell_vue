<template>
  <div id="app">
    <v-header :seller="seller"></v-header>
    <div class="tab border-1px">
      <router-link tag="div" to="/" class="tab-item" activeClass="on" exact>商品</router-link>
      <router-link tag="div" to="/ratings" class="tab-item" activeClass="on" exact>评论</router-link>
      <router-link tag="div" to="/seller" class="tab-item" activeClass="on" exact>商家</router-link>
    </div>
    <keep-alive>
      <router-view :seller="seller"/>
    </keep-alive>
  </div>
</template>

<script>
import {urlParse} from './common/js/util'
import vheader from './components/header/header'
export default {
  name: 'app',
  data () {
    return {
      seller: {
        id: (() => {
          if (window.location.search) {
            return urlParse().id
          } else {
            return 1
          }
        })()
      }
    }
  },
  created () {
    this.$http.get('./static/data/data.json').then(response => {
      this.seller = Object.assign({}, this.seller, response.data.seller)
    }, error => {
      console.log(error)
    }).catch(err => {
      console.log(err)
    })
  },
  components: {
    'v-header': vheader
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "./common/stylus/mixin.styl"
@import "./common/stylus/base.styl"
html,body{
  margin: 0;
  font-weight:200;
  line-height:1
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  .tab{
    display: flex;
    height: 40px;
    line-height: 40px;
    border-1px(rgba(7,17,27,0.1))
    .tab-item{
      flex: 1;
      text-align center;
      font-size 14px;
      color: rgb(77, 85, 93);
      &.on{
        color: rgb(240, 20, 20);
      }
    }
  }
}
</style>
