<template>
  <div class="shopcart" :class="{'highlight':totalCount>0}">
    <div class="content-left">
        <div class="logo-wrapper" :class="{'highlight':totalCount>0}">
            <span class="icon-shopping_cart logo" :class="{'highlight':totalCount>0}"></span>
            <i class="num" v-show="totalCount">{{totalCount}}</i>
        </div>
        <div class="desc-wrapper">
            <p class="total-price" v-show="totalPrice">¥{{totalPrice}}</p>
            <p class="tip" :class="{'highlight':totalCount>0}">{{shipping_fee_tip}}</p>
        </div>
    </div>

    <div class="content-right" :class="{'highlight':totalCount>0}">
        {{payStr}}
    </div>

  </div>
</template>

<script>
export default {
    props: {
        min_price_tip: {
            type: String,
            default: ''
        },
        shipping_fee_tip: {
            type: String,
            default: ''
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
            else return this.min_price_tip;
        }
    }
}
</script>

<style>
    @import url("./Shopcart.css");
</style>