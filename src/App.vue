<template>
  <div id="app">
  	<view-box>
  		<x-header class="header"  slot="header" :left-options="{showBack: false}">
  				<div slot="left">直播</div>
  				<div>新闻</div>
  				<div slot="right">搜索</div>
  		</x-header>
  		<sc :lock-y="true">
  			<div>
  				<tab class="tab">
		  			<tab-item selected>推荐</tab-item>
		  			<tab-item>要闻</tab-item>
		  			<tab-item>娱乐</tab-item>
		  			<tab-item>体育</tab-item>
		  			<tab-item>科技</tab-item>
		  			<tab-item>游戏</tab-item>
		  		</tab>
  			</div>
  		</sc>
  		<scroller
  			:on-refresh="refresh"
  			refresh-text="loading"
  			:on-infinite="infinite"
  			ref="myRef"
  			>
	  		<div id="swip" v-on:touchmove.stop v-on:touchstart.stop v-on:touchend.stop>
			    <swiper loop auto :list="swiperlist" v-model="swiperIndex"
			    	v-on:touchend.native.stop v-on:touchstart.native.stop v-on:touchmove.native.stop
			        style="width:100%;margin:0 auto;" height="8rem">
			    </swiper>
		   </div>
	  		<marquee>
		      <marquee-item v-for="list in marqueelist" class="align-middle">{{list.title}}</marquee-item>
		    </marquee>
		    <panel :list="datalist"></panel>
		    <panel :list="moredatalist"></panel>
		  </scroller>
  		<tabbar slot="bottom">
  			<tabbar-item>
		        <i slot="icon" class="fa fa-home" aria-hidden="true"></i>
		        <span slot="label">首页</span>
		    </tabbar-item>
		    <tabbar-item link="/Search">
		        <i slot="icon" class="fa fa-th-list" aria-hidden="true"></i>
		        <span slot="label">视频</span>
		    </tabbar-item>
		    <tabbar-item>
		        <i slot="icon" class="fa fa-compass" aria-hidden="true"></i>
		        <span slot="label">发现</span>
		    </tabbar-item>
		    <tabbar-item>
		        <i slot="icon" class="fa fa-id-card-o" aria-hidden="true"></i>
		        <span slot="label">频道</span>
		    </tabbar-item>
		    <tabbar-item>
		        <i slot="icon" class="fa fa-user-circle" aria-hidden="true"></i>
		        <span slot="label">我</span>
		    </tabbar-item>
  		</tabbar>
  	</view-box>
    <router-view/>
  </div>
</template>

<script>
import {ViewBox,XHeader,Tabbar,TabbarItem,Tab, TabItem,Scroller as sc,Swiper,SwiperItem,Marquee, MarqueeItem,Panel,Toast} from 'vux';
//import Vswiper from '@/components/Vswiper';
var refreshKey = ['A','B01','B02','B03','B04','B05','B06','B07','B08','B09','B010'];
var key = 0;
var start = 0;//上拉加载起始值
var end = start + 9;//上拉加载每次加载10条
var initLoaded = false; //初始化数据是否加载完成

var keyvalue = 'A';
function getRefreshKey(){
	key++;
	if(key == refreshKey.length){
		key = 0;
	}
	keyvalue = refreshKey[key];
}

