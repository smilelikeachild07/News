<template>
	<!--轮播图-->
	<div id="swip" v-on:touchmove.stop v-on:touchstart.stop v-on:touchend.stop>
	    <swiper loop auto :list="demo06_list" v-on:touchend.native.stop v-on:touchstart.native.stop v-on:touchmove.native.stop  @on-index-change="demo06_onIndexChange"
	        style="width:100%;margin:0 auto;" height="8rem" dots-position="center">
	    </swiper>
    </div>
</template>

<script>
import {
    Swiper,
    SwiperItem
} from 'vux';	
import Vue from 'vue'
const baseList = [{
  url: 'javascript:',
  img: '../../static/img/Bur.jpg',
  title: 'Christopher Bailey 即将离开 Burberry'
}, {
  url: 'javascript:',
  img: '../../static/img/park.jpg',
  title: '厦门公寓里的“退化公园”展现了一种极端的城市生活'
}, {
  url: 'javascript:',
  img: '../../static/img/tatoo.jpg',
  title: '我来到孟州的武松公园，见到了网络传说中的纹身师强子'
//fallbackImg: 'https://static.vux.li/demo/3.jpg'
}];
const urlList = baseList.map((item, index) => ({
  url: 'http://www.vice.cn/',
  img: item.img,
  fallbackImg: item.fallbackImg,
  title: `${item.title}`
}));
export default {
	name: 'Vswiper',
	components: {
    	Swiper,
    	SwiperItem
	},
	methods: {
	    demo06_onIndexChange (index) {
	      this.demo06_index = index
	    }
	},
	data () {
	    return {
	      demo06_list: urlList
	    }
	}
};
</script>

<style lang="less">
	@import '~vux/src/styles/reset.less';
	@r:50rem;
	#swip {
		width: 100%;
		overflow: hidden;
	}
	#swip .vux-icon-dot.active {
		background: #000;
	}
	#swip .vux-swiper-desc {
		font-size: 43/@r;
		padding-bottom: 80/@r;
		height: 2.8rem;
		overflow: visible;
		white-space: pre-line;
	}
	
	#app .vux-slider > .vux-indicator > a > .vux-icon-dot.active, 
	#app .vux-slider .vux-indicator-right > a > .vux-icon-dot.active {
		background-color: #000;
	}
</style>