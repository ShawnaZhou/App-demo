<template>
	<view style="min-height: 100vh;">
		<u-navbar  :is-back="true" title="">
			<view class="slot-wrap">
				<view class="search-wrap">
					<u-search v-model="keyword" show-action="true" height="56" @search="getData(keyword)" @custom="getData(keyword)" :action-style="{color: '#000'}"></u-search>
				</view>
				<view @click="moveToHot">
					<image @click="moveToHot" src="../../static/hot.png" style="width: 2rem; height: 2rem; position:relative;left: 2vw;"></image>
				</view>
			</view>
		</u-navbar>
		<view>
			<view class="u-tabs-box">
				<u-tabs-swiper activeColor="#f29100" ref="tabs" :list="list" :current="current" @change="change" :is-scroll="false"
				 swiperWidth="750"></u-tabs-swiper>
			</view>
			<swiper class="swiper-box" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish" style="min-height: 100vh;">
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="page-box" style="min-height: 100%;">
							<view v-if="queue.length == 0"style="width: 100vw; text-align: center; margin: 50px 0;">暂无相关发布哦！</view>
							<block class="blocks " v-for="(item,index) in queue" :key="index">
								<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
									<view style="display: flex; flex-direction: row;">
										<u-avatar :src="item.profile.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.profile.id)"></u-avatar>
										<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.profile.nickName}}</text>
									</view>
									<view class="card" @click="goDetail1(item.queue.id)" style="margin-top: 1vh;">
										<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.queue.content" mode="aspectFill"></image>
										<view class="card-bottm row">
											<view class="car-title-view row">
												<text class="card-title" style="margin-left: 5vw;">{{item.queue.title}}</text>
											</view>
											<view><text class="card-num-view">{{item.CreateData}}</text></view>
											<view @click.stop="share(item)" class="card-share-view"></view>
										</view>
									</view>
								</view>
							</block>
							<u-loadmore :status="status" v-if="queue.length != 0"  />
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item" >
					<scroll-view scroll-y style="height: 100%;width: 100%;" @scrolltolower="reachBottom" >
						<view class="page-box" style="min-height: 100vh !important;">
							<view v-if="view.length == 0" style="width: 100vw; text-align: center;margin: 50px 0;">暂无相关景区信息哦！</view>
							<view style="position: relative;  width: 90vw; margin-left: 5vw; border-radius: 10px 10px;margin-top: 2vh; display: flex; flex-direction: column;" >
							<block v-for="(item,index) in view" :key="index">
								<view style="width: 90vw; height: 40vh; box-shadow: 0px 1px grey; margin-top: 2vh; background-color: #fff;" 
								@click="goDetail2(item[0].name)">
									<text style="line-height: 5vh; margin-left: 2vw;">{{item[0].name}}</text>
									<view style="width: 100vw; margin-left: 2vw;">
										<image  v-if="item.id != 0" :src="item[0].pic" style="border-radius: 5px 5px;" mode="aspectFill"></image>
									</view>
								</view>
							</block>
							<u-loadmore :status="status" v-if="view.length != 0"  />
							</view>
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="page-box">
							<view v-if="scenery.length == 0"style="width: 100vw; text-align: center;margin: 50px 0;">暂无相关小景哦！</view>
							 <block class="blocks " v-for="(item,index) in scenery" :key="index">
								<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
									<view style="display: flex; flex-direction: row;">
										<u-avatar :src="item.profile.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.profile.userId)"></u-avatar>
										<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.profile.nickName}}</text>
									</view>
									
									<view class="card" @click="goDetail3(item.scenery.id)" style="margin-top: 1vh;">
										<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.scenery.content" mode="aspectFill"></image>
										<view class="card-bottm row">
											<view class="car-title-view row">
												<text class="card-title" style="margin-left: 5vw;">{{item.scenery.title}}</text>
											</view>
											<view><text class="card-num-view">{{item.scenery.CreateData}}</text></view>
											<view @click.stop="share(item)" class="card-share-view"></view>
										</view>
									</view>
								</view>
							</block> 
							<u-loadmore :status="status" v-if="scenery.length != 0" />
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="page-box" style="min-height: 100%;">
							<view v-if="user.length == 0"style="width: 100vw; text-align: center; margin: 50px 0;">暂无相关用户哦！</view>
							<block class="blocks " v-for="(item,index) in user" :key="index">
								<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
									<view style="display: flex; flex-direction: row;">
										<u-avatar :src="item.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.userId)"></u-avatar>
										<view style="display: flex;flex-direction: column;">
										<text style="line-height: 1rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.nickName}}
										</text>
										<text style="color: #606266;margin-left: 2vw;">{{item.bio}}</text>
										</view>
										<u-button plain="true" ripple-bg-color="#f3f3f3"  @click="subscribe(item.userId)" style="margin-left: 40vw; margin-top: 0.5vh;">关注</u-button>
									</view>
								</view>
							</block>
							<u-loadmore :status="status"  v-if="user.length != 0"/>
						</view>
					</scroll-view>
				</swiper-item>
			</swiper>
		</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				keyword: '',
				queue: [],
				view: [],
				scenery: [],
				user: [],
				status: 'nomore',
				list: [{
					name: '发布'
				}, {
					name: '景区'
				}, {
					name: '小景'
				}, {
					name: '用户'
				}],
				current: 0,
				swiperCurrent: 0,
				current: 0,
				tabsHeight: 0,
				dx: 0,
			}
		},
		onLoad(e) {
			console.log("keyword", e);
			this.keyword = e.data;
			this.getData(this.keyword);
		},
		methods: {
			getData(e) {
				uni.request({
					url: this.$serverUrl + '/search',
					method: 'POST',
					data: {
						q: e,
					},
					success: (res) => {
						console.log("e:",e);
						console.log("res", res.data);
						this.queue = res.data.queue;
						this.view = res.data.view;
						this.user = res.data.profile;
						this.scenery = [];
						for(var i = 0; i< this.queue.length; i++){
							console.log("i",i);
							this.queue[i].queue.content = this.queue[i].queue.content.split("|");
							this.queue[i].queue.content.pop();
						}
						console.log("queue",this.queue);
					}
				})
			},
			goDetail1(e) {
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("URL!", encodeURIComponent(JSON.stringify(e)));
			},
			goDetail2(e){
				uni.navigateTo({
					url: '../sceneryDetail/sceneryDetail?name=' + JSON.stringify(e)
					// ?data='+ encodeURIComponent(JSON.stringify(e))  + JSON.stringify(e)
					});
				console.log("nameInUrl", JSON.stringify(e));
			},
			goDetail3(e) {
				uni.navigateTo({
					url: '../smallScenery/smallScenery?data='+e,
					success: (request) => {
						console.log("resNav",request);
					},
					fail: (err) => {
						console.log("err:",err);
					}
				});
				console.log("URL!", e);
			},
			subscribe(e){
				uni.request({
					url: this.$serverUrl + '/followe',
					method:'POST',
					data:{
						userId: e
					},
					success: (res) => {
						uni.showToast({
							icon:'none',
							title:'关注成功'
						});
						console.log(e);
						console.log("res", res.data);
					}
				})
			},
			moveToHot(){
				uni.navigateTo({
					url: '../hot/hot',
					success: (res) => {
						console.log(res);
					},fail: (err) => {
						console.log("err",err);
					}
				})
			},
			moveToPersonal(e) {
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
			},
			change(index) {
				this.swiperCurrent = index;
			},
			transition({
				detail: {
					dx
				}
			}) {
				this.$refs.tabs.setDx(dx);
			},
			animationfinish({
				detail: {
					current
				}
			}) {
				this.$refs.tabs.setFinishCurrent(current);
				this.swiperCurrent = current;
				this.current = current;
			}
		}
	}
</script>

<style>
	.slot-wrap {
		display: flex;
		align-items: center;
		flex: 1;
		padding: 0 65rpx;
	}
</style>
