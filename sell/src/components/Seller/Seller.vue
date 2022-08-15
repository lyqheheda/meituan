<template>
  <div class="seller">
    <div class="seller-wrapper">
      <Split></Split>
      <div class="seller-view">
        <div class="address-wrapper">
          <div class="address-left">
            {{seller.address}}
          </div>
          <div class="address-right">
            <div class="content"></div>
          </div>
        </div>
        <div class="pics-wrapper" v-if="seller.poi_env" ref="picsView">
          <ul class="pics-list" ref="picsList">
            <li class="pics-item" v-for="imgurl in seller.poi_env.thumbnails_url_list" ref="picsItem">
              <img :src="imgurl" alt="">
            </li>
          </ul>
        </div>
        <div class="safty-wrapper">
          查看食品安全档案
          <span class="icon-keyboard_arrow_right"></span>
        </div>
      </div>
      <Split></Split>
      <div class="tip-wrapper">
        <div class="delivery-wrapper">
          配送服务: {{seller.app_delivery_tip}}
        </div>
        <div class="shipping-wrapper">
          配送时间: {{seller.shipping_time}}
        </div>
      </div>
      <Split></Split>
      <div class="other-wrapper">
        <div class="server-wrapper">
          商家服务
          <div class="poi-server" v-for="item in seller.poi_service">
            <img :src="item.icon" alt="">
              {{item.content}}
          </div>
        </div>
        <div class="discounts-wrapper">
          <div class="discounts-item" v-for="item in seller.discounts2">
            <div class="icon">
              <img :src="item.icon_url" alt="">
            </div>
            <div class="text">{{item.info}}</div>
          </div>
        </div>
      </div>
      <Split class="bottom"></Split>

    </div>
  </div>
</template>

<script>
import Split from "../Split/Split.vue";
import BScroll from "better-scroll";


export default {
    data(){
      return {
        seller:{}
      }
    },
    created(){
      this.$axios
      .get("/api/seller")
      .then((response) => {
        var dataSource = response.data;
        if (dataSource.code == 0) {
          this.seller = dataSource.data;

          this.$nextTick(()=>{
            if(this.seller.poi_env.thumbnails_url_list){
              let imgW = this.$refs.picsItem[0].clientWidth;
              let marginW = 11;
              let width = (imgW+marginW) * this.seller.poi_env.thumbnails_url_list.length;
              this.$refs.picsList.style.width=width +"px";

              this.scroll=new BScroll(this.$refs.picsView,{
                scrollX:true
              })
              
            }
          })

          
        }
      })
      .catch(function (error) {
        console.log(error);
      });
    },
    components:{
      Split,
      BScroll
    }
}
</script>

<style>

    @import url("./Seller.css");
</style>