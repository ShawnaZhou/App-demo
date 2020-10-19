<template>
	<view class="index">
				<u-toast ref="uToast" />
			<view style="display: flex; flex-direction: column;">
				<view style="display: flex;flex-direction: row;margin-top: 5vh; width: 100vw;">
					<u-avatar :src="data.queueUser.head_url" v-if="data.code == 0" style="margin-left: 5vw;" :size="100" @click="moveToPersonal(item)"></u-avatar>
					<text style="line-height: 15vw; margin-left: 5vw;width: 60vw;">{{data.nickName}}</text>
					<u-icon name="trash-fill" size="40" color="black" style="margin-top:0;margin-right: 5vw; float: right;"
						v-if="userId == data.queueUser.id" @click="deleteMain(data.hotqueue.id)"
					></u-icon>
				</view> 
				<block v-for="item in imgList">
				<image :src="item" v-if="true" style="width: 100vw; margin-top: 5vh; height: 60vh;" mode="aspectFill" ></image>
				</block>
				<view style="display: flex; flex-direction: row; width: 100vw; height: 10vh;">
					<view class="download" v-if="data.flag == false" style="margin-left: 5vw; line-height: 5vh; width: 4em; text-align: center; background-color: #000000;color: white; height: 5vh; border-radius: 5px 5px; margin-top: 2.5vh;" @click="loveit">收藏</view>
					<view class="download" v-else-if="data.flag == true" style="margin-left: 5vw; line-height: 5vh; width: 5em; text-align: center; background-color: #000000;color: white; height: 5vh; border-radius: 5px 5px; margin-top: 2.5vh;" @click="unloveit">已收藏</view>
					<text style="line-height: 10vh; width: 5vw; margin-left: 40vw;" v-if="true">{{data.queueLikeCount}}</text>
					<u-icon name="heart" size="40"  color="grey" @click="likeItMain" v-if="data.queueLikeStatus == 0"></u-icon>
					<u-icon name="heart-fill" size="40"  color="red" @click="cancelLikeMain" v-if="data.queueLikeStatus == 1"></u-icon>
					<text style="line-height: 10vh; width: 5vw;margin-left: 5vw;">{{data.hotqueue.commentCount}}</text>
					<u-icon name="coupon-fill" size="40" color="black" @click="commentMain" v-if="data.queueLikeStatus == 0 || data.queueLikeStatus == 1"></u-icon>
					<!-- <view style=" line-height: 5vh; width: 4em; text-align: center; background-color: #000000;color: white; height: 5vh; border-radius: 5px 5px; margin-top: 2.5vh;" @click="commentMain">评论</view> -->
				</view>
				<view style="width: 100vw; min-height: 9vh; background-color: #f0f5fe;" v-if="weatherComment==true">
					<u-field
								v-model="commentContent"
								placeholder="请输入评论内容"
								type="textarea"
								label-width="0"
							>
							<u-button size="mini" slot="right" type="success" @tap="submitComment">发布</u-button>
							</u-field>
				</view>
				<text style="line-height: 5vh;margin-left: 30vw;">发布时间：{{data.hotqueue.CreateDate}}</text>
				<block v-for="item in data.comments" key= "item">
					<view style="display: flex;flex-direction: column; width: 100vw; padding-bottom: 5vh;">
						<view style="display: flex;flex-direction: row; margin-left: 5vw; width: 90vw;">
						<u-avatar :src="item.user.head_url" size="80" style="margin-left: 0;" img-mode="circle"></u-avatar>
						<text style="line-height: 100rpx; margin-left: 2vw; color: grey;">{{item.user.name}}</text>
						</view>
						<view style="display: flex;flex-direction: column; width: 90vw; margin-left: 5vw; ">
							<text style="margin-left: 90rpx;">{{item.comment.content}}</text>
							<view style="display: flex; flex-direction: row; float: right;margin-top: 5vh;">
								<text style="margin-left: 10vw;">发布日期：{{item._cretedate}}</text>
								<text  style="margin-left: 5vw;">{{item.CommentlikeCount}}</text>
								<u-icon name="heart" size="40" style="margin-left: 1vw;" color="grey" @click="likeComment(item.comment.id)" v-if="item.commentStatus == 0"></u-icon>
								<u-icon name="heart-fill" size="40" color="red" @click="cancelLikeComment(item.comment.id)" v-else-if="item.commentStatus == 1"></u-icon>
								<u-icon name="trash-fill" size="40" color="grey" style="margin-top: -0.3vh;margin-left: 1vw;"
									v-if="item.com_nickName.userId == userId"
									@click="deletComment(item.comment.id)"
								></u-icon>
						
							</view>
						</view>
					</view>
				</block>
			</view>
	</view>
</template>

