<template>
	<view>
		<view style="margin-left: 5vw;">
			<text style="font-weight: bold; font-size: 1.5rem;">粉丝</text>
		</view>
		<view style="display: flex; flex-direction: column;">
			<text v-if="list.length == 0"style="width: 90vw; margin-left: 5vw;font-size: 1.1rem;">当前暂无粉丝哦，请多发布些作品吧！</text>
			<block v-for="item in list">
				
			<view style="display: flex; flex-direction: row; padding-top: 0.5rem;">
				<u-avatar  :src="item.user.head_url" style="margin-left: 5vw;"></u-avatar>
				<text style="line-height: 50px; margin-left: 2vw; width: 40vw;">{{item.profile.nickName}}</text>
				<u-button plain="true" ripple-bg-color="#f3f3f3" v-if="item.isfollowed == false" @click="subscribe(item.user.id)" style="margin-left: 10vw;">关注</u-button>
				<u-button plain="true" ripple-bg-color="#e31a1a" v-if="item.isfollowed == true" ripple="true" style="color: #894242; border: 2px solid #894242;" @click="disSubscribe(item.user.id)">取消关注</u-button>
			</view>
			<u-line color="grey" />
			</block>
		</view>
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
		onLoad(e){
			console.log("userId",e.data);
			this.userId = e.data;
			console.log(this.userId);
			this.getData(this.userId);
		},
		methods: {
			getData(e){
				console.log("e",e);
				uni.request({
					url: this.$serverUrl + '/fans/'+ e,
					success: (res) => {
						if(res.data.code == 0){
							this.list = res.data.fans;
							console.log("list",this.list);
						}
						console.log("res",res.data);
						console.log("url",this.$serverUrl + '/fans/'+ e);
					},
					fail: (err) => {
						console.log(err);
					},
				});
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
						this.getData(this.userId);
					}
				})
			},
			disSubscribe(e){
				uni.request({
					url: this.$serverUrl + '/unfollow',
					method:'POST',
					data:{
						userId: e
					},
					success: (res) => {
						uni.showToast({
							icon:'none',
							title:'取消关注成功'
						});
						console.log(e);
						console.log("res", res.data);
						this.getData(this.userId);
					}
				})
			},
		}
	}
</script>

<style>

</style>
