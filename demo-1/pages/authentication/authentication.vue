<template>
	<view>
		<u-navbar  :is-back="true" title="申请认证">
		</u-navbar>
		<u-toast ref="uToast" />
		<view style="display: flex; flex-direction: column;">
			<label for="name">真实姓名*</label><input id="name" v-model="name"/>
			<label for="mobile">手机号*</label><input id="mobile" v-model="mobile"/>
			<view style="display: flex; flex-direction: row;">
				<city-select v-model="value" @city-change="cityChange"></city-select>
			</view>
			<label for="location">详细地址*</label><u-input v-model="location" type="select" style="width: 75vw; margin-left: 0vw;" placeholder="请选择地址" border-color="#000000"  @click="value = true" />
			<label for="idNum">身份证号*</label><u-input id="idNum" maxlength="18" minlength="14" style="margin-left: 0vw; width: 70vw;" placeholder="请输入身份证号" type="number" v-model="idNum"/>
			<label for="photo">上传1寸免冠照片*</label>
			<view class="uni-uploader__files">
				<block v-for="(image,index) in imageList" :key="index">
					<image @click="deleteImg(index)" src="../../static/close.png" v-if="true" style="width: 20px; height: 20px; z-index: 1000; position: absolute;left: 28vw; margin-top: 0.4rem;" ></image>
					<view class="uni-uploader__file" style="position: relative;left: 5vw;">
						<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
					</view>
				</block>
				<view class="uni-uploader__input-box" style="position: relative;left: 5vw;" v-if="imageList.length==0">
					<view class="uni-uploader__input"  @tap="chooseImage"></view>
				</view>
			</view>
			<label for="photo">上传省市级以上摄协会员证或省市级以上作品获奖证书*</label>
			<view class="uni-uploader__files">
				<block v-for="(image,index) in imageList2" :key="index">
					<image @click="deleteImg2(index)" src="../../static/close.png" v-if="true" style="width: 20px; height: 20px; z-index: 1000; position: absolute;left: 28vw; margin-top: 0.4rem;" ></image>
					<view class="uni-uploader__file" style="position: relative;left: 5vw;">
						<image class="uni-uploader__img" :src="image" :data-src="image" @tap="previewImage"></image>
					</view>
				</block>
				<view class="uni-uploader__input-box" style="position: relative;left: 5vw;" v-if="imageList2.length==0">
					<view class="uni-uploader__input"  @tap="chooseImage2"></view>
				</view>
			</view>
			
			<u-button :ripple="true" type="error" style="width: 80vw;" @click="uploadFiles">提交</u-button>
		</view>
	</view>
</template>

<script>
	import citySelect from './u-city-select.vue';
	export default {
		components: {
			citySelect
		},
		data() {
			return {
				name: '',
				mobile: '',
				location: '',
				idNum: '',
				value: false,
				imageList: [],
				imageList2:[],
				
			}
		},
		methods: {
			cityChange(e) {
				this.location = e.province.label + '-' + e.city.label + '-' + e.area.label;
			},
			chooseImage(){
			uni.chooseImage({
				count: 1,
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
			});
			
			},
			chooseImage2(){
			uni.chooseImage({
				count: 1,
				success: (res) => {
					this.imageList2 = this.imageList2.concat(res.tempFilePaths);
				},
				fail: (err) => {
					console.log("err",err);
				}
			});
			},
			previewImage: function(e) {
				var current = e.target.dataset.src
				uni.previewImage({
					current: current,
					urls: this.imageList
				})
			},
			deleteImg(e){
				this.imageList = [];
			},
			deleteImg2(e){
				this.imageList2 = [];
			},
			uploadFiles(){
				if(this.name == ''){
					uni.showToast({
						icon:'none',
						title:'请输入真实姓名！'
					})
				}
				else if(this.mobile == ''){
					uni.showToast({
						icon:'none',
						title:'请输入手机号！'
					})
				}
				else if(this.location == ''){
					uni.showToast({
						icon:'none',
						title:'请选择地址！'
					})
				}
				else if(this.idNum == ''){
					uni.showToast({
						icon:'none',
						title:'请输入身份证号！'
					})
				}
				else if(this.imageList.length == 0){
					uni.showToast({
						icon:'none',
						title:'请上传1寸免冠照片！'
					})
				}
				else if(this.imageList2.length == 0){
					uni.showToast({
						icon:'none',
						title:'请上传省市级以上摄协会员证或省市级以上作品获奖证书！'
					})
				}
				else{
						var file = [];						
							file.push({
								name: 'file1',
								uri: this.imageList[0]
							}, {
								name: 'file2',
								uri: this.imageList2[0]
							}) //name: 'file' + i,	
						console.log("file:", file);
						uni.uploadFile({
							url: this.$serverUrl + '/member',
							files: file,
							formData: {
								'name': this.name,
								'telNum': this.mobile,
								'location':  this.location,
								'reporterNum': this.idNum
							},
							success: (res) => {
								let data =JSON.parse(res.data);
								console.log(data);
								let orderInfo = data.data;
								console.log(orderInfo);
								uni.showLoading({
									mask: true,
									title: '加载中'
								});
								uni.requestPayment({
											provider: 'wxpay',
											orderInfo: orderInfo, //微信、支付宝订单数据
											success: res => {
												 uni.hideLoading();
												this.$refs.uToast.show({
													title: '提交成功，请耐心等待审核',
													type: 'success',
												});
												setTimeout(() => {
													uni.switchTab({
														url: '../index/index'
													})
												}, 2500)
											},
											fail: function(err) {
												console.log('fail:' + JSON.stringify(err));
											}
										});
							},
							fail: (err) => {
								console.log("err", err);
							}
						});
				}
			}
		}
	}
</script>

<style>
	label{
		padding-left: 1rem;
		padding-top: 1rem;
	}
	input{
		border: 1px solid #000000;
		line-height: 1.5rem;
		height: 1.5rem;
		width: 70vw;
		margin-left: 1rem;
		border-radius: 5px 5px;
	}
</style>
