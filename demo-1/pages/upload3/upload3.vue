<template>
	<view>
		<u-toast ref="uToast" />
		<page-head :title="title"></page-head>
		<view class="uni-common-mt">
			<form>
				<view class="uni-list">
					<u-input placeholder=" 请输入标题" style="line-height: 2rem; height: 2rem;" v-model="title"></u-input>
				</view>
				<view class="uni-list list-pd">
					<view class="uni-list-cell cell-pd">
						<view class="uni-uploader">
							<view class="uni-uploader-head">
								<view class="uni-uploader-title">点击可预览选好的图片</view>
								<view class="uni-uploader-info">{{imageList.length}}/9</view>
							</view>
							<view class="uni-uploader-body">
								<view class="uni-uploader__files" style="display: flex; flex-direction: column;">
									<block v-for="item in fileList" >
										<view class="uni-uploader__file" style="width: 100vw; display: flex;flex-direction: row;">
											<image class="uni-uploader__img" :src="item.img" :data-src="image" @tap="previewImage"></image>
											<text>{{item.content}}</text>
										</view>
									</block>
									<view class="uni-uploader__input-box" style="display: flex; flex-direction: row;">
										<view class="uni-uploader__input" @tap="chooseImage"></view>
									</view>
									<u-input v-model="content" type="textarea" border="true"/>
									
								</view>
								<u-button ripple="true" style="background-color: #DD6161; color: white;margin-top: 50vh; z-index: 1000;" @click="upload">发布</u-button>
							</view>
						</view>
					</view>
				</view>
			</form>
		</view>
	</view>
</template>
<script>
	// import permision from "@/common/permission.js"
	var sourceType = [
		['camera'],
		['album'],
		['camera', 'album']
	]
	var sizeType = [
		['compressed'],
		['original'],
		['compressed', 'original']
	]
	export default {
		data() {
			return {
				title: '',
				userId: '',
				imageList: [],
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 8,
				content: '',
				contentList:[],
				fileList:[],
				count: [1, 2, 3, 4, 5, 6, 7, 8, 9]
			}
		},
		
		onLoad() {
			this.userInfo = uni.getStorageSync('userinfo');
			this.userId = this.userInfo.userId;
		},
		
		onUnload() {
			this.imageList = [],
				this.sourceTypeIndex = 2,
				this.sourceType = ['拍照', '相册', '拍照或相册'],
				this.sizeTypeIndex = 2,
				this.sizeType = ['压缩', '原图', '压缩或原图'],
				this.countIndex = 8;
		},
		methods: {
			sourceTypeChange: function(e) {
				this.sourceTypeIndex = parseInt(e.detail.value)
			},
			sizeTypeChange: function(e) {
				this.sizeTypeIndex = parseInt(e.detail.value)
			},
			countChange: function(e) {
				this.countIndex = e.detail.value;
			},
			chooseImage: async function() {
				// #ifdef APP-PLUS
				// TODO 选择相机或相册时 需要弹出actionsheet，目前无法获得是相机还是相册，在失败回调中处理
				if (this.sourceTypeIndex !== 2) {
					let status = await this.checkPermission();
					if (status !== 1) {
						return;
					}
				}
				// #endif

				if (this.imageList.length === 9) {
					let isContinue = await this.isFullImg();
					console.log("是否继续?", isContinue);
					if (!isContinue) {
						return;
					}
				}
				uni.chooseImage({
					sourceType: sourceType[this.sourceTypeIndex],
					sizeType: sizeType[this.sizeTypeIndex],
					count: this.imageList.length + this.count[this.countIndex] > 9 ? 9 - this.imageList.length : this.count[this.countIndex],
					success: (res) => {
						this.imageList = this.imageList.concat(res.tempFilePaths);
						console.log("imglist",this.imageList);
						this.contentList.push(this.content);
						console.log("contentlist",this.contentList);
						this.content = '';
						for(var i=0;i < this.contentList.length; i++){
						console.log("filelist",this.fileList);
						}
						this.fileList.push({img: this.imageList[i-1], content: this.contentList[i-1]});
						console.log("filelist",this.fileList);
					},
					fail: (err) => {
						// #ifdef APP-PLUS
						if (err['code'] && err.code !== 0 && this.sourceTypeIndex === 2) {
							this.checkPermission(err.code);
						}
						// #endif
						// #ifdef MP
						uni.getSetting({
							success: (res) => {
								let authStatus = false;
								switch (this.sourceTypeIndex) {
									case 0:
										authStatus = res.authSetting['scope.camera'];
										break;
									case 1:
										authStatus = res.authSetting['scope.album'];
										break;
									case 2:
										authStatus = res.authSetting['scope.album'] && res.authSetting['scope.camera'];
										break;
									default:
										break;
								}
								if (!authStatus) {
									uni.showModal({
										title: '授权失败',
										content: 'Hello uni-app需要从您的相机或相册获取图片，请在设置界面打开相关权限',
										success: (res) => {
											if (res.confirm) {
												uni.openSetting()
											}
										}
									})
								}
							}
						})
						// #endif
					}
				})
			},
			isFullImg: function() {
				return new Promise((res) => {
					uni.showModal({
						content: "已经有9张图片了,是否清空现有图片？",
						success: (e) => {
							if (e.confirm) {
								this.imageList = [];
								res(true);
							} else {
								res(false)
							}
						},
						fail: () => {
							res(false)
						}
					})
				})
			},
			previewImage: function(e) {
				var current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.imageList
				})
			},
			async checkPermission(code) {
				let type = code ? code - 1 : this.sourceTypeIndex;
				let status = permision.isIOS ? await permision.requestIOS(sourceType[type][0]) :
					await permision.requestAndroid(type === 0 ? 'android.permission.CAMERA' :
						'android.permission.READ_EXTERNAL_STORAGE');

				if (status === null || status === 1) {
					status = 1;
				} else {
					uni.showModal({
						content: "没有开启权限",
						confirmText: "设置",
						success: function(res) {
							if (res.confirm) {
								permision.gotoAppSetting();
							}
						}
					})
				}

				return status;
			},
			upload() {
				var file = [];
				var content2=[];
				var content3 = '';
				for(var i=1 ; i <= this.imageList.length ; i ++){
					 file.push({name: 'file' + i , uri : this.imageList[i-1]}, ); //name: 'file' + i,
					 content2.push(this.contentList[i-1] + '&&-|');
					 content3 = content2.join("")
				} console.log("file:", file);console.log("content2",content2);console.log("content3",content3);
				uni.uploadFile({
					url: 'http://139.196.58.222:8080/demo/scenery/upload', 
					files: file,
					formData: {
						'title': this.title,
						'count': this.imageList.length,
						'content': content3
					},
					success: (uploadFileRes) => {
						console.log("contentuploading",content2);
						console.log("uploadingfile", file);
						console.log(uploadFileRes.data);
						if(uploadFileRes.statusCode == 200){
						this.$refs.uToast.show({
							title: '发布成功',
							type: 'success',
							url: '/pages/personal/personal?data=' + this.userId
						});
						}
						else{
							this.$refs.uToast.show({
								title: uploadFileRes.data.msg,
								type: 'warning',
							});
						}
					},
					fail: (err) => {
						console.log("err",err);
						this.$refs.uToast.show({
							type: 'warning',
							title: '发布失败，请检查网络'
						});
					}
				});
			},
		}
	}
</script>

<style>
	.cell-pd {
		padding: 22rpx 30rpx;
	}
	.list-pd {
		margin-top: 50rpx;
	}
</style>