export default {
  name: 'app',
  components: {
  	ViewBox,
  	XHeader,
  	Tabbar,
  	TabbarItem,
  	Tab, 
  	TabItem,
  	sc,
  	Swiper,
  	SwiperItem,
//	Vswiper,
  	Marquee, //上下滚动新闻组件
  	MarqueeItem,
  	Panel,//主题部分滚动新闻组件
  	Toast//刷新之后的提示功能组件
  },
  created() {
//	console.log(1)
		this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html').then(data =>{
//			console.log(data);
				//通过以下发送请求得到轮播图的数据
				this.swiperlist = data.focus.filter(item =>{
					return item.addata === null && item.picInfo[0];
				}).map(item =>{
					return {
						url: item.link,
						img: item.picInfo[0].url,
						title: item.title
					}
				});
				//新闻列表的数据
				this.datalist = data.news.filter(item =>{
					return item.addata === null && item.picInfo[0];
				}).map(item =>{
					return {
						src: item.picInfo[0].url,
						title: item.title,
						desc: item.digest,
						url: item.link
					}
				});
				//滚动新闻数据
				this.marqueelist = data.live.filter(item =>{
					return item.addata === null;
				}).map(item =>{
					return {
						title: item.title
					}
				});
				
				initLoaded = true;//只要created加载完成即为true
		});

  },
  data(){
//	var datalist = [];
//	for(var i=0;i<10;i++){
//		datalist.push({
//      src: 'http://somedomain.somdomain/x.jpg',
//      fallbackSrc: 'http://placeholder.qiniudn.com/60x60/3cc51f/ffffff',
//      title: '标题一',
//      desc: '由各种物质组成的巨型球状天体，叫做星球。星球有一定的形状，有自己的运行轨道。',
//      url: '/component/cell'
//    })
//	}
  	return {
  		swiperlist: [],
			swiperIndex: 0,
			datalist: [],
			marqueelist: [],
			moredatalist: []
  	}
 },
 methods: {
 	//下拉刷新
 		refresh() {
 			getRefreshKey();
 			this.$jsonp('http://3g.163.com/touch/jsonp/sy/recommend/0-9.html',{
 				miss: '30',
 				refresh: keyvalue
 			}).then(data =>{
				//刷新新闻列表的数据
//				console.log(this.$refs.myRef)scroller的实例对象，ref是它的属性
				this.datalist = data.list.filter(item =>{
					return item.addata === null && item.picInfo[0];
				}).map(item =>{
					return {
						src: item.picInfo[0].url,
						title: item.title,
						desc: item.digest,
						url: item.link
					}
				});
				//this.$refs.myRef得到该对象，调用finishPullToRefresh()停止刷新完成之后的下拉刷新
				this.$refs.myRef.finishPullToRefresh();
				this.$vux.toast.text(`共刷新了${this.datalist.length}条新闻`, 'top');
			})
 		},
 		infinite() {
 			if(!initLoaded){
 				this.$refs.myRef.finishInfinite();
 				return;
 			}
 			console.log(2)
 			this.$jsonp(`http://3g.163.com/touch/jsonp/sy/recommend/${start}-${end}.html`,{
 				miss: '30',
 				refresh: keyvalue
 			}).then(data =>{
				//上拉加载新闻列表的数据
//				console.log(this.$refs.myRef)scroller的实例对象，ref是它的属性
				setTimeout(() =>{
					var datalist = data.list.filter(item =>{
						return item.addata === null && item.picInfo[0];
					}).map(item =>{
						return {
							src: item.picInfo[0].url,
							title: item.title,
							desc: item.digest,
							url: item.link
						}
					});
					this.moredatalist = this.moredatalist.concat(datalist);
					start +=10;
					end = start + 9;
					this.$refs.myRef.finishInfinite();
				},1000)
			})
 		}
 }
}
</script>

<style lang="less">
@import "css/reset.css";
@import "css/font-awesome.min.css";
@import '~vux/src/styles/reset.less';
@r:50rem;
html, body {
    margin: 0;
    height: 100%;
    width: 100%;
    overflow-x: hidden;
    font-family: "Helvetica Neue", Arial, "PingFang SC", "Hiragino Sans GB", "Source Han Sans CN", Roboto, "Microsoft YaHei", sans-serif;
}
#app {
    height: 100%;
    .tab {
    	width: 600px;
    	margin-top: 46px;
    }
    ._v-container {
    	position: absolute;
    	top: 90px;
    	left: 0;
    }
    .align-middle {
    	height: 40px;
    	line-height: 40px;
    	font-size: 18px;
    }
    .header {
    	position: absolute;
    	top: 0;
    	left: 0;
    	z-index: 99;
    	width: 100%;
    }
		.vux-slider > .vux-indicator, .vux-slider .vux-indicator-right {
			position: absolute;
    	right: 0px;
   	 	bottom: 4px;
		}
		.weui-media-box__hd,.weui-media-box__hd img{
			width: 102px;
			height: 78px;
		}
		.weui-media-box__bd {
			height: 78px;
		}
		.loading-layer {
			padding-bottom: 20px;
		}
}
#swip .vux-icon-dot.active {
		background: #000;
}
#app .vux-slider > .vux-indicator > a > .vux-icon-dot.active, 
#app .vux-slider .vux-indicator-right > a > .vux-icon-dot.active {
	background-color: #000;
}
</style>
