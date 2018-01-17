<template>
  <transition name="move">
    <div class="foodDetail" v-show="showFlag" ref="foodWrapper">
      <!--为better-scroll多加一层div-->
      <div>
        <!--脑袋上的大图-->
        <div class="image-header">
          <img :src="food.image" alt="大图">
          <div class="back" @click="showFlag = false">
            <span class="icon-left">&lt;</span>
          </div>
        </div>
        <!--商品概要-->
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}</span>
          </div>
          <div class="price">
            <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartcontrol :eventHub="eventHub" :food="food"></cartcontrol>
          </div>
          <transition name="fade">
            <div @click="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <!--商品信息-->
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <!--商品评价-->
        <div class="ratingClass">
          <h1 class="title">商品评价</h1>
          <ratingselect :select-type="selectType"
                        :only-content="onlyContent"
                        :desc="desc"
                        :ratings="food.ratings"
                        @ratingtypeSelect="ratingtypeSelectHandle"
                        @contentToggle="contentToggleHandle">
          </ratingselect>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  import BScroll from 'better-scroll'
  import Vue from 'vue'
  import cartcontrol from '../cartcontrol/cartcontrol'
  import split from '../split/split'
  import ratingselect from '../ratingselect/ratingselect'
  // const POSITIVE = 0
  // const NEGATIVE = 1
  const ALL = 2
  export default {
    name: 'food-detail',
    props: {
      food: {
        type: Object
      },
      eventHub: {
        type: Object
      }
    },
    data () {
      return {
        showFlag: false,
        selectType: ALL,
        onlyContent: false,
        desc: {
          all: '全部',
          positive: '推荐',
          negative: '吐槽'
        }
      }
    },
    methods: {
      // 展示该页面，被外部调用
      show () {
        this.showFlag = true
        this.selectType = ALL
        this.onlyContent = false
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.foodWrapper, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      },
      // 添加购物车
      addFirst (event) {
        if (!event._constructed) {
          return
        }
        this.eventHub.$emit('cart-add', event.target) // 将点击的节点用派发事件的方式发送出去
        Vue.set(this.food, 'count', 1)
      },
      // 接收子组件派发的事件，显示对应类型的评价
      ratingtypeSelectHandle (type) {
        this.selectType = type
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      },
      // 接收子组件派发的事件, 是否显示没有内容的评论
      contentToggleHandle (onlyContent) {
        this.onlyContent = onlyContent
        this.$nextTick(() => {
          this.scroll.refresh()
        })
      }
    },
    components: {
      cartcontrol,
      split,
      ratingselect
    }
  }
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  .foodDetail{
    position: fixed
    top:0;
    bottom: 48px;
    left: 0;
    z-index: 30;
    width: 100%;
    background: #fff;
    transition: all 0.3s linear
    &.move-enter, &.move-leave-active {
      transform: translate3d(100%, 0, 0)
    }
    .image-header{
      position: relative
      width: 100%;
      height: 0;
      padding-top 100%
      img{
        position: absolute
        top:0;
        left:0;
        width: 100%;
        height: 100%
      }
      .back {
        position: absolute
        top: 10px;
        left: 0
        .icon-left {
          display: block
          padding: 10px;
          font-size: 20px;
          color: #fff
        }
      }
    }
    /*商品概要*/
    .content {
      position: relative;
      padding: 18px;
      .title {
        line-height: 14px;
        margin-bottom: 8px;
        font-size: 14px;
        font-weight: 700;
        color: rgb(7, 17, 27)
      }
      .detail {
        margin-bottom: 18px;
        line-height: 10px;
        height: 10px;
        font-size: 0;
        .sell-count, .rating {
          font-size: 10px;
          color: rgb(147, 153, 159)
        }
        .sell-count {
          margin-right: 12px;
        }
      }
      .price {
        font-weight: 700px
        line-height: 24px;
        .now {
          margin-right: 8px;
          font-size: 14px;
          color: rgb(240, 20, 20)
        }
        .old {
          text-decoration: line-through
          font-size: 10px;
          color: rgb(147, 153, 159)
        }
      }
      .cartcontrol-wrapper {
        position: absolute
        right: 12px;
        bottom: 12px;
      }
      .buy {
        position: absolute;
        right: 18px;
        bottom: 18px;
        z-index: 10;
        height: 24px;
        line-height: 24px;
        padding: 0 12px;
        box-sizing: border-box
        border-radius: 12px;
        font-size: 10px;
        color: #fff
        background: rgb(0, 160, 220)
        transition: all 0.2s
        &.fade-enter-active {
          opacity: 0
        }
      }
    }
    /*商品信息*/
    .info {
      padding: 18px;
      .title {
        line-height: 14px;
        margin-bottom: 6px;
        font-size: 14px;
        color: rgb(7, 17, 27)
      }
      .text {
        line-height: 24px;
        padding: 0 8px;
        font-size: 12px;
        color: rgb(77, 85, 93)
      }
    }
    .ratingClass {
      padding-top: 18px;
      .title {
        line-height: 14px;
        margin-left: 18px;
        font-size: 14px;
        color: rgb(7, 17, 27)
      }
    }
  }
</style>