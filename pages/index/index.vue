<template>
	 <view>
		<view>
			<scroll-view
			scroll-x="true"
			scroll-with-animation="true"
			:scroll-left="scrollLeft"
			class="navBar" id="navBar"
			>
				<view v-for="(item,index) in tabList" 
				@click="changeTab(index)" 
				:key="item.id" class="nav-item" 
				:class="{current:index === tabCurrentIndex}" 
				:id="'tab'+index"
				@touchstart="test">
					{{item.name}}
				</view>
			</scroll-view>
		</view>
		<view>
			<mix-pulldown-refresh :top="90" @refresh="" @setEnableScroll="setEnableScroll">
				<swiper
				:duration="300"
				id="swiper"
				class="swiper-container">
					<swiper-item v-for="tabItem in tabBar" :key = "tabItem.id">
						<scroll-view
						:scroll-y="enableScroll"
						@scrolltolower="loadMore">
						<view class="news-list">
							<view v-for="(item,index) in tabItem.newsList" class="news-item">
								<text :class="['title'+item.type]">{{item.title}}</text>
				                <view :class="['img-list'+item.type]">
									<view v-for="(imgItem,imgIndex) in item.images" class="img-item">
											<image :src="imgItem" class="img"></image>	
									</view>
								</view>					
							</view>
						</view>
						</scroll-view>
					</swiper-item>
				</swiper>
			</mix-pulldown-refresh>
		</view>
		 
	 </view>
	 
	 
</template>

<script>
	import uniSwiperDor from "../../components/uni-swiper-dot/uni-swiper-dot.vue";
	import mixPulldownRefresh from "../../components/mix-refresh/mix-pulldown-refresh.vue";
	import json from "../../json.js";
	let windowWidth = 0,scollTimer = false,tabBar;
	export default {
		components:{
			uniSwiperDor,
			mixPulldownRefresh
		},
		data() {
			return {
			  tabCurrentIndex:0,
			  tabBar:[],
			  tabList:[{
				  "name":"财经",
				  "id":"1"
			  },{
				  "name":"情感",
				  "id":"2"
			  },{
				  "name":"娱乐",
				  "id":"3"
			  },{
				  "name":"游戏",
				  "id":"4"
			  },{
				  "name":"情感",
				  "id":"5"
			  },{
				  "name":"娱乐",
				  "id":"6"
			  },{
				  "name":"游戏",
				  "id":"7"
			  },{
				  "name":"情感",
				  "id":"8"
			  },{
				  "name":"娱乐",
				  "id":"9"
			  },{
				  "name":"游戏",
				  "id":"10"
			  }],
			  scrollLeft:0,
			  enableScroll:false
			}
		},
		onLoad() {

		},
		methods: {
		
			getElSize(id){
				return new Promise((res,rej) =>{
					let ul = uni.createSelectorQuery().select("#"+id);
					ul.fields({
						scrollOffset:true,
						size:true,
						rect:true
					},(data)=>{
						res(data);
					}).exec();
				});	
			},   
			loadTabBar(){
				let tabBar = json.tabList;
				tabBar.forEach(item=>{
					item.loadStatus = 0;
					item.newsList = [];
					item.refresh = false;
				})
				this.tabBar = tabBar;
				this.loadMore()
			},
			async changeTab(e){
				let index = e;
				let width = 0;
				this.tabCurrentIndex = index;
				let nowWidth;
				this.tabBar =await this.getElSize('navBar');
				if(scollTimer){
					clearTimeout(scollTimer);
					scollTimer = false;
				}
				for(let i = 0; i<=index ;i++){
					let result = await this.getElSize('tab'+i);
					width +=result.width;
					if(i == index){
						nowWidth = result.width;
					}			
				}
				scollTimer = setTimeout(() =>{
					let position = width - nowWidth/2 - windowWidth/2;
					if(position > 0){
						this.scrollLeft = width - nowWidth/2 - windowWidth/2;
					}else{
						this.scrollLeft = 0;
					}
					this.tabCurrentIndex = index;
				},300)						
			},
			loadNewsList(type){
				let tabItem = this.tabBar[this.tabCurrentIndex]
				if(type == 'add'){
					if(tabItem.loadStatus == 2){
						return
					}
					tabItem.loadStatus = 1;
				}
				if(type == 'refresh'){
					tabItem.refresh = true
				}
				setTimeout(function(){
					let newsList = json.newsList;
					if(type == 'refresh'){
						tabItem.newsList=[];
					}
					newsList.forEach(item=>{
						item.id = Math.random()*10000;
						tabItem.newsList.push(item)
					})
				},600)
				
			},
			setEnableScroll(enable){
				if(this.enableScroll != enable){
					this.enableScroll = enable;
				}	
			},
			loadMore(){
				this.loadNewsList('add');
			}
		},
		async onLoad(){
			windowWidth = uni.getSystemInfoSync().windowWidth;
			this.loadTabBar();
		}
	}
</script>

<style lang="scss">
	.navBar{
		height: 90upx;
		background-color:white;
		position: relative;
		white-space: nowrap;
		box-shadow: 0 2upx 8upx rgba(0,0,0,.06);
		.nav-item{
			display: inline-block;
			position: relative;
			line-height: 90upx;
			text-align: center;
			color: #303133;
			width: 150upx;
			font-size: 30upx;
			&:after{
				content: '';
				width: 0;
				height: 0;
				border-bottom: 4upx solid #007aff;
				position: absolute;
				left: 50%;
				bottom: 0;
				transform: translateX(-50%);
				transition: .3s;
			}
		};
		.current{
			color: #8A6DE9;
			&:after{
				width:50%
			}
		}
		z-index: 1;
		
	}
	.swiper-container{
		height: 100%;
	}
	view{
		display: flex;
        flex-direction: column;
	}
	page, .content{
		height: 100%;
		overflow: hidden;
	}
	.title{
		font-size: 32upx;
	}
	.img-list3{
		flex-direction: row;
		background-color: #fff;
		width: 690upx;
		height: 150upx;
		position: relative;
	}
	.img-list1{
		flex-direction: row;
		background-color: #fff;
		width: 228upx;
		height: 150upx;
		position: absolute;
		right: 30upx;
	}
	.title1{
		padding-right: 300upx;
		font-size: 32upx;
	}
	.img-item{
		width: 228upx;
		height: 140upx;
	    flex: 1;
		margin-right: 4upx;
	}
	.img-item1{
		flex: 1;
	}
	.title1{
		flex: 2;
	}
	.img{
		width: 100%;
		height: 100%;
	}
	.news-list{
		width: 750upx;
		height: 100%;
	}
	.news-item{
		position: relative;
		padding: 0 30upx;
	}
</style>
