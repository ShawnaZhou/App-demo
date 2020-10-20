<template>
	<view>
		<view style="margin-top: 2vh;">
			<block v-for="(item,index) in list">
				<view v-if="true" style="width: 100vw; border-bottom: 1px solid #d1d3ea; margin: 2vh 0;" >
				<u-avatar :size="100" v-if="true" style="margin-left: 3vw;" :src="item.profile.head_url" @click="moveToPersonal(item.profile.userId)"></u-avatar>
				<view @click="moveToDetail(item.news.datajson.hotqueueId)">
				<!-- <image mode="aspectFill" style="width: 100vw;" :src="item.profile.head_url"></image> -->
				<text>{{item.descri}}</text>
				<text style="font-weight: bold; margin-left: 2vw; height: 4vh; width: 60vw;">{{item.profile.nickName}}发布的作品</text>
				<br>
				<text style="width: 100vw; margin-left: 25vw; height: 4vh;">{{item.queueTitle}}</text>
				</view>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userInfo: [],
				userId: '',
				news: [],
				descri: [],
				list: []
			}
		},
		onShow() {
			this.getData(this.userId);
		},
		onLoad() {
			this.userInfo = uni.getStorageSync('userinfo');
			this.userId = this.userInfo.userId;
			console.log(this.userId);
			this.getData(this.userId);
		},
		methods: {
			getData(e){
				uni.request({
					url:  this.$serverUrl + '/news/pull',
					method: 'POST',
					success: (reg) => {
						console.log(reg.data);
						this.list = reg.data.msg;
						console.log("list:",this.list);
					}
				})
			},
			moveToPersonal(e){
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("thisUser",encodeURIComponent(JSON.stringify(e)));
			},
			moveToDetail(e) {
				uni.navigateTo({
					url: '../detail/detail?data=' + e
				});
				console.log("URL!", e);
			},
		}
	}
</script>

<style>

</style>
