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

.seller{
    position: absolute;
    top:191px;
    left: 0;
    bottom: 0;
    width: 100%;
    
}

.seller .seller-wrapper{

}

.seller .seller-wrapper .seller-view{
    padding-left: 15px;
}

.seller .seller-wrapper .seller-view .address-wrapper{
    display: flex;
    padding: 16px 0;
    border-bottom: 1px solid #f4f4f4;
}

.seller .seller-wrapper .seller-view .address-wrapper .address-left{
    flex:1;
    background: url("./address.png") no-repeat left center;
    background-size: 14px 18px;
    padding-left: 26px;
    padding-right: 31px;
    font-size: 14px;
    line-height: 19px;
}

.seller .seller-wrapper .seller-view .address-wrapper .address-right{
    flex: 0 0 60px;
    background: url("./line.png") no-repeat left center;
    background-size: 1px 15px;

}

.seller .seller-wrapper .seller-view .address-wrapper .address-right .content{
    background: url("./phone.png") no-repeat center center;
    background-size: 18px 18px;
    height: 100%;
    width: 100%;
}

.seller .seller-wrapper .seller-view .pics-wrapper{
    padding: 10px 0;
    overflow: hidden;
    border-bottom: 1px solid #f4f4f4;
    white-space: nowrap;
}

.seller .seller-wrapper .seller-view .pics-wrapper .pics-list {

}

.seller .seller-wrapper .seller-view .pics-wrapper .pics-list .pics-item{
    display: inline-block;
    margin-right: 11px;
    width: 93px;
    height: 75px;
}

.seller .seller-wrapper .seller-view .pics-wrapper .pics-list .pics-item img{
    width: 100%;
    height: 100%;
    border-radius: 2px;
}

.seller .seller-wrapper .seller-view .safty-wrapper{
    padding: 15px 14px 15px 25px;
    background: url("./safety.png") no-repeat left center;
    background-size: 14px 16px;
    font-size: 14px;
}

.seller .seller-wrapper .seller-view .safty-wrapper span{
    float: right;
    font-size: 14px;
}

.seller .seller-wrapper .tip-wrapper {
    padding-left: 15px;
}

.seller .seller-wrapper .tip-wrapper .delivery-wrapper{
    background: url("./delivery.png") no-repeat left center;
    background-size: 14px 16px;
    padding: 15px 0 15px 25px;
    font-size: 14px;
    border-bottom: 1px solid #f4f4f4;
}

.seller .seller-wrapper .tip-wrapper .shipping-wrapper{
    background: url("./time.png") no-repeat left center;
    padding: 15px 17px 15px 25px;
    background-size: 15px 15px;
    font-size: 14px;
    line-height: 18px;
}

.seller .seller-wrapper .other-wrapper{
    padding-left: 15px;
}

.seller .seller-wrapper .other-wrapper .server-wrapper{
    background: url("./server.png") no-repeat left center;
    background-size: 15px 15px;
    padding: 15px 0 17px 25px;
    font-size: 14px;
    border-bottom: 1px solid #f4f4f4;
}

.seller .seller-wrapper .other-wrapper .server-wrapper .poi-server{
    margin-left: 17px;
    display: inline-block;
}

.seller .seller-wrapper .other-wrapper .server-wrapper .poi-server img{
    width: 15px;
    height: 15px;
    margin-right: 6px;
    vertical-align: middle;
}

.seller .seller-wrapper .other-wrapper .discounts-wrapper{
    padding: 17px 24px 19px 0;
}

.seller .seller-wrapper .other-wrapper .discounts-wrapper .discounts-item{
    display: flex;
}

.seller .seller-wrapper .other-wrapper .discounts-wrapper .discounts-item .icon{
    flex: 0 0 25px;
}

.seller .seller-wrapper .other-wrapper .discounts-wrapper .discounts-item .icon img{
    width: 15px;
    height: 15px;
}

.seller .seller-wrapper .other-wrapper .discounts-wrapper .discounts-item .text{
    flex: 1;
    font-size: 14px;
}

.seller .seller-wrapper .bottom{
    height: 28px;
}
</style>