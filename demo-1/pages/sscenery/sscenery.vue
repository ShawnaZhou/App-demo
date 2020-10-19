<template>
	<view>
		<u-toast ref="uToast"></u-toast>
		<view class="index">
		
			 <block class="blocks " v-for="(item,index) in list" :key="index">
				<view class="blocks" style="background-color: #fbf8f8; padding-top: 20rpx; padding-bottom: 40rpx;">
					<view style="display: flex; flex-direction: row;">
						<u-avatar :src="item.profile.head_url" style="margin-left: 5vw;" mode="circle" @click="moveToPersonal(item.profile.userId)"></u-avatar>
						<text style="line-height: 2rem; margin-top: 0.1rem;margin-left: 2vw;">{{item.profile.nickName}}</text>
					</view>
		
					<view class="card" @click="goDetail(item.scenery.id)" style="margin-top: 1vh;">
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
			<text class="loadMore" style="text-align: center; width: 100vw;">已加载到底部</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[],
				imgList: [],
				contentList: [],
			}
		},
		onLoad() {
			this.getData();
		},
		methods: {
			getData(){
			 uni.request({
			 	url:this.$serverUrl + '/scenery/',
				success: (res) => {
					console.log("res.data",res.data.msg);
					if(res.data.code == 0){
						this.list = res.data.msg;
						var index;
						for (var i=0 ;i< this.list.length;i++){
							index= this.list[i].scenery.content.indexOf("|");
							console.log("index",index+i);
							this.list[i].scenery.content = res.data.msg[i].scenery.content.substr(0,index);
							console.log("newList",this.list);
						}
						console.log("trueList",this.list);
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
			goDetail(e) {
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
			moveToPersonal(e) {
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("thisUser", encodeURIComponent(JSON.stringify(e)));
			},
		}
	}
</script>

<style>

</style>
