<template>
	<view>
		<view>
			<view class="u-tabs-box">
				<u-tabs-swiper activeColor="#f29100" ref="tabs" :list="list" :current="current" @change="change" :is-scroll="false"
				 swiperWidth="750"></u-tabs-swiper>
			</view>
			<swiper class="swiper-box" :current="swiperCurrent" @transition="transition" @animationfinish="animationfinish" style="min-height: 100vh;">
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="page-box" style="min-height: 100%;">
							<view v-if="queue.length == 0"style="width: 100vw; text-align: center; margin: 50px 0;">暂未收藏发布哦！</view>
							<block class="blocks " v-for="(item,index) in queue" :key="index">
								<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
									<view style="display: flex; flex-direction: row;">
										<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.proName1}}</text>
									</view>
							
									<view class="card" @click="goDetail1(item.id)" style="margin-top: 1vh;">
										<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.content" mode="aspectFill"></image>
										<view class="card-bottm row">
											<view class="car-title-view row">
												<text class="card-title" style="margin-left: 5vw;">{{item.title}}</text>
											</view>
											<view><text class="card-num-view">{{item.CreateData}}</text></view>
											<view @click.stop="share(item)" class="card-share-view"></view>
										</view>
									</view>
									<u-line color="grey" margin="10px 0" />
								</view>
							</block>
						</view>
					</scroll-view>
				</swiper-item>
				<swiper-item class="swiper-item">
					<scroll-view scroll-y style="height: 100%;width: 100%;">
						<view class="page-box">
							<view v-if="scenery.length == 0"style="width: 100vw; text-align: center;margin: 50px 0;">暂未收藏小景哦！</view>
							<block class="blocks " v-for="(item,index) in scenery" :key="index">
								<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
<!-- 									<view style="display: flex; flex-direction: row;">
										<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.profile.nickName}}</text>
									</view> -->
									
									<view class="card" @click="goDetail2(item.id)" style="margin-top: 1vh;">
										<image class="card-img" style="width: 100vw; height: 60vh;" :src="item.content" mode="aspectFill"></image>
										<view class="card-bottm row">
											<view class="car-title-view row">
												<text class="card-title" style="margin-left: 5vw;">{{item.title}}</text>
											
											</view>
											<view><text class="card-num-view">{{item.CreateDate}}</text></view>
										</view>
										<u-line color="grey" margin="10px 0" />
									</view>
								</view>
							</block> 
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
				queue: [],
				scenery: [],
				list: [{
					name: '发布'
				},  {
					name: '小景'
				}],
				current: 0,
				swiperCurrent: 0,
				current: 0,
				tabsHeight: 0,
				dx: 0,
			}
		},
		onLoad() {
			this.getData1();
		},
		methods: {
			getData1(){
				uni.request({
					url: this.$serverUrl + '/colList' ,
					success: (res) => {
						console.log("res:",res.data);
						this.queue = res.data.list;
						console.log("queue",this.queue);
						for(var i=0;i< this.queue.length ; i++){
							this.queue[i].content = this.queue[i].content.split("|");
							this.queue[i].content.pop();
						}
						console.log("img",this.queue);
					}
				})
			},
			getData2(){
				uni.request({
					url: this.$serverUrl + '/sceneryList' ,
					success: (res) => {
						console.log("res.data",res.data);
						if(res.data.code == 0){
							this.scenery = res.data.list;
							var index;
							for (var i=0 ;i< this.scenery.length;i++){
								index= this.scenery[i].content.indexOf("|");
								console.log("index",index+i);
								this.scenery[i].content = res.data.list[i].content.substr(0,index);
								console.log("newList",this.scenery);
							}
							console.log("trueList",this.scenery);
						}
						else{
							this.$refs.uToast.show({
								title: '请求异常',
								type: 'error',
							});
						}
					},
					fail: (err) => {
						this.$refs.uToast.show({
							title: '网络异常',
							type: 'error',
						});
					}
				})
			},
			change(index) {
				this.swiperCurrent = index;
				console.log("index",index);
				if(index == 0){
					this.getData1();
				}else if(index == 1){
					this.getData2();
				}
			},
			goDetail1(e) {
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("URL!", encodeURIComponent(JSON.stringify(e)));
			},
			goDetail2(e) {
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
				if(current == 0){
					this.getData1();
				}else if(current == 1){
					this.getData2();
				}
			}
		}
	}
</script>

<style>

</style>
