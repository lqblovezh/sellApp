<template>
    <div class="ratings" ref="ratings">
        <div class="ratings-content">
            <div class="overview">
                <div class="overview-left">
                    <h1 class="score">{{seller.score}}</h1>
                    <div class="title">综合评分</div>
                    <div class="rank">高于周边商家{{seller.rankRate}}%</div>
                </div>
                <div class="overview-right">
                    <div class="score-wrapper">
                        <span class="title">服务态度</span>
                        <v-star :size="36" :score="seller.serviceScore"></v-star>
                        <span class="score">{{seller.serviceScore}}</span>
                    </div>
                    <div class="score-wrapper">
                        <span class="title">商品评分</span>
                        <v-star :size="36" :score="seller.foodScore"></v-star>
                        <span class="score">{{seller.foodScore}}</span>
                    </div>
                    <div class="delivery-wrapper">
                        <span class="title">送达时间</span>
                        <span class="delivery">{{seller.deliveryTime}}分钟</span>
                    </div>
                </div>
            </div>
            <v-split></v-split>
            <v-ratingselect @select="selectRating" @toggle="toggleContent" :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="ratings"></v-ratingselect>
            <div class="rating-wrapper">
                <ul>
                    <li v-show="needShow(rating.rateType,rating.text)" v-for="rating in ratings" class="rating-item">
                        <div class="avatar">
                            <img :src="rating.avatar">
                        </div>
                        <div class="content">
                            <h1 class="name">{{rating.username}}</h1>
                            <div class="star-wrapper">
                                <v-star :size="24" :score="rating.score"></v-star>
                                <span class="delivery" v-show="rating.deliveryTime">{{rating.deliveryTime}}分钟送达</span>
                            </div>
                            <p class="text">{{rating.text}}</p>
                            <div class="recommend" v-show="rating.recommend && rating.recommend.length">
                                <span class="icon-thumb_up"></span>
                                <span class="item" v-for="item in rating.recommend">{{item}}</span>
                            </div>
                            <div class="time">
                                {{rating.rateTime | formatDate}}
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import star from './../star/star.vue';
    import split from './../split/split';
    import Vue from 'vue';
    import ratingselect from './../ratingselect/ratingselect';
    const ALL = 2;
    export default {
        props: {
            seller: {
                type: Object
            }
        },
        data () {
            return {
                ratings: [],
                selectType: ALL,
                onlyContent: true,
                desc: {
                    all: '全部',
                    positive: '满意',
                    negative: '不满意'
                }
            };
        },
        mounted () {
            this.$http.get('/api/ratings').then((res) => {
                if (res.body.errno === true) {
                    this.ratings = res.body.data;
                    this.$nextTick(() => {
                        this.ratingScroll = new BScroll(this.$refs.ratings, {
                            click: true
                        });
                    });
                }
            });
        },
        methods: {
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
            selectRating(type) {
                this.selectType = type;
                this.$nextTick(() => {
                    this.ratingScroll.refresh();
                });
            },
            toggleContent() {
                this.onlyContent = !this.onlyContent;
                this.$nextTick(() => {
                    this.ratingScroll.refresh();
                });
            }
        },
        components: {
            'v-star': star,
            'v-split': split,
            'v-ratingselect': ratingselect
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import "../../common/stylus/mixin.styl"

    .ratings
        position: absolute
        top: 3.48rem
        bottom: 0
        left: 0
        width: 100%
        overflow: hidden
        .overview
            display: flex
            padding: .36rem 0
            .overview-left
                flex: 0 0 2.74rem
                padding: .12rem 0
                width: 2.74rem
                border-right: 1px solid rgba(7, 17, 27, 0.1)
                text-align: center
                .score
                    margin-bottom: .12rem
                    line-height: .56rem
                    font-size: .48rem
                    color: rgb(255, 153, 0)
                .title
                    margin-bottom: .16rem
                    line-height: .24rem
                    font-size: .24rem
                    color: rgb(7, 17, 27)
                .rank
                    line-height: .2rem
                    font-size: .2rem
                    color: rgb(147, 153, 159)
            .overview-right
                flex: 1
                padding: .12rem 0 .12rem .48rem
                .score-wrapper
                    margin-bottom: .16rem
                    font-size: 0
                    .title
                        display: inline-block
                        line-height: .36rem
                        vertical-align: top
                        font-size: .24rem
                        color: rgb(7, 17, 27)
                    .star
                        display: inline-block
                        margin: 0 .24rem
                        vertical-align: top
                    .score
                        display: inline-block
                        line-height: .36rem
                        vertical-align: top
                        font-size: .24rem
                        color: rgb(255, 153, 0)
                .delivery-wrapper
                    font-size: 0
                    .title
                        line-height: .36rem
                        font-size: .24rem
                        color: rgb(7, 17, 27)
                    .delivery
                        margin-left: .24rem
                        font-size: .24rem
                        color: rgb(147, 153, 159)
        .rating-wrapper
            padding: 0 .36rem
            .rating-item
                display: flex
                padding: .36rem 0
                border-1px(rgba(7, 17, 27, 0.1))
                .avatar
                    flex: 0 0 .56rem
                    width: .56rem
                    margin-right: .24rem
                    img
                        width: .56rem
                        height: .56rem
                        border-radius: 50%
                .content
                    position: relative
                    flex: 1
                    .name
                        margin-bottom: .08rem
                        line-height: .24rem
                        font-size: .2rem
                        color: rgb(7, 17, 27)
                    .star-wrapper
                        margin-bottom: .12rem
                        font-size: 0
                        .star
                            display: inline-block
                            margin-right: .12rem
                            vertical-align: top
                        .delivery
                            display: inline-block
                            vertical-align: top
                            line-height: .24rem
                            font-size: .2rem
                            color: rgb(147, 153, 159)
                    .text
                        margin-bottom: .16rem
                        line-height: .36rem
                        color: rgb(7, 17, 27)
                        font-size: .24rem
                    .recommend
                        line-height: .32rem
                        font-size: 0
                        .icon-thumb_up, .item
                            display: inline-block
                            margin: 0 .16rem .08rem 0
                            font-size: .18rem
                        .icon-thumb_up
                            color: rgb(0, 160, 220)
                        .item
                            padding: 0 .12rem
                            border: 1px solid rgba(7, 17, 27, 0.1)
                            border-radius: 1px
                            color: rgb(147, 153, 159)
                            background: #fff
                    .time
                        position: absolute
                        top: 0
                        right: 0
                        line-height: .24rem
                        font-size: .2rem
                        color: rgb(147, 153, 159)
</style>

