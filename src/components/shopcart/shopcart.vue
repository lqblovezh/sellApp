<template>
    <div class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper" :class="{'highLight':totalCount>0}">
                    <div class="logo">
                        <i class="icon-shopping_cart"></i>
                    </div>
                    <div class="num" v-if="totalCount>0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highLight':totalPrice>0}">￥{{totalPrice}}</div>
                <div class="desc">另需配送费{{deliveryPrice}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" @click.stop.prevent="pay" :class="{'highLight':totalPrice>=minPrice}">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <div class="ball-container">
            <div v-for="(ball,index) in balls">
                <transition name="drop" @before-enter="beforeDrop" @enter="dropping" @after-enter="afterDrop" :key="index">
                    <div class="ball" v-show="ball.show">
                        <div class="inner inner-hook"></div>
                    </div>
                </transition>
            </div>
        </div>
        <transition name="fold">
            <div class="shopcart-list" v-show="listShow" ref="shopcartList">
                <div class="list-header">
                    <h1 class="title">购物车</h1>
                    <span class="empty" @click="empty">清空</span>
                </div>
                <div class="list-content" ref="listContent">
                    <ul>
                        <li class="food" v-for="food in selectFoods">
                            <span class="name">{{food.name}}</span>
                            <div class="price">
                                <span>￥{{food.price*food.count}}</span>
                            </div>
                            <div class="cartcontrol-wrapper">
                                <v-cartcontrol @add="addFood" :food="food"></v-cartcontrol>
                            </div>
                        </li>
                    </ul>
                </div>
            </div>
        </transition>
        <transition name="fade">
            <div class="list-mask" @click="fold = true" ref="listMask" v-show="listShow"></div>
        </transition>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import cartcontrol from './../cartcontrol/cartcontrol';
    export default {
        props: {
            selectFoods: {
                type: Array,
                default () {
                    return [];
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
            };
        },
        computed: {
            totalPrice () {
                let total = 0;
                this.selectFoods.forEach((food) => {
                    total += food.price * food.count;
                });
                return total;
            },
            totalCount () {
                let total = 0;
                this.selectFoods.forEach((food) => {
                    total += food.count;
                });
                return total;
            },
            payDesc () {
                if (this.totalPrice === 0) {
                    return '￥' + this.minPrice + '元起送';
                } else if (this.totalPrice < this.minPrice) {
                    return '还差￥' + (this.minPrice - this.totalPrice) + '元起送';
                } else {
                    return '去结算';
                }
            },
            listShow () {
                if (!this.totalCount) {
                    this.fold = true;
                    return false;
                }
                let show = !this.fold;
                if (show) {
                    this.$nextTick(() => {
                        if (!this.scroll) {
                            this.scroll = new BScroll(this.$refs.listContent, {
                                click: true
                            });
                        } else {
                            this.scroll.refresh();
                        }
                    });
                }
                return show;
            }
        },
        methods: {
            drop (el) {
                for (let i = 0; i < this.balls.length; i++) {
                    let ball = this.balls[i];
                    if (!ball.show) {
                        ball.show = true;
                        ball.el = el;
                        this.dropBalls.push(ball);
                        return;
                    }
                }
            },
            beforeDrop (el) {
                let count = this.balls.length;
                while (count--) {
                    let ball = this.balls[count];
                    if (ball.show) {
                        let rect = ball.el.getBoundingClientRect();
                        let x = (rect.left - 32);
                        let y = -(window.innerHeight - rect.top - 22);
                        console.log(x + '--' + y);
                        el.style.display = 'none';
                        el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                        el.style.transform = `translate3d(0,${y}px,0)`;
                        let inner = el.querySelector('.inner-hook');
                        inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
                        inner.style.transform = `translate3d(${x}px,0,0)`;
                    }
                }
            },
            dropping (el) {
                el.offsetHeight;
                this.$nextTick(() => {
                    el.style.webkitTransform = 'translate3d(0,0,0)';
                    el.style.transform = 'translate3d(0,0,0)';
                    let inner = el.querySelector('.inner-hook');
                    inner.style.webkitTransform = 'translate3d(0,0,0)';
                    inner.style.transform = 'translate3d(0,0,0)';
                });
            },
            afterDrop (el) {
                let ball = this.dropBalls.shift();
                if (ball) {
                    ball.show = false;
                    el.style.display = 'none';
                }
            },
            addFood(target) {
                this.drop(target);
            },
            toggleList () {
                if (!this.totalCount) {
                    return;
                }
                this.fold = !this.fold;
            },
            empty() {
                this.selectFoods.forEach((food) => {
                    food.count = 0;
                });
            },
            pay() {
                if (this.totalPrice < this.minPrice) {
                    return;
                }
                window.alert(`支付${this.totalPrice}元`);
            }
        },
        components: {
            'v-cartcontrol': cartcontrol
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/mixin.styl"
    .shopcart
        height: .96rem
        width: 100%
        z-index: 50
        left: 0
        position: fixed
        bottom: 0
        .content
            height: .96rem
            display: flex
            font-style: 0
            background: #141d27
            position: absolute
            z-index: 2;
            width: 100%
            color: rgba(255, 255, 255, .4)
            .content-left
                flex: 1
                .logo-wrapper
                    display: inline-block
                    vertical-align: top
                    position: relative
                    top: -.2rem
                    margin: 0 .24rem
                    padding .12rem
                    width: 1.12rem
                    height: 1.12rem
                    box-sizing: border-box
                    border-radius: 50%
                    background: #141d27
                    &.highLight
                        .logo
                            background: #00a0dc
                            i
                                color: #fff
                    .logo
                        width: 100%
                        height: 100%
                        border-radius: 50%
                        background: #2b343c
                        text-align: center
                        .icon-shopping_cart
                            font-size: .48rem
                            color: #80858a
                            line-height: .88rem
                            &.highLight
                                color: #fff
                    .num
                        position: absolute
                        top: 0
                        right: 0
                        width: .48rem
                        height: .32rem
                        line-height: .32rem
                        border-radius: .12rem
                        font-weight: 700
                        font-size: .18rem
                        box-shadow: 0 .08rem .16rem 0 rgba(0, 0, 0, 0.4)
                        background: rgb(240, 20, 20)
                        text-align: center
                        color: #fff
                .price
                    display: inline-block
                    vertical-align: top
                    line-height: .48rem
                    margin-top: .24rem
                    padding-right: .24rem
                    box-sizing: border-box
                    border-right: 1px solid rgba(255, 255, 255, .1)
                    font-size: .32rem
                    font-weight: 700
                    color: rgba(255, 255, 255, .4)
                    &.highLight
                        color: #fff
                .desc
                    display: inline-block
                    vertical-align: top
                    line-height: 0.96rem
                    margin-left: 0.24rem
                    font-size: .2rem
                    font-weight: 700
            .content-right
                flex: 0 0 2.1rem
                width: 2.1rem
                .pay
                    height: .96rem
                    line-height: .96rem
                    text-align: center
                    font-style: .24rem
                    font-weight: 700
                    background: #2b333b
                    &.highLight
                        color: #fff
                        background: #00b43c
        .ball-container
            .ball
                position: fixed
                left: .64rem
                bottom: .44rem
                z-index: 200
                &.drop-enter, &.drop-enter-active
                    transition: all 0.4s cubic-bezier(0.49, -0.29, 0.75, 0.41)
                    .inner-hook
                        border-radius: 50%
                        width: .32rem
                        height: .32rem
                        background: rgb(0, 160, 220)
                        transition: all 0.4s linear
        .shopcart-list
            position: absolute
            left: 0
            top: 0
            z-index: 1
            width: 100%
            transform: translate3d(0, -100%, 0)
            &.fold-enter-active, &.fold-leave-active
                transition: all 0.5s
            &.fold-enter, &.fold-leave-active
                transform: translate3d(0, 0, 0)
            .list-header
                height: .8rem
                line-height: .8rem
                padding: 0 .36rem
                background: #f3f5f7
                border-bottom: 1px solid rgba(7, 17, 27, 0.1)
                .title
                    float: left
                    font-size: .28rem
                    color: rgb(7, 17, 27)
                .empty
                    float: right
                    font-size: .24rem
                    color: rgb(0, 160, 220)
            .list-content
                padding: 0 .36rem
                max-height: 4.34rem
                overflow: hidden
                background: #fff
                .food
                    position: relative
                    padding: .24rem 0
                    box-sizing: border-box
                    border-1px(rgba(7, 17, 27, 0.1))
                    .name
                        line-height: .48rem
                        font-size: .28rem
                        color: rgb(7, 17, 27)
                    .price
                        position: absolute
                        right: 1.8rem
                        bottom: .24rem
                        line-height: .48rem
                        font-size: .28rem
                        font-weight: 700
                        color: rgb(240, 20, 20)
                    .cartcontrol-wrapper
                        position: absolute
                        right: 0
                        bottom: .12rem
        .list-mask
            position: fixed
            top: 0
            left: 0
            width: 100%
            height: 100%
            z-index: 0
            backdrop-filter: blur(10px)
            background: rgba(7, 17, 27, 0.6)
            &.fade-enter-active, &.fade-leave-active
                transition: all 0.5s ease
            &.fade-enter, &.fade-leave-active
                opacity: 0
                background: rgba(7, 17, 27, 0)
</style>
