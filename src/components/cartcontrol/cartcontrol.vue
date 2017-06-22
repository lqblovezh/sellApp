<template>
    <div class="cartcontrol">
        <transition name="move">
            <div class="cart-decrease" v-show="food.count>0" @click.stop.prevent="increaseDecrease(-1,$event)">
                <span class="inner icon-remove_circle_outline"></span>
            </div>
        </transition>
        <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
        <div class="cart-add icon-add_circle" @click.stop.prevent="increaseDecrease(1,$event)"></div>
    </div>
</template>

<script type="text/ecmascript-6">
    import Vue from 'vue';
    export default {
        props: {
            food: {
                type: Object
            }
        },
        methods: {
            increaseDecrease (type, event) {
                if (!event._constructed) {
                    return;
                }
                if (type === 1) {
                    if (this.food.count) {
                        this.food.count++;
                    } else {
                        Vue.set(this.food, 'count', 1);
                    }
                    this.$emit('add', event.target);
                } else {
                    if (this.food.count) {
                        this.food.count--;
                    }
                }
            }
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    .cartcontrol
        font-size: 0
        .cart-decrease
            display: inline-block
            padding: .1rem
            transition: all 0.4s linear
            .inner
                display: inline-block
                font-size: .48rem
                line-height: .48rem
                color: rgb(0, 160, 220)
            &.move-enter-active, &.move-leave-active
                opacity: 1
                transform: translate3d(0, 0, 0)
                .inner
                    transition: all 0.4s linear
                    transform: rotate(0)
            &.move-enter, &.move-leave-active
                opacity: 0
                transform: translate3d(.48rem, 0, 0)
                .inner
                    transform: rotate(180deg)
        .cart-count
            display: inline-block
            font-size: .2rem
            vertical-align: top
            padding-top: .12rem
            min-width: .24rem
            text-align: center
            line-height: .48rem
            color: rgb(147, 153, 159)
        .cart-add
            display: inline-block
            padding: .1rem
            font-size: .48rem
            line-height: .48rem
            color: rgb(0, 160, 220)
</style>
