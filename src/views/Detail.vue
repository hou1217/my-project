<template>
  <div class="container">
    <headers></headers>
    
    <div class="detail" v-show="isShow">
      <input type="hidden" id="article_id" :value="id">
      <input type="hidden" id="token" :value="token">
      <input type="hidden" id="device_num" :value="device">
      <div v-if="video">
            <div class="video-placeholder">
              <div class="video-js">
                <video class="vjs-tech" webkit-playsinline  preload="none" controls="true"
                :poster="coverUrl" :src="url"></video>
              </div>
              
            </div>
            
      </div>
      <ul>
        <li class="li-channel" id="article_channel">
          <a href="#">新闻</a>
        </li>
        <li class="li-title" id="article_title">
          {{title}}
        </li>
        <li class="li-media">
          <div class="article-meta-img div-media">
            <img id="portraitUrl" :src="portraitUrl" class="img-media">
          </div>
          <div class="article-meta-info div-media">
            <div class="article-meta-name" id="article_mediaName">
              {{mediaNames}}
            </div>
            <div class="article-meta-date" id="article_time">
              {{time | formatDate}}
            </div>
          </div>
          <div class="article-meta-follow div-media" id="follow1">
            <input type="hidden" id="follow">
            <span class="media-un-follow" id="follow-action">+关注</span>
          </div>
        </li>
        <li class="li-content" id="article_content" >
          <div v-for='(item,index) in content' :key="index">
            <p v-if='item.content'>{{item.content}}</p>
            <div class="content-panel" v-if='item.url'>
              <img class="content-img" :src="item.url" alt="图片信息">
            </div>
          </div>
        </li>
        <li class="li-keywords" id="article_keywords">
          <span v-for='(item,index) in keywords' :key="index">
            {{item}}
          </span>
        </li>
        <li class="li-footer">
          <a id="origin-link" :href="originUrl">查看原文</a>
          <div class="footer-panel">
            <span class="footer-source" id="footer_source">本文来源：  {{origin}}</span>
            <span class="footer-author" id="footer_author">
              {{originAuthor}}
            </span>
          </div>
        </li>
        <li class="li-star">
          <div class="div-star">
            <img src="../assets/images/un_star.png" id="star_img" class="un-star">
            <span class="star-count">{{likes}}</span>
          </div>
        </li>
        <li class="li-star-panel">
          <div class="star-img-panel" id="star_img_panel">
            <!-- 点赞人的头像 -->
          </div>
          <div class="star-info-panel">
            <span class="star-count">{{likes}}</span>人点赞
            <span class="star-icon">></span>
          </div>
        </li>
        <li class="li-media li-media-bottom">
          <div class="article-meta-img div-media" onclick="mediaDetail('5a741e7433f17c50cc7b1209')">
            <img id="portraitUrlBottom" :src="portraitUrl" class="img-media">
          </div>
          <div class="article-meta-info div-media">
            <div class="article-meta-name" id="article_mediaNameBottom">
              {{mediaNames}}
            </div>
            <div class="media-fans" id="media-fans">
              <span>关注{{mediaFollows}}</span>
              <span>粉丝{{mediaFans}}</span>
            </div>
          </div>
          <div class="article-meta-follow div-media" id="follow2">
            <span class="media-un-follow" id="follow-action-bottom">+关注</span>
          </div>
        </li>
        <li class="li-advert">
          <img src="../assets/images/advert.png">
        </li>
        <recommend></recommend>
        <hotChat></hotChat>
      </ul>
    </div>
  </div>
</template>

