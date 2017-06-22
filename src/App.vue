<template>
    <div>
        <v-header :seller="seller"></v-header>
        <div class=tab>
            <div class="tab-item">
                <router-link to="/goods">商品</router-link>
            </div>
            <div class="tab-item">
                <router-link to="/ratings">评论</router-link>
            </div>
            <div class="tab-item">
                <router-link to="/seller">商家</router-link>
            </div>
        </div>
        <keep-alive>
            <router-view :seller="seller"></router-view>
        </keep-alive>
    </div>
</template>

<script>
    import Vue from 'vue';
    import {urlParse} from './common/js/util';
    import header from './components/header/header';

    export default {
        data () {
            return {
                seller: {
                    id: (() => {
                        let queryParam = urlParse();
                        return queryParam.id;
                    })()
                }
            };
        },
        created() {
            this.$http.get('/api/seller?id=' + this.seller.id).then((response) => {
                response = response.body;
                if (response.errno === true) {
                    this.seller = Object.assign({}, this.seller, response.data);
                    console.log(this.seller.id);
                }
            });
        },
        components: {
            'v-header': header
        }
    };
    Vue.filter('formatDate', function (time) {
        let date = new Date(time);
        var fmt = 'yyyy-MM-dd hh:mm';
        if (/(y+)/.test(fmt)) {
            fmt = fmt.replace(RegExp.$1, (date.getFullYear() + '').substr(4 - RegExp.$1.length));
        }
        let o = {
            'M+': date.getMonth() + 1,
            'd+': date.getDate(),
            'h+': date.getHours(),
            'm+': date.getMinutes(),
            's+': date.getSeconds()
        };
        for (let k in o) {
            if (new RegExp(`(${k})`).test(fmt)) {
                let str = o[k] + '';
                fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? str : ('00' + str).substr(str.length));
            }
        }
        return fmt;
    });
</script>

<style lang="stylus" rel="stylesheet/stylus">
    @import './common/stylus/mixin.styl'

    .tab
        display: flex
        width: 100%
        height: .8rem
        line-height: .8rem
        border-1px(rgba(7, 17, 27, 0.1))
        .tab-item
            flex: 1
            text-align: center
            a
                color: rgb(77, 85, 93)
                font-size: .28rem
                &.active
                    color: rgb(240, 20, 20)
</style>
