<template>
	<view>
		<image :src="personalInfo.profile.head_url" style="position: absolute; width: 100vw; height: 25vh;z-index: -1;top: 0;left: 0;"></image>
		<view style="justify-content: center; display: flex;flex-direction: column;
		width: 100vw; min-height: 30vh; z-index: 100;margin-top: 15vh;
		">
			<u-avatar :size="200" style="margin-left: 8vw;z-index: 1000;" :src="personalInfo.profile.head_url" v-if="true"></u-avatar>
			<text style="width: 100vw; height: 3vh; margin-left: 8vw; font-weight: 800;font-size: 1.3rem;">{{personalInfo.profile.nickName}}</text>
			<text style="width: 100vw; height: 3vh; margin-left: 8vw; margin-top: 2vh;">{{personalInfo.profile.birthDay}}</text>
			<text style="width: 100vw; height: 3vh; margin-left: 8vw;">{{personalInfo.profile.bio}}</text>
			<text style="width: 100vw; height: 8vh; border-bottom: 1px solid #d1d3ea ;"></text>
			<view style="background-color: #000000;color: white; line-height: 5vh; width: 15vw; height: 5vh; text-align: center; border-radius: 5px 5px;
			margin-top: -21vh; margin-left: 75vw;" v-if="personalInfo.flag == 1" @click="subscribe(personalInfo.profile.userId)">关注</view>
			<view style="background-color: #000000;color: white; 
			line-height: 5vh; width: 15vw; height: 5vh; text-align: center; border-radius: 5px 5px;
			margin-top: -21vh; margin-left: 75vw;" v-else-if="personalInfo.flag == 0" @click="disSubscribe(personalInfo.profile.userId)">已关注</view>
		</view>
		<view style="margin-top: 15vh;">
			<block v-for="(item,index) in personalInfo.vl">
				<view v-if="true" style="width: 100vw; border-bottom: 1px solid #d1d3ea; margin-top: 2vh;" @click="moveTo(item.hq.id)">
				<u-avatar :size="100" v-if="true" style="margin-left: 3vw;" :src="personalInfo.profile.head_url"></u-avatar>
				<image mode="aspectFill" style="width: 100vw;" :src="item.pic"></image>
				<text style="font-weight: bold;">{{personalInfo.profile.nickName}}:</text>
				<text style="width: 100vw; margin-left: 3vw;">{{item.hq.title}}</text>
				</view>
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				userid: "",
				personalInfo: [],
				imageList: [],
				page: ''
			}
		},
		onLoad(e) {
			this.userid = e;
			console.log("e:",e);
			console.log("id:",this.userid);
			this.getData(e);
			this.page = e;
		},
		methods: {
			getData(e){
				uni.request({
					url: this.$serverUrl + '/profile/' + e.data,
					method: "GET",
					success: (reg) => {
						console.log(e);
						console.log("reg.data:",reg.data);
						console.log(reg.statusCode);
						this.personalInfo = reg.data;
						console.log("user:", this.personalInfo);	
					}
				})
			},
			moveTo(e){
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				});
				console.log("URL!",encodeURIComponent(JSON.stringify(e)));
			},
			subscribe(e){
				uni.request({
					url: this.$serverUrl + '/followe',
					method:'POST',
					data:{
						userId: this.personalInfo.profile.userId
					},
					success: (res) => {
						uni.showToast({
							icon:'none',
							title:'关注成功'
						});
						console.log(e);
						console.log("res", res.data);
						this.getData(this.userid);
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
						this.getData(this.userid);
					}
				})
			},
		}
	}
</script>

<style>

</style>