<script type="text/javascript">
  import Headers from '../components/Headers.vue'
  import Recommend from '../components/Recommend.vue'
  import HotChat from '../components/HotChat.vue'
  import axios from 'axios'
  
  import {formatDate} from '../assets/js/Date.js'
	export default{
	  name: 'Detail',
	  components:{
    'headers':Headers,
    'recommend':Recommend,
    'hotChat':HotChat,
    },
    filters:{
      formatDate(time){
        var date = new Date(time);
        return formatDate(date,'yyyy-MM-dd hh:mm');
      }
    },
	  data(){
	    return {
	      coverUrl:"",
	      url:"",
	      video:false,
	      isShow:false,
	      id:this.$route.query.id,
	      token:this.$route.params.token,
	      device:this.$route.params.device_num,
	      title:'',
        content:[],
        time:0,
        keywords:[],
        mediaId:0,
        mediaNames:'',
        mediaFans:0,
        mediaFollows:0,
        portraitUrl:'',
        portraits:'',
        originAuthor:'',
        origin:'',
        originUrl:'',
        starCounts:0,
        likes:0,
	    }
	  },
	  methods:{
	    getDetail(pay){
	      console.log("加载文章详情。");
	      const options = {
          method: 'GET',
          headers: {
            'content-type': 'application/json;',
            'WALLAN-TOKEN': '5d4139e70b35803e75414ddef0f57cd9',
            'WALLAN-DEVICENUM':'12000000000',
            'USERNAME':sessionStorage.getItem("username")
          },
          url:this.GLOBAL.serverUrl+'/media-dc/article/detail'+'?id='+pay.kind
        };
        axios(options)
          .then(function (res){
            console.log(res.data);
            //console.log(res);
            console.log("文章的id:"+pay.kind);
            if(res.data.data){
              this.isShow = true;
              if(res.data.data.url){
                this.video = true;
              }
             
              this.coverUrl = res.data.data.coverUrl;
              this.url = res.data.data.url;
              //this.dataList = res.data.datas;
              this.time = res.data.data.createdAt;
              this.title = res.data.data.title;
              if(res.data.data.media){
                this.mediaNames = res.data.data.media.name;
                this.portraitUrl = res.data.data.media.portraitUrl;
                this.mediaId = res.data.data.media.id;
                this.mediaFans = res.data.data.media.fans;
                this.mediaFollows = res.data.data.media.follows;
              }else{
                this.mediaNames = '';
                this.portraitUrl = '';
                this.mediaId = 0;
                this.mediaFans = 0;
                this.mediaFollows = 0;
              }
              
              
              
              this.keywords = res.data.data.keywords;
              this.content = res.data.data.items;
              this.origin = res.data.data.origin;
              this.originUrl = res.data.data.originUrl;
              this.originAuthor = res.data.data.originAuthor;
              this.linkes = res.data.data.likes;
              
              
            }else{
              this.isShow = false;
            }

          }.bind(this))
          .catch(function (error) {
            console.log(error);
          });
      },
	  },
	  mounted(){
	    document.body.scrollTop = document.documentElement.scrollTop = 0;//滚动条回到顶部
      this.getDetail({
        kind:this.$route.query.id,
      });
	  },
	  destroyed(){
      console.log("detailPage destroyed");
    },
	}
</script>

<style scoped>

