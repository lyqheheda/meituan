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
                    </div>
                    <div class="img-wrapper" v-if="comment.comment_pics.length">
                    <img v-for="item in comment.comment_pics" :src="item.thumbnail_url" alt="">
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
            // console.log(arr[0].comment_pics[0].thumbnail_url);
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

.ratings {
    position: absolute;
    top: 191px;
    left: 0;
    bottom: 0;
    width: 100%;
    overflow: hidden;
}

.ratings .ratings-wrapper .overview{
    padding: 20px 0 18px 0;
    display: flex;
}

.ratings .ratings-wrapper .overview .overview-left{
    flex: 1;
    padding-left: 26px;
}

.ratings .ratings-wrapper .overview .overview-left .comment-score{
    float: left;
    width: 48px;
    text-align: center;
    margin-right: 26px;
}

.ratings .ratings-wrapper .overview .overview-left .comment-score .score{
    font-size: 23px;
    font-weight: 800;
    color: #ffb000;
    margin-bottom: 9px;
}

.ratings .ratings-wrapper .overview .overview-left .comment-score .text{
    font-size: 11px;
    color: #666666;
}

.ratings .ratings-wrapper .overview .overview-left .other-score{
    float: left;
    margin-top: 3px;
}



.ratings .ratings-wrapper .overview .overview-left .other-score .item{
    height: 11px;
}

.ratings .ratings-wrapper .overview .overview-left .other-score .text{
    font-size: 11px;
    color: #666666;
    margin-right: 11px;
    float: left;
}

.ratings .ratings-wrapper .overview .overview-left .other-score .star{
    margin-right: 11px;
    float: left;

}

.ratings .ratings-wrapper .overview .overview-left .other-score .score{
    font-size: 11px;
    float: left;
    color: #ffb000;
}

.ratings .ratings-wrapper .overview .overview-left .other-score .quality-score{
    margin-bottom: 14px;
}

.ratings .ratings-wrapper .overview .overview-right{
    flex: 0 0 100px;
    text-align: center;
    border-left: 1px solid #9d9d9d;
}

.ratings .ratings-wrapper .overview .overview-right .delivery-score{
}

.ratings .ratings-wrapper .overview .overview-right .delivery-score .score{
    font-size: 19px;
    font-weight: 500;
    color: #999999;
    margin-bottom: 10px;
    margin-top: 3px;
}

.ratings .ratings-wrapper .overview .overview-right .delivery-score .text{
    font-size: 11px;
    color: #999999;
}

.ratings .content {
    padding: 16px;
}

.ratings .content .rating-select{
    width: 100%;
    box-sizing: border-box;
    font-size: 0;
    border: 1px solid #ffb000;
    margin-bottom: 11px;
    border-radius: 3px;
    border-right: 0;
}

.ratings .content .rating-select .item{
    width: 33.3%;
    display: inline-block;
    height: 33px;
    line-height: 33px;
    font-size: 14px;
    text-align: center;
    border-right: 1px solid #ffb000;
    box-sizing: border-box;
    color: #ffb000;
}

.ratings .content .rating-select .item:last-child{
    /* border-right: 0; */
}

.ratings .content .rating-select .item:last-child img{
    height: 14px;
    vertical-align: middle;
}

.ratings .content .rating-select .item.active{
    background: #ffb000;
    color: black;
}


.ratings .content .labels-view{
    /* margin-bottom: 14px; */
}
.ratings .content .labels-view .item{
    display: inline-block;
    height: 27px;
    line-height: 27px;
    padding: 0 10px;
    font-size: 12px;
    background: #f4f4f4;
    margin-right: 6px;
    margin-bottom: 6px;
    border-radius: 3px;
    color: #999999;
}

.ratings .content .labels-view .item.highlight{
    color: #656565;
}



.ratings .content .rating-list{

}

.ratings .content .rating-list .comment-item{
    padding: 16px 16px 16px 0;
    border-bottom: 1px solid #f4f4f4;
    width: 100%;
    box-sizing: border-box;
    display: flex;
}

.ratings .content .rating-list .comment-item .comment-header{
    flex: 0 0 35px;
    margin-right: 11px;
}

.ratings .content .rating-list .comment-item .comment-header img{
    width: 35px;
    height: 35px;
    border-radius: 50%;
}

.ratings .content .rating-list .comment-item .comment-main{
    flex: 1;
}

.ratings .content .rating-list .comment-item .comment-main .user{
    width: 50%;
    float: left;
    font-size: 11px;
    color: #333333;
}

.ratings .content .rating-list .comment-item .comment-main .time{
    width: 50%;
    float: right;
    text-align: right;
    font-size: 9px;
    color: #666666;
}

.ratings .content .rating-list .comment-item .comment-main .star-wrapper{
    float: left;
    margin-top: 12px;
    margin-bottom: 15px;
    width: 100%;
}

.ratings .content .rating-list .comment-item .comment-main .star-wrapper .text{
    font-size: 11px;
    color: #999999;
    float: left;

}

.ratings .content .rating-list .comment-item .comment-main .star-wrapper .star{
    float: left;
    margin-left: 7px;
}

.ratings .content .rating-list .comment-item .comment-main .c_content{
    float: left;
    font-size: 13px;
    line-height: 19px;
    width: 100%;
}

.ratings .content .rating-list .comment-item .comment-main .c_content i{
    color: #576b95;
}

.ratings .content .rating-list .comment-item .comment-main .img-wrapper{
    margin-top: 9px;
    float: left;
}

.ratings .content .rating-list .comment-item .comment-main .img-wrapper img{
    width: 175px;
}
</style>