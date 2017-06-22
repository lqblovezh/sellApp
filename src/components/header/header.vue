<template>
    <div class="header">
        <div class="content-wrapper">
            <div class="avatar">
                <img src="" :src="seller.avatar" alt="">
            </div>
            <div class="content">
                <div class="title">
                    <span class="brand"></span>
                    <span class="name">{{seller.name}}</span>
                </div>
                <div class="description">{{seller.description}}/{{seller.deliveryTime}}分钟送达</div>
                <div v-if="seller.supports" class="support">
                    <span class="icon" :class="classMap[seller.supports[0].type]"></span>
                    <span class="text">{{seller.supports[0].description}}</span>
                </div>
                <div v-if="seller.supports" class="support-count" @click="detailShow=true">
                    <span class="count">{{seller.supports.length}}个</span>
                    <i class="icon-keyboard_arrow_right"></i>
                </div>
            </div>
        </div>
        <div class="bulletin-wrapper" @click="detailShow=true">
            <span class="bulletin-tiller"></span><span class="bulletin-text">{{seller.bulletin}}</span>
            <i class="icon-keyboard_arrow_right"></i>
        </div>
        <div class="background">
            <img :src="seller.avatar" width="100%" alt="">
        </div>
        <transition name="fade">
            <div v-show="detailShow" class="detail">
                <div class="detail-wrapper clearfix">
                    <div class="detail-main">
                        <h1 class="name">{{seller.name}}</h1>
                        <div class="star-wrapper">
                            <v-star :size="48" :score="seller.score"></v-star>
                        </div>
                        <div class="title">
                            <div class="line"></div>
                            <div class="text">优惠信息</div>
                            <div class="line"></div>
                        </div>
                        <ul v-if="seller.supports" class="supports">
                            <li class="support-item" v-for="(item,$index) in seller.supports">
                                <span class="icon" :class="classMap[seller.supports[$index].type]"></span>
                                <span class="text">{{seller.supports[$index].description}}</span>
                            </li>
                        </ul>
                        <div class="title">
                            <div class="line"></div>
                            <div class="text">商家公告</div>
                            <div class="line"></div>
                        </div>
                        <div class="bulletin">
                            <p class="content">{{seller.bulletin}}</p>
                        </div>
                    </div>
                </div>
                <div class="detail-close" @click="detailShow=false">
                    <i class="icon-close"></i>
                </div>
            </div>
        </transition>
    </div>
</template>

<script type="text/ecmascript-6">
    import star from '../star/star';

    export default {
        props: {
            seller: {
                type: Object
            }
        },
        data () {
            return {
                detailShow: false
            };
        },
        created () {
            this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
        },
        components: {
            'v-star': star
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin'

    .header
        color: #fff
        overflow hidden
        background: rgba(7, 17, 27, .5)
        position: relative
        .content-wrapper
            padding: .48rem .24rem .36rem .48rem
            position: relative
            font-size: 0
            .avatar
                display: inline-block
                vertical-align: top
                img
                    width: 1.28rem
                    height: 1.28rem
                    border-radius: .04rem
            .content
                display: inline-block
                margin-left: .32rem
                .title
                    margin: .04rem .12rem .16rem 0
                    font-weight: bold
                    .brand
                        bg-image('brand')
                        display: inline-block
                        width: .6rem
                        height: .36rem
                        margin-right: .12rem
                        vertical-align: top
                    .name
                        font-size: .32rem
                        line-height: .36rem
                .description
                    font-size: .24rem
                    line-height: .24rem
                    margin-bottom: .2rem
                .support
                    font-size: .2rem
                    line-height: .24rem
                    .icon
                        display: inline-block
                        width: .24rem
                        height: .24rem
                        margin-right: 0.08rem
                        vertical-align: top
                        &.decrease
                            bg-image('decrease_1')
                        &.discount
                            bg-image('discount_1')
                        &.guarantee
                            bg-image('guarantee_1')
                        &.invoice
                            bg-image('invoice_1')
                        &.special
                            bg-image('special_1')
                .support-count
                    position: absolute
                    right: .24rem
                    bottom: .36rem
                    padding: 0 .16rem
                    height: .48rem
                    line-height: .48rem
                    border-radius: .28rem
                    font-size: .2rem
                    background: rgba(0, 0, 0, .2)
                    text-align: center
                    .count
                        vertical-align: top
                    i
                        margin-left: .04rem
                        line-height: .48rem
                        font-size: .2rem
        .bulletin-wrapper
            position: relative
            height: .48rem
            line-height: .48rem
            padding: 0 .44rem 0 .24rem
            overflow hidden
            white-space: nowrap
            text-overflow: ellipsis
            background: rgba(7, 17, 27, .2)
            .bulletin-tiller
                vertical-align: top
                width: .44rem
                height: .24rem
                margin-top: .1rem
                display: inline-block
                bg-image('bulletin')
            .bulletin-text
                vertical-align: top
                font-size: .2rem
                margin: 0 .08rem
            i
                position: absolute
                right: .24rem
                top: .12rem

        .background
            position: absolute
            top: 0
            left: 0
            z-index: -1
            width: 100%
            height: 100%
            filter: blur(10px)
        .detail
            position: fixed
            z-index: 100
            top: 0
            left: 0
            width: 100%
            height: 100%
            overflow: auto
            -webkit-backdrop-filter: blur(10px)
            background: rgba(7, 17, 27, .8)
            transition: all 0.5s ease
            &.fade-enter-active, &.fade-leave-active
                opacity: 1
                background: rgba(7, 17, 27, 0.8)
            &.fade-enter, &.fade-leave-active
                opacity: 0
                background: rgba(7, 17, 27, 0)
            .detail-wrapper
                min-height: 100%
                width: 100%
                .detail-main
                    padding-bottom: 1.28rem
                    margin-top: 1.28rem
                    .name
                        line-height: .32rem
                        text-align: center
                        font-size: .32rem
                        font-weight: 700
                    .star-wrapper
                        margin-top: .36rem
                        padding: 2px 0
                        text-align: center
                .title
                    width 80%
                    display: flex
                    margin: .56rem auto .48rem auto
                    .line
                        flex: 1
                        position: relative
                        top: -.12rem
                        border-bottom: 1px solid rgba(255, 255, 255, 0.2)
                    .text
                        text-align: center
                        padding: 0 .24rem
                        font-weight: 700
                        font-size: .28rem
                .supports
                    width: 80%
                    margin: 0 auto
                    .support-item
                        padding: 0 .24rem
                        margin-bottom: .24rem
                        font-size: 0
                        &:last-child
                            margin-bottom: 0
                        .icon
                            width: .24rem
                            height: .24rem
                            vertical-align: top
                            display: inline-block
                            margin-right: .08rem
                            &.decrease
                                bg-image('decrease_2')
                            &.discount
                                bg-image('discount_2')
                            &.guarantee
                                bg-image('guarantee_2')
                            &.invoice
                                bg-image('invoice_2')
                            &.special
                                bg-image('special_2')
                        .text
                            font-size: .24rem
                            font-weight: 700
                            line-height: .24rem
                .bulletin
                    width: 80%
                    padding: .48rem .24rem
                    margin: 0 auto
                    .content
                        font-size: .24rem
                        line-height: .48rem
            .detail-close
                position: relative
                width: .64rem
                height: .64rem
                clear: both
                font-size: .64rem
                margin: -1.28rem auto 0 auto
</style>