<script>
	import FontAwesome from '@/components/Am-FontAwesome/index.vue'
	export default {
		data() {
			return {
				 components: {
				        FontAwesome
				    },
				commentContent: '',
				weatherComment: false,
				imgShow: false,
				index: 0,
				showBtn: false,
				screenHeight: 0,
				imgLength: 0,
				imgList:[],
				providerList: [],
				data: [],
				detailDec: "",
				userId: ''
			}
		},
		onLoad(e) {
			// #ifdef APP-PLUS
			if (plus.os.name === 'Android') {
				this.showBtn = true;
			}
			// #endif
			this.screenHeight = uni.getSystemInfoSync().windowHeight;
			this.detailDec = e;
			// let data = JSON.parse(decodeURIComponent(e));
			// let data = e;
			// console.log("ddaattaa:",data);
			// this.imgLength = data.img_num;
			// this.data.push(data.img_src);
			this.getData(e);
			this.userInfo = uni.getStorageSync('userinfo');
			console.log(this.userInfo);
			this.userId = this.userInfo.userId;
			uni.setNavigationBarTitle({
				// title: "1/" + this.imgLength
			});
			// 获取分享通道
			uni.getProvider({
				service: "share",
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
					console.log("获取登录通道失败", e);
				}
			});
		},
		onShareAppMessage() {
			return {
				title: '欢迎使用uni-app看图模板',
				path: '/pages/detail/detail?data=' + this.detailDec,
				imageUrl: this.data[this.index]
			}
		},
		onNavigationBarButtonTap(e) {
			if (e.index === 0) {
				// #ifdef APP-PLUS
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
							summary: '欢迎使用uni-app模版',
							imageUrl: this.data[this.index],
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
				});
				// #endif
				// #ifdef H5
				this.collect();
				// #endif
			}
		},
		methods: {
			commentMain(){
				this.weatherComment = !this.weatherComment;
			},
			submitComment(){
				if(this.commentContent == ''){
					uni.showToast({
						title: '请输入评论内容',
						icon: 'none'
					});
				}
				else{
					console.log("comment:",JSON.stringify(this.commentContent) );
					uni.request({
						url: this.$serverUrl + '/comment/add',
						method: 'POST',
						data:{
							content: this.commentContent,
							questionId: this.data.hotqueue.id
						},
						success: (reg) => {
							console.log(reg);
							console.log("reg0:",reg.data);
							if (reg.data.code !== 0) {
								uni.showModal({
									content: '评论失败，失败原因：' + reg.data.msg,
									showCancel: true
								})
								return;
							}
							else{
								uni.showToast({
									title:"评论成功！",
									icon: 'success'
								});
							}
							this.getData(this.detailDec);
							console.log("data:",this.data);
							this.commentContent = '';
							this.weatherComment = false;
						},
						fail: () => {
							uni.showToast({
								content: '评论失败，请检查网络重试!',
								showCancel: false
							});
						}
						
					})
				}
				
			},
			deletComment(index){
				console.log("delete!");
				console.log(index);
				uni.request({
					url: this.$serverUrl + '/comment/',
					method: 'DELETE',
					data:{
						commentId: index
					},
					success: (msg) => {
						console.log(msg.data);
						this.getData(this.detailDec);
						uni.showToast({
							title: "删除成功",
							icon: "success"
						});
					},
					
				});
			},
			download() {
				uni.downloadFile({
					url: this.data.hotqueue.content,
					success: (e) => {
						uni.saveImageToPhotosAlbum({
							filePath: e.tempFilePath,
							success: () => {
								uni.showToast({
									icon: 'none',
									title: '已保存到手机相册'
								})
							},
							fail: () => {
								uni.showToast({
									icon: 'none',
									title: '保存到手机相册失败'
								})
							}
						});
					},
					fail: (e) => {
						uni.showModal({
							content: '下载失败，' + e.errMsg,
							showCancel: false
						})
					}
				})
			},
			//#ifdef APP-PLUS
			setting() {
				uni.showToast({
					icon: 'none',
					title: '正在设为壁纸'
				})
				setTimeout(() => {
					var WallpaperManager = plus.android.importClass('android.app.WallpaperManager');
					var Main = plus.android.runtimeMainActivity();
					var wallpaperManager = WallpaperManager.getInstance(Main);
					plus.android.importClass(wallpaperManager);
					var BitmapFactory = plus.android.importClass('android.graphics.BitmapFactory');
					uni.downloadFile({
						url: this.data.hotqueue.content,
						success: (e) => {
							var filePath = plus.io.convertLocalFileSystemURL(e.tempFilePath);
							var bitmap = BitmapFactory.decodeFile(filePath);
							try {
								wallpaperManager.setBitmap(bitmap);
								uni.showToast({
									icon: 'none',
									title: '壁纸设置成功'
								})
							} catch (e) {
								uni.showToast({
									icon: 'none',
									title: '壁纸设置失败'
								})
							}
						},
						fail: () => {
							uni.showToast({
								icon: 'none',
								title: '壁纸设置失败'
							})
						}
					})
				}, 100)
			},
			//#endif
			swpierChange(e) {
				this.index = e.detail.current;
				uni.setNavigationBarTitle({
					title: e.detail.current + 1 + '/' + this.imgLength
				})
			},
			preImg(index) {
				if (this.imgShow) { //防止点击过快导致重复调用 
					return;
				}
				this.imgShow = true;
				setTimeout(() => {
					this.imgShow = false;
				}, 1000)
				setTimeout(() => {
					uni.previewImage({
						current: this.data[index],
						urls: this.data
					})
				}, 150)
			},
			getData(e) {
				uni.request({
					url: this.$serverUrl + '/queue/'+ e.data,
					method: 'GET',
					success: (res) => {
						console.log(e);
						console.log("URL:",this.$serverUrl + '/queue/'+ e.data);
						console.log("res0:",res);
						if (res.data.code !== 0) {
							uni.showModal({
								content: '请求失败，失败原因：' + res.data.error,
								showCancel: false
							})
							return;
						}
						this.data = res.data;
						console.log("data:",this.data);
						this.imgList = this.data.hotqueue.content.split("\|");
						this.imgList.pop();
						console.log("imgs:",this.imglist);
					},
					fail: () => {
						uni.showModal({
							content: '请求失败，请检查网络重试!',
							showCancel: false
						})
					}
				})
			},
			loveit(){
				uni.request({
					url: this.$serverUrl + '/collect',
					method: 'POST',
					data:{
						hotqueueId: this.data.hotqueue.id
					},
					success: (res) => {
						console.log("collecting",res.data);
						if (res.data.code == 0){
							this.$refs.uToast.show({
								title: '收藏成功',
								type: 'success',
							});
							this.getData(this.detailDec);
						}
						else{
							this.$refs.uToast.show({
								title: '收藏失败',
								type: 'error',
							})
						}
					}
				})
			},
			unloveit(){
				uni.request({
					url: this.$serverUrl + '/uncollect',
					method: 'POST',
					data:{
						hotqueueId: this.data.hotqueue.id
					},
					success: (res) => {
						console.log("collecting",res.data);
						if (res.data.code == 0){
							this.$refs.uToast.show({
								title: '取消收藏成功',
								type: 'warning',
							});
							this.getData(this.detailDec);
						}else{
							this.$refs.uToast.show({
								title: '取消收藏失败',
								type: 'error',
							})
						}
					}
				})
			},
			likeItMain(){
				console.log(this.data.hotqueue.id);
				uni.request({
					url: this.$serverUrl + '/likeq',
					method: 'POST',
					data: {
						queueId: this.data.hotqueue.id
					},
					success: (ret) =>{
						console.log("likeUrl:",this.$serverUrl + '/likeq',this.data.hotqueue.id);
						console.log("ret",ret);
						if (ret.data.code !== 0){
							this.$refs.uToast.show({
								title: '点赞失败',
								type: 'error',
							})
						}else{
							this.getData(this.detailDec);
							this.$refs.uToast.show({
								title: '点赞成功',
								type: 'success',
							})
						}
					},fail: (err) => {
						console.log("err",err);
					}
				})
			},
			cancelLikeMain(){
				console.log("取消点赞");
				
				console.log("status:",this.data.queueLikeStatus);
				uni.request({
					url: this.$serverUrl + '/disLikeq',
					method: 'POST',
					data: {
						queueId: this.data.hotqueue.id
					},
					success: (retd) =>{
						console.log("retd",retd);
						if (retd.data.code !== 0){
							this.$refs.uToast.show({
								title: '取消点赞失败',
								type: 'error',
							})
						}else{
							this.getData(this.detailDec);
							this.$refs.uToast.show({
								title: '取消点赞成功',
								type: 'success',
							})
						}
					},fail: (errd) => {
						console.log("errd",errd);
					}
				})
			},
			deleteMain(e){
				uni.request({
					url: this.$serverUrl + '/queue',
					method: 'DELETE',
					data:{
						id: e
					},
					success: (res) => {
						console.log("e",e);
						console.log("delete", res.data);
						if(res.data.code == 0){
							this.$refs.uToast.show({
								title: '删除成功',
								type: 'success',
							})
							uni.switchTab({
								url: '../index/index'
							})
						}
					}
				})
			},
			likeComment(e){
				uni.request({
					url: this.$serverUrl + '/likeco' ,
					method:'POST',
					data:{
						commentId: e
					},
					success: (res) => {
						console.log("successclick",res.data);
						this.getData(this.detailDec);
						this.$refs.uToast.show({
							title: '点赞成功',
							type: 'success',
						})
					}
				})
			},
			cancelLikeComment(e){
				uni.request({
					url: this.$serverUrl + '/disLikeco' ,
					method:'POST',
					data:{
						commentId: e
					},
					success: (res) => {
						console.log("successcancleclick",res.data);
						this.getData(this.detailDec);
						this.$refs.uToast.show({
							title: '取消点赞成功',
							type: 'warning',
						})
					}
				})
			}
		}
	}
</script>

<style lang="scss" scoped>

	swiper {
		flex: 1;
		display: flex;
		flex-direction: column;
	}

	swiper-item {
		display: flex;
		align-items: center;
	}

</style>
