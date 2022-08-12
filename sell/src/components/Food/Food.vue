<template>
  <div class="food" v-show="showFlag">

  </div>
</template>

<script>
export default {
    data(){
        return {
            showFlag: true
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
        }
    }
}
</script>

<style>

    @import url("./Food.css");
</style>