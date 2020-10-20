<template>
	<view>
		<view class="u-flex-1" style="width: 100vw;">
					<image :src="backImage" mode="aspectFill" style="width: 100vw; position: relative;" ></image>
					<image  src="../../static/changeback.png" style="width: 45px; height: 45px; position: absolute; z-index: 1000; left: 80vw; top: 25vh;" @click="changeBackImage"></image>
		</view>
		<view class="u-flex user-box u-p-l-30 u-p-r-20 u-p-b-30" style="border-radius: 20px 20px; margin-top: 1vh; z-index: 1000; ">
			<view class="u-m-r-10">
				<u-avatar :src="head_url" size="140" @click="moveToPersonal(userId)"></u-avatar>
				<view style="width: 4rem; height: 1.5rem; background-color: #000000; color: white; text-align: center;border-radius: 5px 5px;"
				@click="changeAvator"
				>修改头像</view>
			</view>
			<view class="u-m-r-20" style="display: flex; flex-direction: column;">
				<image v-if="level == 3" src="../../static/city.png" style="width: 25px; height: 25px;"></image>
				<image v-if="level == 2" src="../../static/town.png" style="width: 25px; height: 25px;"></image>
				<image v-if="level == 4" src="../../static/province.png" style="width: 25px; height: 25px;"></image>
				 <image v-if="level == 6" src="../../static/controller.png" style="width: 25px; height: 25px;"></image> 
			</view>
			<view class="u-flex-2">
				<view class="u-font-18 u-p-b-20">{{nickname}}</view>
				<view class="u-font-14 u-tips-color">{{bio}}</view>
				<view style="display: flex;flex-direction: row;">
					<text> 段位：</text>
					<image :src="levelImg" style="width: 38px; height: 18px; padding-top: 3px;">
					</image>
				</view>
				<u-line-progress :percent="this.score1" show-percent="true" striped="true" striped-active="true" :round="false" active-color="#ff9900">
				</u-line-progress>

			</view>
			<view class="u-flex-1">
				<view class="u-font-18 u-p-b-20">
					
				</view>
			</view>
			<view class="u-flex-1" style="display: flex;flex-direction: column;">
				<text @click="moveToFollow(nowUser)">关注：{{followcount}}</text>
				<text @click="moveToFans(nowUser)">粉丝：{{fanscount}}</text>
				
			</view>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="chat" title="私信" @click = "moveToMessage">
				</u-cell-item>
			</u-cell-group>
		</view>
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="account" title="个人信息" @click="moveToPersonalInf(userId)"></u-cell-item>
				<u-cell-item icon="photo" title="收藏" @click = "moveToCollect"></u-cell-item>
				<u-cell-item icon="coupon" title="相册" @click = "moveToPhoto(userId)"></u-cell-item>
				<u-cell-item icon="rmb-circle-fill" title="钱包" @click = "moveToMoner(userId)"></u-cell-item>
			</u-cell-group>
		</view>
		
		<view class="u-m-t-20">
			<u-cell-group>
				<u-cell-item icon="setting" @click="moveToInvite" title="邀请码"></u-cell-item>
			</u-cell-group>
		</view>
		<u-button style="margin-top: 2vh;" type="error" @click="logOut">退出登录</u-button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				levelImg:'../../static/xinji.png',
				nickname: '',
				bio: '',
				head_url: '',
				userId: '',
				rank: [],
				score: '',
				score1: '',
				show:true,
				fanscount: '',
				followcount: '',
				level: '',
				backImage: '',
				nowUser: ''
			}
		},
		onLoad() {
			this.getData();
			var loginRes = this.checkLogin('../index/index', '2');
						if(!loginRes){
							return false;
							uni.showModal({
							    title: '未登录',
							    content: '您未登录，需要登录后才能继续',
							    /**
							     * 如果需要强制登录，不显示取消按钮
							     */
							    showCancel: false,
							    success: (res) => {
							        if (res.confirm) {
										/**
										 * 如果需要强制登录，使用reLaunch方式
										 */
							            if (this.forcedLogin) {
							                uni.reLaunch({
							                    url: '../login/login'
										});
							            }
							        }
							    }
							});
						}else{
							console.log(loginRes);		
							this.userInfo = uni.getStorageSync('userinfo');
							this.userId = this.userInfo.userId;
							console.log('uurrrll!',this.head_url);
						}
		},
		onShow() {
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
			moveToMessage(){
				uni.navigateTo({
					url:'../message/message'
				})
			},
			moveToLevel(){
				console.log("mpve1");
			},
			moveToPersonal(e) {
				uni.navigateTo({
					url: '../personal/personal?data=' + encodeURIComponent(JSON.stringify(e))
				});
			},
			moveToPhoto(e){
				uni.navigateTo({
					url: '../photograph/photograph?data=' + e
				});
			},
			moveToMoner(e){
				uni.navigateTo({
					url: '../money/money?data=' + e
				});
			},
			changeBackImage(){
				uni.chooseImage({
				    success: (chooseImageRes) => {
				        const tempFilePaths = chooseImageRes.tempFilePaths;
				        uni.uploadFile({
				            url: this.$serverUrl + '/back/', //仅为示例，非真实的接口地址
				            filePath: tempFilePaths[0],
				            name: 'file1',
				            success: (uploadFileRes) => {
								console.log("上传成功");
				                console.log(uploadFileRes);
				            },fail: (err) => {
				            	console.log("failChangeBack",err);
				            }
				        });
				    }
				});
			},
			getData(){
				uni.request({
					url: this.$serverUrl + '/profile/' + this.userId,
					method:'GET',
					success: (res) => {
						console.log("url:", this.$serverUrl + '/profile/' + this.userId);
						console.log("apple",res.data);
						console.log("resPer:",res.data.rank);
						this.backImage = res.data.back.pic;
						this.level = res.data.curuser.level;
						this.fanscount = res.data.fanscount;
						this.followcount = res.data.followeecount;
						this.nowUser = res.data.curuser.id;
						this.score = res.data.rank.score;
						this.head_url = res.data.curuser.head_url;
						this.nickname = res.data.profile.nickName;
						this.bio = res.data.profile.bio;
						console.log("score:",this.score);
						if(this.score <= 50){
							this.levelImg = '../../static/qt.png';
							this.score1 = this.score * 2;
						}
						else if(this.score > 50 && this.score <= 100){
							this.levelImg = '../../static/qt.png';
							this.score1 = (this.score - 50) * 2;
						}
						else if(this.score > 100 && this.score<=200){
							this.levelImg = '../../static/by.png';
							this.score1 = (this.score - 100);
						}
						else if(this.score > 200 && this.score<=300){
							this.levelImg = '../../static/hj.png';
							this.score1 = (this.score - 200);
						}
						else if(this.score > 300 && this.score<=450){
							this.levelImg = '../../static/zs.png';
							this.score1 = (this.score - 300)/3*2;
							this.score1= parseInt(this.score1);
							console.log("score:",this.score1);
						}
						else if(this.score > 450 && this.score<=750){
							this.levelImg = '../../static/xy.png';
							this.score1 = (this.score - 450) / 3;
							this.score1= parseInt(this.score1);
							console.log("score:",this.score1);
						}
						else if(this.score > 750 && this.score<=1500 ){
							this.levelImg = '../../static/wz.png';
							this.score1 = 100;
						}
						else if(this.score >= 1500){
							this.levelImg = '../../static/kings.png';
							this.score1 = 100;
						}
						else{
							this.score1 = this.score * 2;
						}
						console.log("score1:",this.score1);
					}
				})
			},
			changeAvator(){
				uni.chooseImage({
				    success: (chooseImageRes) => {
				        const tempFilePaths = chooseImageRes.tempFilePaths;
				        uni.uploadFile({
				            url: this.$serverUrl + '/profile/head_url', //仅为示例，非真实的接口地址
							method: 'PUT',
				            filePath: tempFilePaths[0],
				            name: 'file',
				            success: (uploadFileRes) => {
				                console.log(uploadFileRes.data);
				            }
				        });
				    }
				});
			},
			logOut(){
				uni.showToast({
					icon: 'loading'
				});
				uni.request({
					url:this.$serverUrl + '/logout',
					method:'POST',
					success: (res) => {
						uni.removeStorage({
						    key: 'userinfo',
						    success: function (res) {
								uni.showToast({
									title:'登出成功',
									icon:'none'
								});
								console.log("logOutSuccess");
								uni.redirectTo({
									url: '../login/login'
								})
						    }
						});
					}
				})
			},
			moveToFollow(e){
				uni.navigateTo({
					url: '../followers/followers?data=' + e,
					success: (res) => {
						console.log(res);
					},
					fail: (err) => {
						console.log(err);
					}
				});
				console.log("follow",e);
			},
			moveToFans(e){
				uni.navigateTo({
					url: '../fans/fans?data=' + e,
				});
				console.log("Fans",e);
			},
			moveToPersonalInf(e){
				uni.navigateTo({
					url: '../personalInformation/personalInformation?data=' +e,
				})
			},
			moveToCollect(){
				uni.navigateTo({
					url: '../collect/collect'
				})
			},
			moveToInvite(){
				uni.navigateTo({
					url: '../invite/invite'
				})
			},
			moveToInvite(){
				uni.navigateTo({
					url: '../invite/invite'
				})
			},
		}
	}
</script>

<style lang="scss">
page{
	// background-color: #ededed;
}
.user-box{
	background-color: #fff;
}
</style>
