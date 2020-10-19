<template>
	<view class="all">
		<u-toast ref="uToast" />
		<view class="wrap">

			<view class="top"></view>
			<view class="content">
				<view class="title">欢迎登录</view>
				<input class="u-border-bottom" type="number" v-model="tel" placeholder="请输入手机号" />
				<view class="tips">未注册的手机号验证后自动创建账号</view>
				<button @tap="submit" :style="[inputStyle]" class="getCaptcha">获取短信验证码</button>
				<view class="alternative">
					<view class="password" @click="passwordLog">密码登录</view>
					<view class="issue">遇到问题</view><!-- @click="trouble" -->
				</view>
			</view>
			<view class="buttom">
				<view class="loginType" style="text-align: center; justify-content: center;">
					<view class="wechat item" style="text-align: center;">
						<!-- @click="wechatLog" -->
						<view class="icon" @click="wechatLogin">
							<u-icon size="70" name="weixin-fill" color="rgb(83,194,64)"></u-icon>
						</view>
						微信
					</view>
				</view>
				<view class="hint">
					登录代表同意
					<text class="link">用户协议、隐私政策，</text>
					并授权使用您的账号信息（如昵称、头像、地址）以便您统一管理
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				tel: '',
				latitude: '',
				longitude: '',
				address: []
			}
		},
		computed: {
			inputStyle() {
				let style = {};
				if (this.tel) {
					style.color = "#fff";
					style.backgroundColor = this.$u.color['warning'];
				}
				return style;
			}
		},
		methods: {
			submit() {
				uni.navigateTo({
					url: './login-detail?data=' + this.tel
				})
			},
			passwordLog(){
				uni.navigateTo({
					url: './password'
				})
			},
			wechatLogin() {
				uni.login({
					provider: 'weixin',
					success: (res) => {
						console.log("res", res);
						uni.showToast({
							icon:'loading',
						});
						uni.getUserInfo({
							success: (info) => {
								console.log("info", info);
								
								uni.request({
									url: this.$serverUrl + '/wx/',
									method:'POST',
									data:{
										openId: info.userInfo.openId,
										nickName: info.userInfo.nickName,
										gender: info.userInfo.gender,
										city: info.userInfo.city,
										province: info.userInfo.province,
										country: info.userInfo.country,
										avatarUrl: info.userInfo.avatarUrl,
										unionId: info.userInfo.unionId,
										errMsg:  info.errMsg,
									},
								success: (res) => {
									console.log("requstres",res);
									uni.setStorage({
										key: 'userinfo',
										data: {
											bio: res.data['pro']['bio'],
											birthday: res.data['pro']['birthDay'],
											nickname: res.data['pro']['nickName'],
											sex: res.data['pro']['sex'],
											head_url: res.data['pro']['head_url'],
											name: res.data['user']['name'],
											code: res.data['code'],
											userId: res.data['pro']['userId'],
											level: res.data['user']['level']
										},
										 success: function () {
											console.log("存储成功！");
											this.userInfo = uni.getStorageSync('userinfo');
											console.log("userif",this.userInfo);
											uni.showToast({
												title: '登录成功',
												type: 'success',
												icon: 'none'
											});
											setTimeout(2000,uni.switchTab({
												url: '../index/index',
											}));
										 },
										 fail: () => {
										 	console.log("存储失败！");
										 }
									});
									
								},fail: (err) => {
									console.log("err",err);
									this.$refs.uToast.show({
										title: '登录失败',
										type: 'error',
									});
								}
								})
							},
							fail: (fail) => {
								console.log("fail", fail);
							}
						});
					},
					fail: (res) => {
						console.log("err", res);
						uni.showToast({
							title: '登陆失败123',
							icon: 'none'
						})
					}
				})

			}
		}
	};
</script>

<style lang="scss" scoped>
	.all {
		width: 100%;
		height: 100%;
		margin: 0;
		padding: 0;

		overflow: hidden;
	}

	.wrap {
		width: 80%;
		position: relative;
		left: 10%;
		font-size: 28rpx;

		.content {
			width: 600rpx;
			padding: 80rpx auto 0;

			.title {
				text-align: left;
				font-size: 60rpx;
				font-weight: 500;
				padding-top: 60rpx;
				margin-bottom: 100rpx;
			}

			input {
				text-align: left;
				margin-bottom: 10rpx;
				padding-bottom: 6rpx;
			}

			.tips {
				color: $u-type-info;
				margin-bottom: 60rpx;
				margin-top: 8rpx;
			}

			.getCaptcha {
				background-color: rgb(253, 243, 208);
				color: $u-tips-color;
				border: none;
				font-size: 30rpx;
				padding: 12rpx 0;

				&::after {
					border: none;
				}
			}

			.alternative {
				color: $u-tips-color;
				display: flex;
				justify-content: space-between;
				margin-top: 30rpx;
			}
		}

		.buttom {
			.loginType {
				display: flex;
				padding: 350rpx 150rpx 150rpx 150rpx;
				justify-content: space-between;

				.item {
					display: flex;
					flex-direction: column;
					align-items: center;
					color: $u-content-color;
					font-size: 28rpx;
				}
			}

			.hint {
				padding: 20rpx 40rpx;
				font-size: 20rpx;
				color: $u-tips-color;

				.link {
					color: $u-type-warning;
				}
			}
		}
	}
</style>
