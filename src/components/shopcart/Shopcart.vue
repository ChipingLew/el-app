<template lang="html">
  <div>
    <div class="shopcart">
      <div class="content" @click="toggleList">
        <div class="content-left">
          <div class="logo-wrapper">
            <div class="logo" :class="{'highlight':totalCount>0}">
              <span class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></span>
            </div>
            <div class="number" v-show="totalCount>0">
              {{totalCount}}
            </div>
          </div>
          <div class="price" :class="{'highlight':totalPrice>0}">
            ￥{{totalPrice}}
          </div>
          <div class="description">
            另需配送费￥{{deliveryPrice}}元
          </div>
        </div>
        <div class="content-right" @click.stop.prevent="pay">
          <div class="pay" :class="payClass">
            {{payDesc}}
          </div>
        </div>
      </div>
        <transition-group name="drop" @before-enter="beforeEnter" @enter="enter" @after-enter="afterEnter" tag="div" class="ball-container">
        <div class="ball" v-for="ball in balls" :key="ball" v-show="ball.show" ref="ball">
          <div class="inner inner-hook">
          </div>
        </div>
      </transition-group>
      <transition name="fold">
        <div class="shopcart-list" v-show="listShow">
          <div class="list-header">
            <h1 class="title">购物车</h1>
            <span class="empty" @click="empty">清空</span>
          </div>
          <div class="list-content" ref="listContent">
            <ul>
              <li class="food" v-for="food in selectFoods">
                <span class="name">{{food.name}}</span>
                <div class="price">
                  <span cla>￥{{food.price * food.count}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <v-cartcontrol :food="food"></v-cartcontrol>
                </div>
              </li>
            </ul>
          </div>
        </div>
      </transition>
    </div>
    <transition name="fade" v-on:enter="fadeEnter">
      <div class="list-mask" v-show="listShow" @click="hideList"></div>
    </transition>
  </div>
</template>

<script>
import BScroll from 'better-scroll'
import Cartcontrol from '../cartcontrol/Cartcontrol.vue'

export default {
  props: {
    selectFoods: {
      type: Array,
      default () {
        return []
      }
    },
    deliveryPrice: {
      type: Number,
      default: 0
    },
    minPrice: {
      type: Number,
      default: 0
    }
  },
  data () {
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
        }
      ],
      dropBalls: [],
      fold: true
    }
  },
  computed: {
    totalPrice () {
      let total = 0
      this.selectFoods.forEach((food) => {
        total += food.price * food.count
      })
      return total
    },
    totalCount () {
      let count = 0
      this.selectFoods.forEach((food) => {
        count += food.count
      })
      return count
    },
    payDesc () {
      if (this.totalPrice === 0) {
        return `￥${this.minPrice}元起送`
      } else if (this.totalPrice < this.minPrice) {
        return `还差${this.minPrice - this.totalPrice}元起送`
      } else {
        return '去结算'
      }
    },
    payClass () {
      if (this.totalPrice < this.minPrice) {
        return `not-enough`
      } else {
        return `enough`
      }
    },
    listShow () {
      if (!this.totalCount) {
        this.fold = true
        return false
      }
      let show = !this.fold
      if (show) {
        this.$nextTick(() => {
          if (!this.scroll) {
            this.scroll = new BScroll(this.$refs.listContent, {
              click: true
            })
          } else {
            this.scroll.refresh()
          }
        })
      }
      return show
    }
  },
  methods: {
    empty () {
      this.selectFoods.forEach((food) => {
        food.count = 0
      })
    },
    drop (el) {
      for (let i = 0; i < this.balls.length; i++) {
        let ball = this.balls[i]
        if (!ball.show) {
          ball.show = true
          ball.el = el
          this.dropBalls.push(ball)
          return
        }
      }
    },
    toggleList () {
      if (!this.totalCount) {
        return
      }
      this.fold = !this.fold
    },
    beforeEnter (el) {
      let count = this.balls.length
      while (count--) {
        let ball = this.balls[count]
        if (ball.show) {
          let rect = ball.el.getBoundingClientRect()
          let x = rect.left
          let y = -rect.top
          el.style.display = ''
          el.style.transform = `translate3d(0, ${y}px, 0)`
          el.style.webkitTransform = `translate3d(0, ${y}px, 0)`
          let inner = el.getElementsByClassName('inner-hook')[0]
          inner.style.transform = `translate3d(${x}px, 0, 0)`
          inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`
        }
      }
    },
    enter (el) {
      /* eslint-disable no-unused-vars */
      let rf = el.offsetHeight
      let inner = el.getElementsByClassName('inner-hook')[0]
      this.$nextTick(() => {
        el.style.transform = 'translate3d(0, 0, 0)'
        el.style.webkitTransform = 'translate3d(0, 0, 0)'
        inner.style.transform = 'translate3d(0, 0, 0)'
        inner.style.webkitTransform = 'translate3d(0, 0, 0)'
      })
    },
    afterEnter (el) {
      let ball = this.dropBalls.shift()
      if (ball) {
        ball.show = false
      }
    },
    fadeEnter (el) {
      if (this.listShow) {
        el.style.opacity = '1'
        el.style.backgroundColor = 'rgba(7, 17, 27, .6)'
      }
    },
    hideList () {
      console.log('111')
      this.fold = true
    },
    pay () {
      if (this.totalPrice < this.minPrice) {
        return
      }
      window.alert(`请支付${this.totalPrice}元`)
    }
  },
  components: {
    'v-cartcontrol': Cartcontrol
  }
}
</script>

<style lang="less">
@import "../../assets/styles/mixin.less";
.shopcart {
  position: fixed;
  left: 0;
  bottom: 0;
  z-index: 50;
  width: 100%;
  height: 48px;
  .content {
    display: flex;
    background-color: #141d27;
    font-size: 0;
    color: rgba(255, 255, 255, .4);
    .content-left{
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
        background-color: #141d27;
        .logo {
          width: 100%;
          height: 100%;
          border-radius: 50%;
          background-color: #2b343c;
          text-align: center;
          &.highlight {
            background-color: rgb(0, 160, 220);
          }
          .icon-shopping_cart {
            font-size: 24px;
            line-height: 44px;
            color: #80858a;
            &.highlight {
              color: #fff;
            }
          }
        }
        .number {
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
          background-color: rgb(240, 20, 20);
          box-shadow: 0 2px 8px 0 rgba(0, 0, 0, .4);
        }
      }
      .price {
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin-top: 12px;
        padding-right: 12px;
        box-sizing: border-box;
        border-right: 1px solid rgba(255, 255, 255, .1);
        font-size: 16px;
        font-weight: 700;
        &.highlight {
          color: #fff;
        }
      }
      .description {
        display: inline-block;
        vertical-align: top;
        line-height: 24px;
        margin: 12px 0 0 12px;
        font-size: 10px;
      }
    }
    .content-right {
      flex: 0 0 105px;
      width: 105px;
      background-color: #2b333b;
      .pay {
        height: 48px;
        line-height: 48px;
        text-align: center;
        font-size: 12px;
        &.not-enough {
          background-color: #2b333b;

        }
        &.enough {
          background-color: #00b43c;
          color: #fff;
        }
      }
    }
  }
  .ball-container {
    .ball {
      position: fixed;
      left: 28px;
      bottom: 16px;
      z-index: 200;
      transition: all 0.4s cubic-bezier(0.63, 0.07, 0.89, 0.24);
      .inner {
        width: 25px;
        height: 25px;
        border-radius: 50%;
        background-color: rgb(0, 160, 220);
        transition: all 0.4s linear;
      }
    }
  }
  .shopcart-list {
    position: absolute;
    left: 0;
    top: 0;
    z-index: -1;
    width: 100%;
    transition: all .5s;
    transform: translate3d(0, -100%, 0);
    &.fold-enter, &.fold-leave-to {
      transform: translate3d(0, 0, 0);
    }
    .list-header {
      height: 40px;
      line-height: 40px;
      padding: 0 18px;
      background-color: #f3f5f7;
      border-bottom: 1px solid rgba(7, 17, 27, .1);
      .title {
        float: left;
        font-size: 14px;
        color: rgb(7, 17, 27);
      }
      .empty {
        float: right;
        font-size: 12px;
        color: rgb(0, 160, 220);
      }
    }
    .list-content {
      padding: 18px;
      max-height: 217px;
      background-color: #fff;
      overflow: hidden;
      .food {
        position: relative;
        padding: 12px 0;
        box-sizing: border-box;
        .border-1px(rgba(7, 17, 27, .1));
        .name {
          line-height: 24px;
          font-size: 14px;
          color: rgb(7, 17, 27);
        }
        .price {
          position: absolute;
          right: 98px;
          bottom: 12px;
          line-height: 24px;
          font-weight: 700;
          font-size: 14px;
          color: rgb(240, 20, 20);
        }
        .cartcontrol-wrapper {
          position: absolute;
          right: 0;
          bottom: 6px;
        }
      }
    }
  }
}
.list-mask {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 40;
  backdrop-filter: blur(10px);
  transition: all .5s;
}
</style>
