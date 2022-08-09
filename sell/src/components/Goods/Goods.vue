<template>
  <div class="goods">

    <!-- 分类 -->
    <div class="menu-wrapper" ref="menuScroll">
      <ul>
        <!-- 专场 -->
        <li class="menu-item" >
          <p class="text">
            <img :src="container.tag_icon" v-if="container.tag_icon" class="icon">
            {{container.tag_name}}
          </p>
        </li>
        
        <li class="menu-item" v-for="item in goods">
          <p class="text">
            <img :src="item.icon" v-if="item.icon" class="icon">
            {{item.name}}
          </p>
        </li>
      </ul>
    </div>

    <!-- 商品列表 -->
    <div class="foods-wrapper" ref="foodScroll">
      <ul>
        <!-- 专场 -->
        <li class="container-list food-list-hook">
          <div v-for="item in container.operation_source_list">
            <img :src="item.pic_url" alt="">
          </div>
        </li>
          
          
        <!-- 具体分类 -->
        <li v-for="item in goods" class="food-list food-list-hook">
          <h3 class="title" >{{item.name}}</h3>
          <!-- 具体商品列表 -->
          <ul>
            <li v-for="food in item.spus" class="food-item">
              <div class="icon" :style="head_bg(food.picture)"></div>
              <div class="content">
                <h3 class="name">{{food.name}}</h3>
                <p class="desc" v-if="food.description" >{{food.description}}</p>
                <div class="extra">
                  <span class="saled">{{food.month_saled_content}}</span>
                  <span class="praise">{{food.praise_content}}</span>
                </div>
                <img :src="food.product_label_picture" class="product">
                <p class="price">
                  <span class="text">¥{{food.min_price}}</span>
                  <span class="unit">/{{food.unit}}</span>
                </p>
                
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    
  </div>
</template>

<script>

import BScroll from 'better-scroll'


export default {
  data(){
    return {
      container:{},
      goods:[],
      listHeight:[],
      scrollY: 0,
      menuScroll: {},
      foodScroll: {}

    }
  },
  created() {
    // 发起异步请求，获取数据
    this.$axios
      .get("/api/goods")
      .then( (response) =>{
        var dataSource=response.data;
        if(dataSource.code==0){
          this.container = dataSource.data.container_operation_source;
          this.goods = dataSource.data.food_spu_tags;

          // 调用滚动的初始化方法
          // this.initScroll();
            this.$nextTick(()=>{
            this.initScroll();
          })
            this.calculateHeight();
          
        }
      })
      .catch(function (error) {
        console.log(error);
      });
  },
  methods:{
    head_bg(imgName){
      return `background-image: url(${imgName})`;
    },
    initScroll(){
      this.menuScroll = new BScroll(this.$refs.menuScroll);
      this.foodScroll = new BScroll(this.$refs.foodScroll,{
        probeType: 3
      });

      this.foodScroll.on("scroll",(pos)=>{
        this.scrollY = Math.abs(Math.round(pos.y));
      })

    },
    calculateHeight(){
      // 获取元素
      let foodList = this.$refs.foodScroll.getElementsByClassName('food-list-hook');

      // 添加到数组区间
      let height = 0;
      this.listHeight.push(height);
      for(let i=0;i<foodList.length;i++){
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }

      
    }
  },
  computed: {
    currentIndex(){
      for(let i=0;i<this.listHeight.length;i++){
        // 获取商品区间的范围
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i+1];

        // 是否在上述区间中
        if(this.scrollY>=height1 && this.scrollY<=height2){
          return i;
        }
      }
      return 0;
    }
  }
}
</script>

<style>
    @import url("./Goods.css");
</style>