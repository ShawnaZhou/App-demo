<template>
	<view>
		<u-toast ref="uToast" />
		<page-head :title="title"></page-head>
		<view class="uni-common-mt">
			<form>
				<view class="uni-list">
					<u-input placeholder=" 请输入标题" style="line-height: 2rem; height: 2rem; border-bottom: 1px solid #d6e5d2;" v-model="title"></u-input>
				</view>
				<view> 请选择标签</view>
				<view style="">
					<u-tag  type="warning" text="新建标签 +" shape="circleLeft" style="margin: 1vh 2vw;" @click="TagShow" />
					<block v-for="(item,index) in tag">
							<u-tag :text="item.name" size="default" mode="plain" shape="circleLeft" style="position: relative;margin-left: 1vw; margin-top: 1vh;" :show="show"  @click="tagChoosed(index)" />						
					</block>
				</view>
				<view v-if="newTagShow == true" style="display: flex; flex-direction: column;">
					<u-input style="margin: 1vh 2vw; width: 95% !important;" clearable="true" maxlength='5' v-model="tagName" border="true" ></u-input>
					<view style="display: flex; flex-direction: row;">
					<u-button  style="width: 40%; height: 5vh; background-color: #000000;color: #FFFFFF;"  @click="createTag">新建</u-button>
					<u-button  style="width: 40%; height: 5vh; background-color: #FFFFFF; color: #000;" @click="newTagShow = false">取消</u-button>
					</view>
				</view>
				<view v-if="tagList.length>0">已选择的标签</view>
				<view style="display: flex; flex-direction: row;">
					<block v-for="(item,index) in tagList">
						<view style=" margin: 1vh 2vw;">
							<u-tag :text="item" size="mini" mode="plain" shape="circleLeft" type="warning"  :show="show" closeable @close="closeTag(index)" @click="tagChoosed(index) " />
						</view>
					</block>
				</view>
				<view class="uni-list list-pd">
					<view class="uni-list-cell cell-pd">
						<view class="uni-uploader">
							<view class="uni-uploader-head">
								<view class="uni-uploader-title">点击可预览选好的图片</view>
								<view class="uni-uploader-info">{{imageList.length}}/9</view>
							</view>
							<view class="uni-uploader-body">
								<view class="uni-uploader__files">
									<block v-for="(image,index) in imageList" :key="index">
										<view class="uni-uploader__file">
											<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
										</view>
									</block>
									<view class="uni-uploader__input-box">
										<view class="uni-uploader__input" @tap="chooseImage"></view>
									</view>
								</view>
								<u-button ripple="true" style="background-color: #DD6161; color: white;margin-top: 50vh;" @click="upload">发布</u-button>
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
				tagName:'',
				imageList: [],
				newTagShow: false,
				tag: [],
				tagList:[],
				show: true,
				sourceTypeIndex: 2,
				sourceType: ['拍照', '相册', '拍照或相册'],
				sizeTypeIndex: 2,
				sizeType: ['压缩', '原图', '压缩或原图'],
				countIndex: 8,
				count: [1, 2, 3, 4, 5, 6, 7, 8, 9]
			}
		},
		onLoad() {
			uni.request({
				url: this.$serverUrl + '/tag/group',
				success: (res) => {
					console.log("res", res.data);
					this.tag = res.data.msg;
				}
			})
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
										content: 'Hello 需要从您的相机或相册获取图片，请在设置界面打开相关权限',
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
			TagShow(){
				this.newTagShow = !this.newTagShow;
			},

			tagChoosed(index){
				this.tagList.push(this.tag[index].name);
				console.log("index",this.tag[index].name)
				console.log("taglist",this.tagList);
			},
			createTag(){
				this.tagList.push(this.tagName);
				this.tagName = '';
				this.newTagShow = !this.newTagShow;
			},
			closeTag(e){
				this.tagList.splice(e,1);
				console.log("delete",this.tagList);
			},
			upload() {
				var file = [];
				for (var i = 1; i <= this.imageList.length; i++) {
					file.push({
						name: 'file' + i,
						uri: this.imageList[i - 1]
					}, ) //name: 'file' + i,	
				};
				var tag =this.tagList.join('&@^');
				console.log("file:", file);
				console.log("tag:",tag);
				uni.uploadFile({
					url: this.$serverUrl + '/queue/upload',
					//filePath: this.imageList[i],
					files: file,
					//name: 'file',
					formData: {
						'title': this.title,
						'count': this.imageList.length,
						'tag':  tag,
					},
					success: (uploadFileRes) => {
						console.log(uploadFileRes.data);
						this.$refs.uToast.show({
							title: '发布成功',
							type: 'success',
						});
						setTimeout(() => {
							uni.switchTab({
								url: '../index/index'
							})
						}, 2500)
					},
					fail: (err) => {
						console.log("err", err);
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
