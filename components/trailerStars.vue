<template name="trailerStars">
	<view class="movie-score-wapper">
		<!-- #ifdef MP-WEIXIN -->
		
		<!-- #endif -->
		<!-- #ifdef APP-PLUS -->
		
		<!-- #endif -->
		<image v-for="(yellow,index) in yelloScore" :key="index" src="../../static/icos/star-yellow.png"  class="star-ico"></image>
		<!-- <image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image>
		<image src="../../static/icos/star-yellow.png" class="star-ico"></image> -->
		<image v-for="(gray,index) in grayScore" :key="index+5" src="../../static/icos/star-gray.png" class="star-ico"></image>
		<view v-if="showNum ==1" class="move-score">
			{{innerScore}}
		</view>
	</view>
</template>

<script>
	export default {
		name:"trailerStars",
		data() {
			return {
				yelloScore:0,
				grayScore:5
			}
		},
		methods: {
			
		},
		props:{
			innerScore:0,
			showNum:0
		},
		created() {
			var tempScore = 0;
			if(this.innerScore != null && this.innerScore != undefined && this.innerScore != ''){
				tempScore = this.innerScore;
				
			}
			var yelloScore = parseInt(tempScore /2);
			var grayScore = 5 - yelloScore;
			this.yelloScore = yelloScore;
			this.grayScore = grayScore;
			
		}
	}
</script>

<style>
.movie-score-wapper{
	display: flex;
	flex-direction: row;
	
}
.star-ico{
	width: 20upx;
	height: 20upx;
	margin-top: 6upx;
	
}
.move-score{
	font-size: 12px;
	color: grey;
	margin-left: 8upx;
}
</style>
