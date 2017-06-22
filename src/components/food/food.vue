<template>
    <transition name="move">
        <div v-show="showFlag" class="food" ref="food">
            <div class="food-content">
                <div class="image-header">
                    <img :src="food.image">
                    <div class="back" @click="hide">
                        <i class="icon-arrow_lift"></i>
                    </div>
                </div>
                <div class="content">
                    <h1 class="title">{{food.name}}</h1>
                    <div class="detail">
                        <span class="sell-count">月售{{food.sellCount}}份</span>
                        <span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <v-cartcontrol @add="addFood" :food="food"></v-cartcontrol>
                    </div>
                    <transition name="fade">
                        <div @click.stop.prevent="addFirst" class="buy" v-show="!food.count || food.count===0">加入购物车
                        </div>
                    </transition>
                </div>
                <v-split v-show="food.info"></v-split>
                <div class="info" v-show="food.info">
                    <h1 class="title">商品信息</h1>
                    <p class="text">{{food.info}}</p>
                </div>
                <v-split></v-split>
                <div class="rating">
                    <h1 class="title">商品评价</h1>
                    <v-ratingselect @select="selectRating" @toggle="toggleContent" :selectType="selectType"
                                    :onlyContent="onlyContent" :desc="desc"
                                    :ratings="food.ratings"></v-ratingselect>
                    <div class="rating-wrapper">
                        <ul v-show="food.ratings && food.ratings.length">
                            <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in food.ratings"
                                class="rating-item border-1px">
                                <div class="user">
                                    <span class="name">{{rating.username}}</span>
                                    <img class="avatar" :src="rating.avatar">
                                </div>
                                <div class="time">{{rating.rateTime | formatDate}}</div>
                                <p class="text">
                                    <span :class="{'icon-thumb_up':rating.rateType===0,'icon-thumb_down':rating.rateType===1}"></span>{{rating.text}}
                                </p>
                            </li>
                        </ul>
                        <div class="no-rating" v-show="!food.ratings || !food.ratings.length">暂无评价</div>
                    </div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import split from './../split/split';
    import Vue from 'vue';
    import ratingselect from './../ratingselect/ratingselect';
    import cartcontrol from './../cartcontrol/cartcontrol';

    const ALL = 2;

    export default {
        props: {
            food: {
                type: Object
            }
        },
        data () {
            return {
                showFlag: false,
                selectType: ALL,
                onlyContent: true,
                desc: {
                    all: '全部',
                    positive: '推荐',
                    negative: '吐槽'
                }
            };
        },
        methods: {
            show () {
                this.showFlag = true;
                this.selectType = ALL;
                this.onlyContent = true;
                this.$nextTick(() => {
                    if (!this.scroll) {
                        this.scroll = new BScroll(this.$refs.food, {
                            click: true
                        });
                    } else {
                        this.scroll.refresh();
                    }
                });
            },
            hide () {
                this.showFlag = false;
            },
            addFirst () {
                if (!event._constructed) {
                    return;
                }
                this.$emit('add', event.target);
                Vue.set(this.food, 'count', 1);
            },
            needShow(type, text) {
                if (this.onlyContent && !text) {
                    return false;
                }
                if (this.selectType === ALL) {
                    return true;
                } else {
                    return type === this.selectType;
                }
            },
            addFood(target) {
                this.$emit('add', target);
            },
            selectRating (type) {
                this.selectType = type;
                this.$nextTick(() => {
                    this.scroll.refresh();
                });
            },
            toggleContent () {
                this.onlyContent = !this.onlyContent;
                this.$nextTick(() => {
                    this.scroll.refresh();
                });
            }
        },
        components: {
            'v-cartcontrol': cartcontrol,
            'v-split': split,
            'v-ratingselect': ratingselect
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/mixin.styl"
    .food
        position: fixed
        width: 100%
        left: 0
        top: 0
        bottom: .96rem
        z-index: 30
        background: #fff
        &.move-enter-active, &.move-leave-active
            transition: all 0.2s linear
            transform: translate3d(0, 0, 0)
        &.move-enter, &.move-leave-active
            transform: translate3d(100%, 0, 0)
        .image-header
            position: relative
            width: 100%
            height: 0
            padding-top: 100%
            img
                position: absolute
                top: 0
                left: 0
                width: 100%
                height: 100%
            .back
                position: absolute
                top: .2rem
                left: 0
                .icon-arrow_lift
                    display: block
                    padding: .2rem
                    font-size: .4rem
                    color: #fff
        .content
            position: relative
            padding: .36rem
            .title
                line-height: .28rem
                margin-bottom: .16rem
                font-size: .28rem
                font-weight: 700
                color: rgb(7, 17, 27)
            .detail
                margin-bottom: .36rem
                line-height: .2rem
                height: .2rem
                font-size: 0
                .sell-count, .rating
                    font-size: .2rem
                    color: rgb(147, 153, 159)
                .sell-count
                    margin-right: .24rem
            .price
                font-weight: 700
                line-height: .48rem
                .now
                    margin-right: .16rem
                    font-size: .28rem
                    color: rgb(240, 20, 20)
                .old
                    text-decoration: line-through
                    font-size: .2rem
                    color: rgb(147, 153, 159)
            .cartcontrol-wrapper
                position: absolute
                right: .24rem
                bottom: .24rem
            .buy
                position: absolute
                right: .36rem
                bottom: .36rem
                z-index: 10
                height: .48rem
                line-height: .48rem
                padding: 0 .24rem
                box-sizing: border-box
                border-radius: .24rem
                font-size: .2rem
                color: #fff
                background: rgb(0, 160, 220)
                &.fade-enter-active, &.fade-leave-active
                    transition: all 0.2s
                    opacity: 1
                &.fade-enter, &.fade-leave-active
                    opacity: 0
        .info
            padding: .36rem
            .title
                line-height: .28rem
                margin-bottom: .12rem
                font-size: .28rem
                color: rgb(7, 17, 27)
            .text
                line-height: .48rem
                padding: 0 .16rem
                font-size: .24rem
                color: rgb(77, 85, 93)
        .rating
            padding-top: .36rem
            .title
                line-height: .28rem
                margin-left: .36rem
                font-size: .28rem
                color: rgb(7, 17, 27)
            .rating-wrapper
                padding: 0 .36rem
                .rating-item
                    position: relative
                    padding: .32rem 0
                    border-1px(rgba(7, 17, 27, 0.1))
                    .user
                        position: absolute
                        right: 0
                        top: .32rem
                        line-height: .24rem
                        font-size: 0
                        .name
                            display: inline-block
                            margin-right: .12rem
                            vertical-align: top
                            font-size: .2rem
                            color: rgb(147, 153, 159)
                        .avatar
                            width: .24rem
                            height: .24rem
                            border-radius: 50%
                    .time
                        margin-bottom: .12rem
                        line-height: .24rem
                        font-size: .2rem
                        color: rgb(147, 153, 159)
                    .text
                        line-height: .32rem
                        font-size: .24rem
                        color: rgb(7, 17, 27)
                        .icon-thumb_up, .icon-thumb_down
                            margin-right: .08rem
                            line-height: .32rem
                            font-size: .24rem
                        .icon-thumb_up
                            color: rgb(0, 160, 220)
                        .icon-thumb_down
                            color: rgb(147, 153, 159)
                .no-rating
                    padding: .32rem 0
                    font-size: .24rem
                    color: rgb(147, 153, 159)
</style>
