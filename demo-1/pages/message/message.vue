<template>
	<view style="display: flex;flex-direction: column; width: 100vw;">
		<block v-for="item in list">
		<!-- <view style="display: flex;flex-direction: column;width: 100vw; border: 1px solid #ebecf6;margin-top: 2px;" @click="moveTo(item.mess.id)">
			<view style="display: flex; flex-direction: row;">
			<u-avatar :size="100" :src="item.user.head_url" style="border-color: #000000;" v-if="true"></u-avatar>
			<text style="line-height: 1.5rem;">{{item.nickName}}</text>
			</view>
			<text style="margin-left: 100rpx; background-color: #000000;  color: white;
			width: 80vw;border-radius: 0px 10px 10px 10px;">{{item.mess.content}}</text>
			<text style="width: 100vw;height: 1rem; float: right;"> {{item.mess.CreateDate}}</text>
			
		</view> -->
		<view style="display: flex;flex-direction: row;width: 100vw;margin-top: 1vh; padding-bottom: 1vh; border-bottom: 1rpx solid #C8C9CC;" @click="moveTo(item.user.id)">
			<u-avatar :size="100" :src="item.user.head_url" style="border-color: #000000;" v-if="true"></u-avatar>
			<view style="display: flex;flex-direction: column;">
				<text style="line-height: 1.5rem;">{{item.nickName}}</text>
				<text style="margin-left: 5rpx;  color: grey;
				width: 80vw; height: 1rem; overflow: hidden;">{{item.mess.content}}</text>
			</view>
			<text style="height: 1rem;margin-left: -20vw; margin-top: 1px;"> {{item.mess.CreateDate}}</text>
		</view>
		</block>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				userId: ''
			}
		},
		onLoad() {
			this.getData();
		},
		onShow() {
			this.getData();
		},
		methods: {
			getData(){
				uni.request({
					url: this.$serverUrl + '/message/',
					success: (reg) => {
						console.log(reg.data);
						this.list = reg.data.msg;	
						console.log("list1",this.list);
					},fail: (err) => {
						uni.showModal({
							content:'网络异常',
							title: '提示'
						});
						console.log("err:",err);
					}
				}),
				console.log("list2",this.list);
			},
			moveTo(e){
				// e = this.userId + "-" + e;
				console.log("canshu",e);
				uni.navigateTo({
					url: '../mseeageDetail/mseeageDetail?data=' + JSON.stringify(e)
				})
				console.log("msID",encodeURIComponent(JSON.stringify(e)));
			}
		}
	}
</script>

<style>

</style>
