<template>
  <transition name="detail">
    <div class="food" v-show="showFlag" ref="foodView">
        <div class="food-wrapper">

            <div class="food-content">
                <div class="img-wrapper">
                    <img :src="food.picture" alt="" class="food-img">
                    <span class="close-bt icon-close" @click="closeView"></span>
                    <img src="./share.png" alt="" class="share-bt">
                    <img src="./more.png" alt="" class="more-bt">
                    
                </div>
                <div class="content-wrapper">
                    <h3 class="name">{{food.name}}</h3>
                    <p class="saled">{{food.month_saled_content}}</p>
                    <img :src="food.product_label_picture" class="product"
                    v-show="food.product_label_picture">
                    <p class="price">
                        <span class="text">¥{{food.min_price}}</span>
                        <span class="unit">/{{food.unit}}</span>
                    </p>
                    <div class="cartcontrol-wrapper">
                        <Cartcontrol :food="food"></Cartcontrol>
                    </div>
                    <div class="buy" v-show="!food.count " @click="addFirst">
                        选规格
                    </div>
                    
                </div>
            </div>
            
        </div>
        <Split></Split>

        <div class="rating-wrapper">
            <div class="rating-title">
                <div class="like-ratio">
                    <span class="title">{{food.rating.title}}</span>
                    <span class="ratio">({{food.rating.like_ratio_desc}}
                        <i>{{food.rating.like_ratio}}</i> )</span>
                </div>
                <div class="snd_title">
                    <span class="text">{{food.rating.snd_title}}</span>
                    <span class="icon icon-keyboard_arrow_right"></span>
                </div>

            </div>
        </div>
        
        
    </div>
  </transition>
</template>

<script>
import Cartcontrol from "../Cartcontrol/Cartcontrol.vue";
import Vue from 'vue';
import BScroll from "better-scroll";
import Split from "../Split/Split.vue";


export default {
    data(){
        return {
            showFlag: false
        }
    },
    props:{
        food:{
            type: Object
        }
    },
    methods:{
        showView(){
            this.showFlag=true;

            this.$nextTick(()=>{
                if(!this.scroll){
                    this.scroll = new BScroll(this.$refs.foodView,{
                        click: true
                    })
                }else{
                    this.scroll.refresh();
                }
            })
                
        },
        closeView(){
            this.showFlag=false;
        },
        addFirst(){
            Vue.set(this.food,'count',1);
        }
    },
    components:{
        Cartcontrol,
        BScroll,
        Split
    }
}
</script>

<style>

    @import url("./Food.css");
</style>