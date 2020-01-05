<template>
	<!-- 轮播图 -->
	<view class="page">
		<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000">
			<swiper-item v-for="carousel in carouselList" :key="carousel.id">
				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + carousel.movieId">
				<image :src="carousel.image" class="carousel"></image>
				</navigator>
			</swiper-item>
			<!-- <swiper-item>
				<image src="../../static/carousel/spiderman.png" class="carousel"></image>
			</swiper-item> -->
		</swiper>
		<!-- 超音预告 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/hot.png" class="hot-ico"></image>
				<view class="hot-title">热门超英</view>
			</view>
		</view>
		<scroll-view scroll-x="true" class="page-block hot">
			<view>
				<view class="single-poster" v-for="hot in hotList" :key="hot.id">
					<view class="poster_wapper">
						<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + hot.id">
						<image :src="hot.cover" class="poster"></image>
						</navigator>
						
						<view class="movie-name">{{ hot.name }}</view>
						<trailerStars :innerScore="hot.score" :showNum="1"></trailerStars>
						<!-- <view class="movie-score-wapper">
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
						<image src="../../static/icos/star-gray.png" class="star-ico"></image>
						<view class="move-score">
							{{hot.score}}
						</view>
					</view> -->
					</view>
				</view>
			</view>
		</scroll-view>
		<!-- 热门预告 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/interest.png" class="hot-ico"></image>
				<view class="hot-title">热门预告</view>
			</view>
		</view>

		<view class="page-block hot-movies">
			<video v-for="trailer in trailerList" 
			:key="trailer.id" :src="trailer.trailer" 
			:poster="trailer.poster" 
			class="hot-movie-single"></video>
		</view>
		
		<!-- 猜你喜欢 -->
		<view class="page-block super-hot">
			<view class="hot-title-wapper">
				<image src="../../static/icos/guess-u-like.png" class="hot-ico"></image>
				<view class="hot-title">猜你喜欢</view>
			</view>
		</view>
		
		<view class="page-block guess-u-like">
			<view v-for="(guess,index) in guessULikeList" :key="index" class="single-like-movie">
				<navigator open-type="navigate" :url="'../movie/movie?trailerId=' + guess.id">
				<image :src="guess.cover" class="like-movie"></image>
				</navigator>
				
				<view class="movie-dec">
					<view class="movie-title">
						{{guess.name}}
					</view>
					<trailerStars :innerScore="8" :showNum="1"></trailerStars>
					<view class="movie-info">
						{{guess.basicInfo}}
					</view>
					<view class="movie-info">
						{{guess.releaseDate}}
					</view>
				</view>
				<view class="movie-oper" :data-index ="index" @click="praiseMe">
					<image src="../../static/icos/praise.png" class="praise-ico"></image>
					<view class="praise-me">
						点赞
					</view>
					<view :animation="animationDataArr[index]" class="praise-me animation-opacity">
						+1
					</view>
				</view>
			</view>
			
		</view>
		
	</view>
</template>

<script>
//import common from '../../common/common.js'
import helloComp from '../../components/helloComp.vue';
import trailerStars from '../../components/trailerStars.vue';
export default {
	data() {
		return {
			carouselList: [],
			hotList: [],
			trailerList: [],
			animationData:{},
			guessULikeList:[],
			animationDataArr:[
				{},{},{},{},{}
			]
		};
	},
	components: {
		'hello-Comp': helloComp,
		trailerStars
	},
	onUnload(){
		this.animationData = {};
		animationDataArr:[
			{},{},{},{},{}
		]
	},
	onPullDownRefresh(){
		this.refresh()
	},
	onLoad() {
		var me = this;
		
		// #ifdef APP-PLUS || MP-WEIXIN
		this.animation = uni.createAnimation();
		// #endif
		
		// uni.request({
		// 	url: me.serverUrl + '/index/guessULike?qq=1931889491', //仅为示例，并非真实接口地址。
		// 	method: 'POST',
		// 	success: res => {
		// 		console.log(res.data);
		// 		if (res.data.status == 200) {
		// 			me.guessULikeList = res.data.data;
		// 		}
		// 	}
		// });
		//请求轮播图数据
		uni.request({
			url: me.serverUrl + '/index/carousel/list?qq=1931889491', //仅为示例，并非真实接口地址。
			method: 'POST',
			success: res => {
				console.log(res.data);
				if (res.data.status == 200) {
					me.carouselList = res.data.data;
				}
			}
		});
		uni.request({
			url: me.serverUrl + '/index/movie/hot?qq=1931889491&type=superhero', //仅为示例，并非真实接口地址。
			data: {
				type: 'superhero'
			},
			method: 'POST',
			success: res => {
				console.log(res.data);
				if (res.data.status == 200) {
					me.hotList = res.data.data;
				}
			}
		});
		uni.request({
			url: me.serverUrl + '/index/movie/hot?qq=1931889491&type=trailer', //仅为示例，并非真实接口地址。
			method: 'POST',
			success: res => {
				console.log(res.data);
				if (res.data.status == 200) {
					me.trailerList = res.data.data;
				}
			}
		});
		me.refresh();
	},
	methods: {
		
		refresh(){
			var me = this;
			
			uni.showLoading({
				mask:true
			})
			//uni.showNavigationBarLoading()
			
			
			uni.request({
				url: me.serverUrl + '/index/guessULike?qq=1931889491', //仅为示例，并非真实接口地址。
				method: 'POST',
				success: res => {
					console.log(res.data);
					if (res.data.status == 200) {
						me.guessULikeList = res.data.data;
					}
				},
				complete:()=>{
					uni.stopPullDownRefresh();
					uni.hideLoading()
					uni.stopPullDownRefresh()
				}
			});
		},
		praiseMe(e){
			// #ifdef APP-PLUS||MP-WEIXIN
			
			
			var index = e.currentTarget.dataset.index;
			console.log()
			this.animation.translateY(-60).opacity(1).step({
				duration:400
			});
			// this.animationData = this.animation.export();
			this.animationData = this.animation;
			this.animationDataArr[index] = this.animationData.export;
			setTimeout(function() {
				this.animation.translateY(0).opacity(0).step({
					duration:0
				});
				this.animationData = this.animation;
				this.animationDataArr[index] = this.animationData.export;
			}.bind(this), 500);
			// #endif
		}
	}
};
</script>

<style>
@import url('index.css');
</style>