.detail ul{
  -webkit-padding-start: 0;
}
.detail{
  margin: auto;
  
  border-top: solid 1px #eeeeee;
}
.detail ul{
  padding: 0 30px;
}
.detail ul li{
  list-style-type: none;
  width: 100%;
  float: none;
}
.li-channel{
  color: #333333;
  font-size: 28px;
  margin-top: 30px;
}
.li-title{
  margin-top: 30px;
  font-size: 44px;
  color: #333333;
  font-weight: bold;
}
.li-media{
  margin-top: 40px;
  height: 80px;
}
.detail ul li .div-media{
  float: left;
}
.img-media{
  border-radius: 50%;
  width: 80px;
}
.detail ul li .article-meta-follow{
  float: right;
  line-height: 64px;
}
.article-meta-info{
  margin-left: 30px;
}
.article-meta-info .article-meta-name{
  font-size: 30px;
  color: #333333;
}
.article-meta-info .article-meta-date{
  color: #999999;
  font-size: 22px;
  margin-top: 7px;
}
.li-content{
  margin-top: 80px;
}
.li-content p {
  margin-top: 32px;
  font-size: 34px;
  color: #000;
  line-height: 54px;
}
.li-keywords{
  margin-top: 80px;
}
.li-keywords span {
  border: solid 1px #426fa1;
  border-radius: 15px;
  padding: 0 10px;
  font-size: 21px;
  color: #436fa0;
  height: 50px;
  display: -webkit-inline-box;
  line-height: 50px;
  margin-top: 5px;
}
.li-keywords span:nth-child(n+2){
  margin-left: 20px;
}
.li-footer{
  color: #807a7a;
  line-height: 60px;
  height: 60px;
  margin-top: 5%;
}
.li-footer span.footer-author{
  margin-left: 20px;
  font-size: 24px;
  color: #999999;
}
.li-footer span.footer-source{
  font-size: 24px;
  color: #999999;
}
a{
  color: #436fa0;
  text-decoration: none;
}
.li-footer a{
  color: #436fa0;
  font-size: 28px;
  text-decoration: none;
}
.li-footer .footer-panel{
  float: right;
}
.article-meta-follow span{
  border-radius: 30px;
  width: 140px;
  height: 60px;
  font-size: 28px;
  display: block;
  text-align: center;
}
.article-meta-follow span.media-un-follow{
  border: solid 1px #00d9b7;
  color: #00d8b7;
}
.article-meta-follow span.media-follow{
  border: solid 1px #cccccc;
  color: #999999;
}
.li-content img{
  width: 100%;
  /* margin-left: -30px;
    width: -webkit-calc(100% + 60px);
    width: -moz-calc(100% + 60px);
    width: calc(100% + 60px); */
  margin: 32px auto 0;
    /*max-height: 460px;*/
    /*max-width: 100%;*/
  display: block;
}
img+p {
  margin-top: 60px;
}
.li-star{
  margin-top: 80px;
  height: 30px;
}
.li-star .div-star{
  float: right;
}
.li-star .div-star img{
  transition: width 2s;
  -webkit-transition: width 2s;
}
.li-star span{
  margin-left: 8px;
  font-size: 24px;
  color: #999999;
}
.li-star-panel{
  margin-top: 30px;
  line-height: 60px;
  height: 90px;
  border-bottom: solid 1px #eeeeee;
}
.li-star-panel .star-img-panel{
  float: left;
}
.li-star-panel .star-img-panel img{
  border-radius: 50%;
  width: 60px;
  height: 60px;
}
.li-star-panel .star-img-panel img:nth-child(n+2){
  margin-left: 20px;
}
.li-star-panel .star-info-panel{
  float: right;
  font-size: 24px;
  color: #999999;
}
.li-star-panel .star-icon{
  margin-left: 20px;
}
.li-media-bottom .media-fans{
  color: #999999;
  font-size: 22px;
  margin-top: 7px;
}
.li-media-bottom .media-fans span:nth-child(2){
  margin-left: 24px;
}
.li-advert{
  margin-top: 60px;
}
.li-advert img{
  margin-left: -30px;
  width: -webkit-calc(100% + 60px);
  width: -moz-calc(100% + 60px);
  width: calc(100% + 60px);
}


.container .images,.video{
  border-top: solid 1px #333333;
  background-color: #222222;
}
.images .article-meta-info .article-meta-name,.video .article-meta-info .article-meta-name{
  color: #ffffff;
}
.images .article-meta-info .article-meta-date,.video .article-meta-info .article-meta-date{
  color: #999999;
}
.images .li-title,.video .li-title{
  color: #ffffff;
}
.images .li-content,.video .li-content{
  color: #ffffff;
}
.images .li-recommend .recommend-title,.video .li-recommend .recommend-title{
  color: #ffffff;
}
.images .li-recommend .article-list div.article-info,.video .li-recommend .article-list div.article-info{
  background-color: #282828;
}
.images .li-recommend .article-list div.article-info .article-title,.video .li-recommend .article-list div.article-info .article-title{
  color: #ffffff;
}
.play-count{
  color: #ffffff;
  font-size: 28px;
}
.li-video-channel{
  margin-top: 30px;
}
.video-channel{
  margin-left: 20px;
  border-radius: 30px;
  font-size: 24px;
  color: #333333;
  height: 40px;
  background-color: #ffffff;
  opacity: 0.6;
  display: inline-block;
  padding: 0 20px;
}
.images .li-channel{
  color: #ffffff;
}
.images .li-content .content-group{
  /* padding-top: 60px; */
}
.images .li-content .content-group .content-desc{
  margin-top: 20px;
  font-size: 24px;
  color: #999999;
}
.video-placeholder{
  width: 100%;
  height: 452px;
  position: relative;
}
.video-js{
  position: absolute;
  top:88px;
  background-color: #000;
  width: 100%;
  height: 100%;
}
.video-js .vjs-tech {
    width: 100%;
    height: 100%;
}
</style>