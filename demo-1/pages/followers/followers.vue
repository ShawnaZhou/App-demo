<template>
	<view>
		<view style="margin-left: 5vw;">
			<text style="font-weight: bold; font-size: 1.5rem;">关注的人</text>
		</view>
		<view style="display: flex; flex-direction: column;">
			<text v-if="list.length == 0"style="width: 90vw; margin-left: 5vw;font-size: 1.1rem;">当前暂无关注哦，快去发现和你志趣相投的人吧！</text>
			<block v-for="item in list">
			<view style="display: flex; flex-direction: row; margin-top: 1vh;">
				<u-avatar  :src="item.user.head_url" style="margin-left: 5vw;" @click="moveToPersonal(item.user.id)"></u-avatar>
				<text style="line-height: 50px; margin-left: 2vw; width: 40vw;">{{item.profile.nickName}}</text>
				<u-button plain="true" ripple-bg-color="#e31a1a" ripple="true" style="color: #894242; border: 2px solid #894242;" @click="disSubscribe(item.user.id)">取消关注</u-button>
			</view>
			<u-line color="grey" style="margin-top: 1.1vh; "/>
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
					url: this.$serverUrl + '/followees/'+ e,
					success: (res) => {
						if(res.data.code == 0){
							this.list = res.data.followees;
							console.log("list",this.list);
						}
						console.log("res",res.data);
						console.log("url",this.$serverUrl + '/followees/'+ e);
					},
					fail: (err) => {
						console.log(err);
					},
				});
			},
			moveToPersonal(e) {
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("thisUser", encodeURIComponent(JSON.stringify(e)));
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
