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
			<u-avatar :size="100" :src="item.user.head_url" style="border-color: #000000;margin-left: 0.5rem;" v-if="true">
			</u-avatar>
			<view style="display: flex;flex-direction: column; margin-left: 0.5rem;">
				<text style="line-height: 1.5rem;">{{item.nickName}}</text>
				<text style="margin-left: 5rpx;  color: grey;
				width: 80vw; height: 1.2rem; overflow: hidden;">{{item.mess.content}}</text>
			</view>
			<text style="height: 1.2rem;margin-left: -20vw; margin-top: 1px; overflow: hidden;"> {{item.mess.CreateDate}}</text>
			<text v-if="item.mess.hasRead!=0" style="height: 1.3rem;margin-left: -20vw; margin-top: 2rem;line-height: 1.3rem; text-align: center;color: #FFFFFF; overflow: hidden;width: 1.3rem;border-radius: 50% 50%;background-color: #F76260;"> {{item.mess.hasRead}}</text>
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
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			 setTimeout(function () {
			            uni.stopPullDownRefresh();
			        }, 600);
			this.getData();
		},
		methods: {
			getData(){
				uni.request({
					url: this.$serverUrl + '/message/',
					success: (reg) => {
						let temp = reg.data.msg;
						for (var a=0 ; a<temp.length ; a++){
							reg.data.msg[a].mess.createDate.substr(0,10);
						}
						this.list = reg.data.msg;
						console.log(this.list);
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
