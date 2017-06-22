<template>
    <div class="goods">
        <div class="menu-wrapper" ref="menuWrapper">
            <ul>
                <li v-for="(item,$index) in goods" class="menu-item" :class="{'active':currentIndex===$index}"
                    @click="selectMenu($index,$event)">
                    <span class="text border-1px">
                        <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>
                        {{item.name}}
                    </span>
                </li>
            </ul>
        </div>
        <div class="foods-wrapper" ref="foodsWrapper">
            <ul class="foods-all-hook">
                <li v-for="item in goods" class="food-list food-list-hook">
                    <h1 class="title">{{item.name}}</h1>
                    <ul>
                        <li @click="selectFood(food,$event)" v-for="food in item.foods" class="food-item">
                            <div class="icon">
                                <img width="57" height="57" :src="food.icon" alt="">
                            </div>
                            <div class="content">
                                <h2 class="name">{{food.name}}</h2>
                                <p class="desc" v-if="food.description">{{food.description}}</p>
                                <div class="extra">
                                    <span class="count">月售{{food.sellCount}}份</span>
                                    <span>好评率{{food.rating}}%</span>
                                </div>
                                <div class="price">
                                    <span class="now">￥{{food.price}}</span>
                                    <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                                </div>
                                <div class="cartcontrol-wrapper">
                                    <v-cartcontrol @add="addFood" :food="food"></v-cartcontrol>
                                </div>
                            </div>
                        </li>
                    </ul>
                </li>
            </ul>
        </div>
        <v-shopcart ref="shopcart" :selectFoods="selectFoods" :deliveryPrice="seller.deliveryPrice"
                    :minPrice="seller.minPrice"></v-shopcart>
        <v-food @add="addFood" :food="selectedFood" ref="food"></v-food>
    </div>
</template>

<script type="text/ecmascript-6">
    import BScroll from 'better-scroll';
    import shopcart from './../shopcart/shopcart';
    import cartcontrol from './../cartcontrol/cartcontrol';
    import food from './../food/food';
    export default {
        props: {
            seller: {
                type: Object
            }
        },
        data () {
            return {
                goods: [],
                listHeight: [],
                scrollY: 0,
                selectedFood: {}
            };
        },
        computed: {
            currentIndex () {
                let foodall = this.$refs.foodsWrapper.getElementsByClassName('foods-all-hook')[0];
                let menuWrapper = this.$refs.menuWrapper.getElementsByClassName('menu-item');
                let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
                for (let i = 0; i < this.listHeight.length; i++) {
                    if (this.scrollY >= (foodall.clientHeight - foodList[foodList.length - 1].clientHeight)) {
                        this.menuScroll.scrollToElement(foodList[foodList.length - 1], 300);
                    }
                    if (foodList[0].clientHeight >= this.scrollY) {
                        this.menuScroll.scrollToElement(menuWrapper[0], 300);
                    }
                    let height1 = this.listHeight[i];
                    let height2 = this.listHeight[i + 1];
                    if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
                        return i;
                    }
                }
            },
            selectFoods () {
                let foods = [];
                this.goods.forEach((good) => {
                    good.foods.forEach((food) => {
                        if (food.count) {
                            foods.push(food);
                        }
                    });
                });
                return foods;
            }
        },
        created () {
            this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
            this.$http.get('./api/goods').then((res) => {
                if (res.body.errno === true) {
                    this.goods = res.body.data;
                    this.$nextTick(() => {
                        this.initScroll();
                        this.calculateHeight();
                    });
                }
            });
        },
        methods: {
            initScroll () {
                this.menuScroll = new BScroll(this.$refs.menuWrapper, {
                    click: true
                });
                this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
                    probeType: 3,
                    click: true
                });
                this.foodsScroll.on('scroll', (pos) => {
                    this.scrollY = Math.abs(Math.round(pos.y));
                });
            },
            calculateHeight () {
                let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
                let height = 0;
                this.listHeight.push(height);
                for (let i = 0; i < foodList.length; i++) {
                    let item = foodList[i];
                    height += item.clientHeight;
                    this.listHeight.push(height);
                }
            },
            selectMenu (index, event) {
                if (!event._constructed) {
                    return;
                }
                let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
                let el = foodList[index];
                this.foodsScroll.scrollToElement(el, 300);
            },
            selectFood (food, event) {
                if (!event._constructed) {
                    return;
                }
                this.selectedFood = food;
                this.$refs.food.show();
            },
            addFood(target) {
                this._drop(target);
            },
            _drop(target) {
                // 体验优化,异步执行下落动画
                this.$nextTick(() => {
                    this.$refs.shopcart.drop(target);
                });
            }
        },
        components: {
            'v-shopcart': shopcart,
            'v-cartcontrol': cartcontrol,
            'v-food': food
        }
    };
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import '../../common/stylus/mixin'

    .goods
        display: flex
        position: absolute
        width: 100%
        top: 3.4rem
        bottom: .92rem
        overflow: hidden
        .menu-wrapper
            width: 1.6rem
            flex: 0 0 1.6rem
            background-color: #f3f5f7
            .menu-item
                display: table
                height: 1.08rem
                width: 1.6rem
                padding: 0 .24rem
                line-height: .28rem
                &.active
                    background: #fff
                    font-weight: 700
                    position: relative
                    margin-top: -1px
                    z-index: 10
                    .text
                        border-none()
                .icon
                    width: .24rem
                    height: .24rem
                    vertical-align: top
                    display: inline-block
                    margin-right: .04rem
                    &.decrease
                        bg-image('decrease_3')
                    &.discount
                        bg-image('discount_3')
                    &.guarantee
                        bg-image('guarantee_3')
                    &.invoice
                        bg-image('invoice_3')
                    &.special
                        bg-image('special_3')
                .text
                    display: table-cell
                    font-size: .24rem
                    width: 1.12rem
                    vertical-align: middle
                    border-1px(rgba(7, 17, 27, 0.1))
        .foods-wrapper
            flex: 1
            .title
                padding-left: .28rem
                height: .52rem
                line-height: .52rem
                border-left: 2px solid #d9dde1
                font-size: .24rem
                color: rgb(147, 153, 159)
                background: #f3f5f7
            .food-item
                display: flex
                margin: .36rem
                padding-bottom: .36rem
                border-1px(rgba(7, 17, 27, 0.1))
                &:last-child
                    border-none()
                    margin-bottom: 0
                .icon
                    flex: 0 0 1.04rem
                    margin-right: .2rem
                .content
                    flex: 1
                    .cartcontrol-wrapper
                        position: absolute
                        right: 0
                        bottom: .24rem
                    .name
                        font-size: .28rem
                        height .28rem
                        margin: .02rem 0 .16rem 0
                        line-height: .28rem
                        color: rgn(7, 17, 27)
                    .desc, .extra
                        line-height: .2rem
                        font-size: .2rem
                        color: rgb(147, 153, 159)
                    .desc
                        line-height: .24rem
                        margin-bottom: .16rem
                    .extra
                        .count
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
                        right: 0
                        bottom: 12px

</style>

