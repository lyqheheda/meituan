<template>
  <div class="goods">
    <!-- 分类 -->
    <div class="menu-wrapper" ref="menuScroll">
      <ul>
        <!-- 专场 -->
        <li class="menu-item" :class="{ current: currentIndex === 0 }"
        @click="selectMenu(0)">
          <p class="text">
            <img
              :src="container.tag_icon"
              v-if="container.tag_icon"
              class="icon"
            />
            {{ container.tag_name }}
          </p>
        </li>

        <li
          class="menu-item"
          v-for="(item, index) in goods"
          :class="{ current: currentIndex === index+1 }"
          @click="selectMenu(index+1)"
        >
          <p class="text">
            <img :src="item.icon" v-if="item.icon" class="icon" />
            {{ item.name }}
          </p>

          <i class="num" v-show="calculateCount(item.spus)">
            {{calculateCount(item.spus)}}
          </i>
          
        </li>
      </ul>
    </div>

    <!-- 商品列表 -->
    <div class="foods-wrapper" ref="foodScroll">
      <ul>
        <!-- 专场 -->
        <li class="container-list food-list-hook">
          <div v-for="item in container.operation_source_list">
            <img :src="item.pic_url" alt="" />
          </div>
        </li>

        <!-- 具体分类 -->
        <li v-for="item in goods" class="food-list food-list-hook">
          <h3 class="title">{{ item.name }}</h3>
          <!-- 具体商品列表 -->
          <ul>
            <li v-for="food in item.spus" class="food-item" @click="showDetail(food)">
              <div class="icon" :style="head_bg(food.picture)"></div>
              <div class="content">
                <h3 class="name">{{ food.name }}</h3>
                <p class="desc" v-if="food.description">
                  {{ food.description }}
                </p>
                <div class="extra">
                  <span class="saled">{{ food.month_saled_content }}</span>
                  <span class="praise">{{ food.praise_content }}</span>
                </div>
                <img :src="food.product_label_picture" class="product" />
                <p class="price">
                  <span class="text">¥{{ food.min_price }}</span>
                  <span class="unit">/{{ food.unit }}</span>
                </p>
              </div>
              <div class="cartcontrol-wrapper">
                <Cartcontrol :food="food" />
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>

    <!-- 购物车 -->
    <Shopcart :poiInfo="poiInfo" :selectFoods="selectFoods"
    />

    <!-- 商品详情 -->
    <!-- 注意：当使用该组件时购物车列表视图会出现问题 -->
    <Food :food="selectedFood" ref="foodView"></Food>

    
  </div>
</template>

<script>
import BScroll from "better-scroll";
import Shopcart from "../Shopcart/Shopcart.vue";
import Cartcontrol from "../Cartcontrol/Cartcontrol.vue";
import Food from "../Food/Food.vue";

