<template>
	<view class="page">
		<view class="search-block">
			<view class="search-ico-wapper">
				<image src="../../static/icos/search.png" class="search-ico"></image>
				
			</view>
			<input type="text" value="" placeholder="搜索预告"  class="search-text" @confirm="searchMe" confirm-type="search" focus/>
		</view>
		<view class="movie-list page-block">
			<view class="movie-wapper" v-for="(search,index) in searchList" :key="index">
				<image :src="search.cover" 
				:data-trailerId="search.id"
				@click="showTrailer"
				class="poster"></image>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {                                                                                                                                                                    
				searchList:[],
				keywords:"",
				page:1,
				totalPages:1,
				pageSize:6
			}
		},
		onReachBottom(){
			console.log('下拉')
			var me = this;
			var page = me.page + 1;
			var keywords = me.keywords;
			var totalPages = me.totalPages;
			
			if(page > totalPages){
				return;
			}
			
			me.pagedTrailerList(keywords,page,15)
		},
		onLoad(){
			var me = this;
			
			uni.showLoading({
				mask:true,
				title:"请稍后"
			})
			//uni.showNavigationBarLoading()
			
			
			uni.request({
				url: me.serverUrl + '/search/list?qq=1931889491&keywords=&page=&pageSize=', //仅为示例，并非真实接口地址。
				method: 'POST',
				success: res => {
					if (res.data.status == 200) {
						me.searchList = res.data.data.rows;
					}
				},
				complete:()=>{
					uni.stopPullDownRefresh();
					uni.hideLoading()
					//uin.showNavigationBarLoading(false)
				}
			});
		},
		methods: {
			showTrailer(e){
				var trailerId = e.currentTarget.dataset.trailerid;
				uni.navigateTo({
					url:"../movie/movie?trailerId ="+trailerId
				})
			},
			pagedTrailerList(keywords,page,pageSize){
				var me = this;
				
				uni.showLoading({
					mask:true,
					title:"请稍后"
				})
				//uni.showNavigationBarLoading()
				
				
				uni.request({
					url: me.serverUrl + '/search/list?qq=1931889491&keywords='+keywords+'&page='+page+'&pageSize='+pageSize, //仅为示例，并非真实接口地址。
					method: 'POST',
					success: res => {
						if (res.data.status == 200) {
							var tempList = res.data.data.rows;
							me.searchList = me.searchList.concat(tempList)
							me.totalPages = res.data.data.total;
							me.page =page;
						}
					},
					complete:()=>{
						uni.stopPullDownRefresh();
						uni.hideLoading()
						//uin.showNavigationBarLoading(false)
					}
				});
			},
			
			searchMe(e){
				var me = this;
				var value = e.detail.value;
				me.keywords = value;
				me.searchList = [];
				
				me.pagedTrailerList(value,1,15)
				
				
			}
		}
	}
</script>

<style>
@import url("search.css");
</style>
