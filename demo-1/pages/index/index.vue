<template>
	<view>
		<view class="content">
			<view class="wrap">
				<view class="u-demo-area">
					<u-toast ref="uToast"></u-toast>
					<u-swiper :height="250" :list="listSwiper" :title="title" :effect3d="effect3d" :indicator-pos="indicatorPos" :mode="mode"
					 :interval="3000" @click="SwiperClick"></u-swiper>
				</view>
			</view>
			<view class="wrap">
				<u-search placeholder="搜索您想要的..." :clearabled="true" v-model="keyword" @custom="moveToSearch(keyword)" @search="moveToSearch(keyword)"></u-search>
			</view>
			<view class="wrap" style="display: flex; flex-direction: row; width: 96vw;margin-left: 2vw;">
				<view style="display: flex; flex-direction: column; text-align: center;width: 16vw;justify-content: center;">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/race.png"></image>
					赛事
				</view>
				<view style="display: flex; flex-direction: column;width: 16vw;justify-content: center;">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/huodong.png"></image>
					活动
				</view>
				<view style="display: flex; flex-direction: column;width: 16vw;justify-content: center;">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/ketang.png"></image>
					课堂
				</view>
				<view style="display: flex; flex-direction: column;width: 16vw;justify-content: center;">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/renzheng.png"></image>
					认证
				</view>
				<view style="display: flex; flex-direction: column;width: 16vw;justify-content: center;"@click="navigateToScenery">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/jinqu.png"></image>
					景区
				</view>
				<view style="display: flex; flex-direction: column;width: 16vw;justify-content: center;"@click="navigateToSmallScenery">
					<image style="width: 12vw; height: 12vw; padding: 1vw;" src="../../static/xiaojin.png"></image>
					小景
				</view>
			</view>
			<view class="index">
			<view>
				<view class="u-tabs-box">
					<u-tabs-swiper activeColor="#f29100" ref="tabs" :list="list" :current="current" @change="change" :is-scroll="false"
					 swiperWidth="750"></u-tabs-swiper>
				</view>
				<swiper class="swiper-box" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish" style="min-height: 100vh;">
					<swiper-item class="swiper-item">
						<scroll-view scroll-y style="height: 100%;width: 100%;">
							<view class="page-box" style="min-height: 100%;">
								<block class="blocks " v-for="(item,index) in list" :key="index">
									<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
										<view style="display: flex; flex-direction: row;">
											<u-avatar :src="item.user1.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.user1.id)"></u-avatar>
											<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.proName1}}</text>
										</view>
										<view class="card" @click="goDetail(item.hq1.id)" style="margin-top: 1vh;">
											<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.pic" mode="aspectFill"></image>
											<view class="card-bottm row">
												<view class="car-title-view row">
													<text class="card-title" style="margin-left: 5vw;">{{item.hq1.title}}</text>
												</view>
												<view><text class="card-num-view">{{item.hq1.CreateData}}</text></view>
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
								<block class="blocks " v-for="(item,index) in list" :key="index">
									<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
										<view style="display: flex; flex-direction: row;">
											<u-avatar :src="item.user1.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.user1.id)"></u-avatar>
											<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.proName1}}</text>
										</view>
										<view class="card" @click="goDetail(item.hq1.id)" style="margin-top: 1vh;">
											<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.pic" mode="aspectFill"></image>
											<view class="card-bottm row">
												<view class="car-title-view row">
													<text class="card-title" style="margin-left: 5vw;">{{item.hq1.title}}</text>
												</view>
												<view><text class="card-num-view">{{item.hq1.CreateData}}</text></view>
												<view @click.stop="share(item)" class="card-share-view"></view>
											</view>
										</view>
									</view>
								</block>
								<text class="loadMore" style="text-align: center; width: 100vw;">已加载到底部</text>
								<u-loadmore :status="status" v-if="view.length != 0"  />
								</view>
							</view>
						</scroll-view>
					</swiper-item>
					
				</swiper>
			</view>
				
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				listSwiper: [{
						image: 'https://cdn.uviewui.com/uview/swiper/1.jpg',
					},
					{
						image: 'https://cdn.uviewui.com/uview/swiper/2.jpg',
					},
					{
						image: 'https://cdn.uviewui.com/uview/swiper/3.jpg',
					}
				],
				tempList: [],
				listTab: [{
					name: '作品'
				}, {
					name: '小景'
				}],
				current: 0,
				title: false,
				pic: 'http://139.196.58.222:80/demo/images/res/table.jpg',
				mode: 'none',
				indicatorPos: 'bottomCenter',
				effect3d: true,
				keyword: '',
				refreshing: false,
				providerList: [],
				list: [],
				fetchPageNum: 1
			}
		},
		onLoad() {
			this.getData();
			uni.getProvider({
				service: 'share',
				success: (e) => {
					let data = []
					for (let i = 0; i < e.provider.length; i++) {
						switch (e.provider[i]) {
							case 'weixin':
								data.push({
									name: '分享到微信好友',
									id: 'weixin'
								})
								data.push({
									name: '分享到微信朋友圈',
									id: 'weixin',
									type: 'WXSenceTimeline'
								})
								break;
							case 'qq':
								data.push({
									name: '分享到QQ',
									id: 'qq'
								})
								break;
							default:
								break;
						}
					}
					this.providerList = data;
				},
				fail: (e) => {
					console.log('获取分享通道失败', e);
				}
			});
		},
		onShow() {
			this.getData();
		},
		onReachBottom() {
			console.log('滑动到页面底部')
			this.getData();
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			 setTimeout(function () {
			            uni.stopPullDownRefresh();
			        }, 800);
			this.getData();
		},
		methods: {
			getData() {
				uni.request({
					url: this.$serverUrl + '/index/',
					success: (ret) => {
						this.tempList = JSON.stringify(ret.data.msg);
						this.tempList.pop();
						if (ret.statusCode !== 200) {
							console.log('失败!');
						} else {
							this.refreshing = true;
							this.list = this.tempList;		
						}
					}
				});
			},
			SwiperClick(index) {
				console.log(index);
			},
			change(index) {
				this.current = index;
			},
			goDetail(e) {
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("URL!", encodeURIComponent(JSON.stringify(e)));
			},
			share(e) {
				if (this.providerList.length === 0) {
					uni.showModal({
						title: '当前环境无分享渠道!',
						showCancel: false
					})
					return;
				}
				let itemList = this.providerList.map(function(value) {
					return value.name
				})
				uni.showActionSheet({
					itemList: itemList,
					success: (res) => {
						uni.share({
							provider: this.providerList[res.tapIndex].id,
							scene: this.providerList[res.tapIndex].type && this.providerList[res.tapIndex].type === 'WXSenceTimeline' ?
								'WXSenceTimeline' : 'WXSceneSession',
							type: 0,
							title: 'uni-app模版',
							summary: e.title,
							imageUrl: e.img_src,
							href: 'https://uniapp.dcloud.io',
							success: (res) => {
								console.log('success:' + JSON.stringify(res));
							},
							fail: (e) => {
								uni.showModal({
									content: e.errMsg,
									showCancel: false
								})
							}
						});
					}
				})
			},

			moveToPersonal(e) {
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
			},
			
			navigateToScenery() {
				uni.navigateTo({
					url: '../scenery/scenery'
				})
			},
			
			navigateToSmallScenery() {
				uni.navigateTo({
					url: '../sscenery/sscenery'
				})
			},
			
			moveToSearch(e){
				uni.navigateTo({
					url:'../search/search?data=' + e
				})
			},
		}
	}
</script>
<style lang="scss" scoped>
	page {}

	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 0;
		width: 100%;
	}

	.blocks {
		margin-top: 2rem;
		box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.25);
		background-color: #fee0d5;
		width: 100vw;
		margin-left: 0;
	}

	.wrap {
		padding: 40rpx;
		width: 100%;
	}
</style>