export default {
  data() {
    return {
      container: {},
      goods: [],
      poiInfo: {},
      listHeight: [],
      scrollY: 0,
      menuScroll: {},
      foodScroll: {},
      selectedFood:{},
    };
  },
  created() {
    // 发起异步请求，获取数据
    this.$axios
      .get('../data/goods.json')
      .then((response) => {
        var dataSource = response.data;
        if (dataSource.code == 0) {
          this.container = dataSource.data.container_operation_source;
          this.goods = dataSource.data.food_spu_tags;
          this.poiInfo = dataSource.data.poi_info;

          // 调用滚动的初始化方法
          // 开始时，DOM元素没有渲染，即高度是问题
          // 在获取到数据后，并DOM已经被渲染，表示列表高度是没问题的
          // this.initScroll();
          this.$nextTick(() => {
            this.initScroll();
            this.calculateHeight();
          });
        }
      })
      .catch(function (error) {
        console.log(error);
      });
  },
  methods: {
    head_bg(imgName) {
      return `background-image: url(${imgName})`;
    },
    initScroll() {
      this.menuScroll = new BScroll(this.$refs.menuScroll, {
        click: true
      });
      this.foodScroll = new BScroll(this.$refs.foodScroll, {
        probeType: 3,
        click:true
      });
      this.foodScroll.on("scroll", (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    calculateHeight() {
      // 获取元素
      let foodList = this.$refs.foodScroll.getElementsByClassName("food-list-hook");
      // 添加到数组区间
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
      // console.log(this.listHeight);
    },
    selectMenu(index){
      let foodList = this.$refs.foodScroll.getElementsByClassName("food-list-hook");

      let el = foodList[index];

      //滚动到对应元素
      this.foodScroll.scrollToElement(el,250);
    },
    calculateCount(spus){
      let count=0;
      spus.forEach(food=>{
        if(food.count>0){
          count+=food.count
        }
      })
      return count;
    },
    showDetail(food){
      this.selectedFood=food;
      // 调用子组件的方法，显示详情页
      this.$refs.foodView.showView();

    }
  },
  computed: {
    currentIndex() {
      for (let i = 0; i < this.listHeight.length; i++) {
        // 获取商品区间的范围
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];

        // 是否在上述区间中
        if (!height2  || (this.scrollY >= height1 && this.scrollY <= height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods() {
      let foods=[];
      this.goods.forEach(good=>{
        good.spus.forEach(food=>{
          if(food.count>0){
            foods.push(food)
          }
        })
      })
      return foods;
    }
  },
  components: {
    BScroll,
    Shopcart,
    Cartcontrol,
    Food
  }
};
</script>

<style>
.goods {
    display: flex;

    /* 确定高度 */
    position: absolute;
    top: 190px;
    bottom: 51px;

    overflow: hidden;
    width: 100%;
}

.goods .menu-wrapper {
    flex: 0 0 85px;
    background-color: #f4f4f4;
}

.goods .menu-wrapper .menu-item.current {
    background-color: white;
    font-weight: bold;
    margin-top: -1px;
}

.goods .menu-wrapper .menu-item {
    padding: 16px 23px 15px 10px;
    border-bottom: 1px solid #e4e4e4;
    position: relative;
}

.goods .menu-wrapper .menu-item .text {
    font-size: 13px;
    color: #333333;
    line-height: 17px;
    vertical-align: middle;

    -webkit-line-clamp: 2;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.goods .menu-wrapper .menu-item .text .icon {
    width: 15px;
    height: 15px;
    vertical-align: middle;
}

.goods .menu-wrapper .menu-item .num {
    position: absolute;
    right: 5px;
    top: 5px;
    width: 13px;
    height: 13px;
    border-radius: 50%;
    color: white;
    background: red;
    text-align: center;
    line-height: 13px;
    font-size: 7px;

}

.goods .foods-wrapper {
    flex: 1;

}

.goods .foods-wrapper .container-list {
    padding: 11px 11px 0 11px;
    border-bottom: 1px solid #e4e4e4;
}

.goods .foods-wrapper .container-list img {
    width: 100%;
    margin-bottom: 11px;
    border-radius: 5px;
}

.goods .foods-wrapper .food-list {
    padding: 11px;
}

.goods .foods-wrapper .food-list .title {
    font-size: 13px;
    height: 13px;
    background-image: url("./btn_yellow_highlighted@2x.png");
    background-repeat: no-repeat;
    background-size: 2px 10px;
    background-position: left center;
    padding-left: 7px;
    margin-bottom: 12px;
}

.goods .foods-wrapper .food-list .food-item {
    display: flex;
    margin-bottom: 25px;
    position: relative;
}

.goods .foods-wrapper .food-list .food-item .icon {
    flex: 0 0 63px;
    background-size: 120% 100%;
    background-position: center;
    background-repeat: no-repeat;
    margin-right: 11px;
    height: 75px;
}

.goods .foods-wrapper .food-list .food-item .content{
    flex: 1;
}

.goods .foods-wrapper .food-list .food-item .content .name {
    color: #333333;
    font-size: 16px;
    line-height: 21px;
    margin-bottom: 10px;
    padding-right: 27px;
    font-weight: bold;
}

.goods .foods-wrapper .food-list .food-item .content .desc {
    font-size: 10px;
    line-height: 19px;
    color: #bfbfbf;
    margin-bottom: 8px;

    -webkit-line-clamp: 1;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

.goods .foods-wrapper .food-list .food-item .content .extra {
    font-size: 10px;
    color: #bfbfbf;
    margin-bottom: 7px;
}

.goods .foods-wrapper .food-list .food-item .content .extra .saled {
    margin-right: 14px;
}

.goods .foods-wrapper .food-list .food-item .content img {
    height: 15px;
    margin-bottom: 6px;
}

.goods .foods-wrapper .food-list .food-item .content .price {
    font-size: 0;
}

.goods .foods-wrapper .food-list .food-item .content .price .text {
    font-size: 14px;
    color: #fb4e44;
}

.goods .foods-wrapper .food-list .food-item .content .price .unit {
    font-size: 12px;
    color: #bfbfbf;
}

.goods .foods-wrapper .food-list .food-item .cartcontrol-wrapper{
    position: absolute;
    right: 0;
    bottom: 0;
}

</style>