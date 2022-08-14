<template>
  <div class="nav">
    
    <router-link class="nav-item" to="/goods">点菜
    
    <span class="line"></span>
    </router-link>
    <router-link class="nav-item" to="/ratings">评价({{commentNum}})
    
    <span class="line"></span>
    </router-link>
    <router-link class="nav-item" to="/seller">商家
    
    <span class="line"></span>
    </router-link>



    
  </div>
  
</template>

<script>
export default {
    props: {
      commentNum:{
        type: Number,
        default: 0
      }
    }
}
</script>

<style>
.nav {
  display: flex;
  width: 100%;
  height: 40px;
  line-height: 40px;
  border-bottom: 1px solid #e4e4e4;
}

.nav .nav-item {
  flex: 1;
  text-align: center;
  font-size: 13px;
  text-decoration: none;
  color: #666666;
}

/* 选中之后的样式 */
.nav .active {
    color: #ffbb22;
    position: relative;
}

.nav .active .line {
  width: 21px;
  height: 2px;
  display: inline-block;
  background-color: #ffbb22;
  position: absolute;
  left: 50%;
  bottom: 0;
  margin-left: -10px;
}



</style>