<template>
  <div id="app">
    <!-- 头部 -->
    <Myheader :poiInfo="poiInfo"/>

    <!-- 导航 -->
    <Mynav :commentNum="commentNum" />

    <!-- 主体内容 -->
    <keep-alive>
    <router-view></router-view>
    </keep-alive>
  </div>
</template>

<script>
import Myheader from "./components/Header/Header.vue";
import Mynav from "./components/Nav/Nav.vue";

export default {
  name: "App",
  components: {
    Myheader,
    Mynav,
  },
  data() {
    return {
      // header组件需要的信息
      poiInfo: {},
      commentNum: 0 
    };
  },
  created() {
    // 发起异步请求，获取数据

    var that = this

    this.$axios
      .get("../static/goods.json")
      .then(function (response) {
        var dataSource=response.data;
        if(dataSource.code==0){
          that.poiInfo= dataSource.data.poi_info;
          // console.log(that.poiInfo);

        }
      })
      .catch(function (error) {
        console.log(error);
      });

      this.$axios
      .get("../static/ratings.json")
      .then(function (response) {
        var dataSource=response.data;
        if(dataSource.code==0){
          that.commentNum= dataSource.data.comment_num;

        }
      })
      .catch(function (error) {
        console.log(error);
      });

      
  },
};
</script>

<style>
</style>
