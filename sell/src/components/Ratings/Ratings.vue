<template>
  <div class="ratings" ref="ratingView">
    <div class="ratings-wrapper">
      <div class="overview">
        <div class="overview-left">
          <div class="comment-score">
            <p class="score">{{ratings.comment_score}}</p>
            <p class="text">商家评分</p>
          </div>
          <div class="other-score">
            <div class="quality-score item">
              <span class="text">口味</span>
              <Star :score="ratings.quality_score" class="star"></Star>
              <span class="score">{{ratings.quality_score}}</span>
            </div>
            <div class="pack-score item">
              <span class="text">包装</span>
              <Star :score="ratings.pack_score" class="star"></Star>
              <span class="score">{{ratings.pack_score}}</span>
            </div>
          </div>
        </div>
        <div class="overview-right">
          <div class="delivery-score">
            <p class="score">{{ratings.delivery_score}}</p>
            <p class="text">配送评分</p>
          </div>
        </div>
      </div>
      <Split></Split>
      <div class="content">
        <div class="rating-select" v-if="ratings.tab">
          <span class="item" @click="selectTypeFn(2)" :class="{'active':selectType===2}">
            {{ratings.tab[0].comment_score_title}}
          </span>
          <span class="item" @click="selectTypeFn(1)" :class="{'active':selectType===1}">
            {{ratings.tab[1].comment_score_title}}
          </span>
          <span class="item" @click="selectTypeFn(0)" :class="{'active':selectType===0}">
            <img src="./icon_sub_tab_dp_normal@2x.png" v-show="selectType!==0" >
            <img src="./icon_sub_tab_dp_highlighted@2x.png" v-show="selectType==0">
            {{ratings.tab[2].comment_score_title}}
          </span>
        </div>
        <div class="labels-view">
          <span v-for="item in ratings.labels" class="item" :class="{'highlight':item.label_star>0}">
            {{item.content}}{{item.label_count}}
          </span>
        </div>
        <ul class="rating-list">
          <li v-for="comment in selectComments" class="comment-item">
                <div class="comment-header">
                    <img :src="comment.user_pic_url" v-if="comment.user_pic_url">
                    <img src="./anonymity.png" v-if="!comment.user_pic_url">
                </div>
                <div class="comment-main">
                    <div class="user">{{comment.user_name}}</div>
                    <div class="time">{{formatDate(comment.comment_time)}}</div>
                    <div class="star-wrapper">
                      <span class="text">评分</span>
                      <Star :score="comment.order_comment_score" class="star"></Star>
                    </div>
                    <div class="c_content" v-html="commentStr(comment.comment)" >
                      <div class="img-wrapper">
                      <img v-for="item in comment.comment_pics" :src="item.thumbnail_url" alt="">
                      </div>
                    </div>
                    
                </div>
            </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import Star from '../Star/Star.vue';
import Split from "../Split/Split.vue";
import BScroll from "better-scroll";


const ALL =2; //全部
const PICTURE =1; //有图
const COMMENT =0; //点评

export default {
    data(){
      return {
        ratings:{},
        selectType: ALL
      }
    },
    methods:{
      selectTypeFn(type){
        this.selectType=type;
        this.$nextTick(()=>{
          this.scroll.refresh()

        })
      },
      formatDate(time) {
				let date = new Date(time * 1000);

				// 时间格式
				let fmt = 'yyyy.MM.dd';

				if(/(y+)/.test(fmt)) { // 年
					let year = date.getFullYear().toString();
					fmt = fmt.replace(RegExp.$1, year);
				}
				if(/(M+)/.test(fmt)) { // 月
					let mouth = date.getMonth() + 1;
					if(mouth < 10) {
						mouth = '0' + mouth;
					}
					fmt = fmt.replace(RegExp.$1, mouth);
				}
				if(/(d+)/.test(fmt)) { // 日
					let mydate = date.getDate();
					if(mydate < 10) {
						mydate = '0' + mydate;
					}
					fmt = fmt.replace(RegExp.$1, mydate);
				}

				return fmt;
			},
      commentStr(content){
        let rel =/#[^#]+#/g;
        return content.replace(rel,'<i>$&</i>')
      }
    },
    computed:{
      selectComments(){
        if(this.selectType==ALL){//全部
            return this.ratings.comments;
        }else if(this.selectType==PICTURE){//有图
            let arr=[];
            this.ratings.comments.forEach((comment)=>{
              if(comment.comment_pics.length){
                arr.push(comment);
              }
            });
            return arr;
        }else{//点评
            return this.ratings.comments_dp.comments;
        }
      }
    },
    components:{
      Star,
      Split,
      BScroll
    },
    created(){
      this.$axios
      .get("/api/ratings")
      .then((response)=>{
        var dataSource=response.data;
        if(dataSource.code==0){
          this.ratings= dataSource.data;

          //初始化滚动
          this.$nextTick(()=>{
            if(!this.scroll){
              this.scroll=new BScroll(this.$refs.ratingView,{
                click:true
              });
            }else{
              this.scroll.refresh();
            }
          })

        }
      })
      .catch(function (error) {
        console.log(error);
      });
    }
}
</script>

<style>

    @import url("./Ratings.css");
</style>