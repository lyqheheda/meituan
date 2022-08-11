<template>
  <div class="shopcart" :class="{'highlight':totalCount>0}">
    <div class="content-left">
        <div class="logo-wrapper" :class="{'highlight':totalCount>0}" @click="toggleList">
            <span class="icon-shopping_cart logo" :class="{'highlight':totalCount>0}"></span>
            <i class="num" v-show="totalCount">{{totalCount}}</i>
        </div>
        <div class="desc-wrapper">
            <p class="total-price" v-show="totalPrice">¥{{totalPrice}}</p>
            <p class="tip" :class="{'highlight':totalCount>0}">{{poiInfo.shipping_fee_tip}}</p>
        </div>
    </div>

    <div class="content-right" :class="{'highlight':totalCount>0}">
        {{payStr}}
    </div>

    <div class="shopcart-list" v-show="listShow" :class="{'show':listShow}">
        <div class="list-top" v-if="poiInfo.discounts2">
            {{poiInfo.discounts2[0].info}}
        </div>
        <div class="list-header">
            <h3 class="title">1号口袋</h3>
            <div class="empty">
                <img src="./ash_bin.png">
                <span>清空购物车</span>
            </div>
        </div>
        <div class="list-content">
            <ul>
                <li class="food" v-for="food in selectFoods">
                    <div class="desc-wrapper">
                        <div class="desc-left">
                            <p class="name">{{food.name}}</p>
                            <p class="unit" v-show="!food.description">{{food.unit}}</p>
                            <p class="description" v-show="food.description">{{food.description}}</p>
                        </div>
                        <div class="desc-right">
                            <span class="price">¥{{food.min_price}}</span>
                        </div>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <Cartcontrol :food="food"/>
                    </div>
                </li>
            </ul>
        </div>
        <div class="list-bottom"></div>
    </div>

  </div>
</template>

<script>
import Cartcontrol from '../Cartcontrol/Cartcontrol.vue';

export default {
    data(){
        return {
            fold:true
        }
    },
    props: {
        poiInfo:{
            type: Object,
            default:{}
        },
        selectFoods: {
            type: Array,
            default(){
                return [
                    // {
                    //     min_price: 10,
                    //     count: 3
                    // },
                    // {
                    //     min_price: 7,
                    //     count: 5
                    // }
                ];
            }
        }
    },
    computed:{
        // 总个数
        totalCount(){
            let num = 0;
            this.selectFoods.forEach((food)=>{
                num+=food.count;
            });
            return num;
        },
        totalPrice(){
            let total = 0;
            this.selectFoods.forEach((food)=>{
                total+=food.count*food.min_price;
            });
            return total;
        },
        // 结算按钮显示
        payStr(){
            if(this.totalCount>0) return "去结算";
            else return this.poiInfo.min_price_tip;
        },
        listShow(){
            if(!this.totalCount){
                this.fold=true;
                return false;
            }
            let show = !this.fold;
            return show;
        }
    },
    methods:{
        toggleList(){
            if(!this.totalCount){
                return;
            }
            this.fold = !this.fold;
        }
    },
    components:{
        Cartcontrol
    }
}
</script>

<style>
    @import url("./Shopcart.css");
</style>