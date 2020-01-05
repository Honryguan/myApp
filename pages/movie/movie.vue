<template>
	<view>
		<view class="player">
			<video 
			:key="trailerInfo.id" :src="trailerInfo.trailer" 
			:poster="trailerInfo.poster" 
			class="movie"
			controls></video>
		</view>
		<view class="movie-info">
			<image :src="trailerInfo.cover" class="cover"></image>
			<view class="movie-desc">
				<view class="title">
					{{trailerInfo.name}}
					
				</view>
				<view class="basic-info">
					{{trailerInfo.basicInfo}}
					
				</view>
				<view class="basic-info">
					{{trailerInfo.originalName}}
					
				</view>
				<view class="basic-info">
					{{trailerInfo.totalTime}}
					
				</view>
				<view class="basic-info">
					{{trailerInfo.releaseDate}}
					
				</view>
				<view class="score-block">
					
					<view class="big-score">
						<view class="score-words">
							综合评分:
						</view>
						<view class="movie-score">
							{{trailerInfo.score}}
							
						</view>
					</view>
					<view class="score-stars">
						<block v-if="trailerInfo.score >=0">
							<trailerStars :innerScore="trailerInfo.score" :showNum="0"></trailerStars>
						</block>
						
						<view class="prise-counts">
							{{trailerInfo.prisedCounts}} 人点赞
						</view>
					</view>
				</view>
			</view>
		</view>
		
		<!-- 剧情介绍 -->
		<view class="line-wapper">
			<view class="line">
				
			</view>
			
		</view>
		<view class="plots-block">
			<view class="plots-title">
				剧情介绍
			</view>
			<view class="plots-desc">
				{{trailerInfo.plotDesc}}
			</view>
		</view>
		
		<!-- 演职人员 -->
		<view class="scroll-block">
			<view class="plots-title">
				演职人员
			</view>
			<scroll-view scroll-x="true" class="scroll-list">
				<view class="actor-wapper" v-for="(direct,index) in directorArray" :key="index">
					<image  :src="direct.photo" @click="lookStaffs" :data-staffIndex="index"
				mode="aspectFill" class="single-actor"></image>
				<view class="actor-name">
					{{direct.name}}
					
				</view>
				<view class="actor-role">
					{{direct.role}}
				</view>
				</view>
				<view class="actor-wapper" v-for="(actor,index) in actorArray" :key="index+ directorArray.length">
					<image  :src="actor.photo" @click="lookStaffs" :data-staffIndex="index + directorArray.length"
				mode="aspectFill" class="single-actor"></image>
				<view class="actor-name">
					{{actor.name}}
					
				</view>
				<view class="actor-role">
					{{actor.role===1?'导演':'演员'}}
				</view>
				</view>
				
			</scroll-view>
		</view>
		<!-- 剧照 -->
		<view class="scroll-block">
			<view class="plots-title">
				剧照
			</view>
			<scroll-view scroll-x="true" class="scroll-list">
				<image v-for="(img,index) in plotPicsArray" :key="index" :src="img" 
				mode="aspectFill" class="plot-imag"
				@click="lookMe" :data-imgIndex="index"></image>
			</scroll-view>
		</view>
		
		<!--  -->
	</view>
</template>

<script>
	import trailerStars from '../../components/trailerStars.vue';
	export default {
		data() {
			return {
				trailerInfo:{},
				plotPicsArray:[],
				directorArray:[],
				actorArray:[]
			}
		},
		components:{
			trailerStars
		},
		onLoad(params){
			var trailerId = params.trailerId;
			var me = this;
			
			
			//修改导航栏属性
			uni.setNavigationBarColor({
				frontColor: '#ffffff',
				backgroundColor: '#000000'
			})
			
			
			uni.request({
				url: me.serverUrl + '/search/trailer/'+trailerId+'?qq=1931889491', //仅为示例，并非真实接口地址。
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						var trailerInfo = res.data.data;
						me.trailerInfo = trailerInfo;
						var plotPicsArray = JSON.parse(trailerInfo.plotPics);
						me.plotPicsArray = plotPicsArray;
					}
				}
			});
			
			uni.request({
				url: me.serverUrl + '/search/staff/'+trailerId+'/1'+'?qq=1931889491', //仅为示例，并非真实接口地址。
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						var directorArray = res.data.data;
						me.directorArray = directorArray;
					}
				}
			});
			
			uni.request({
				url: me.serverUrl + '/search/staff/'+trailerId+'/2'+'?qq=1931889491', //仅为示例，并非真实接口地址。
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						var actorArray = res.data.data;
				
						me.actorArray = actorArray;
					}
				}
			});
		},
		methods: {
			lookMe(e){
				var me = this;
				var imgIndex = e.currentTarget.dataset.imgindex;
				uni.previewImage({
					urls:me.plotPicsArray,
					current:me.plotPicsArray[imgIndex]
				})
			},
			lookStaffs(e){
				var me = this;
				var staffIndex = e.currentTarget.dataset.staffindex;
				var newStaffArray =[];
				newStaffArray = newStaffArray.concat(me.directorArray).concat(me.actorArray)
				
				var urls = [];
				var tt='';
				for(var i=0;i<newStaffArray.length;i++){
					var tempPhoto = newStaffArray[i].photo;
					urls.push(tempPhoto);
				}
				uni.previewImage({
					urls:urls,
					current:urls[staffIndex]
				})
			}
		}
	}
</script>

<style>
@import url("movie.css");
</style>
