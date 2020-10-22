<template>
	<view class="all" style="display: flex; flex-direction: column;">
	<view style="width: 80vw; height: 55vh; background-color: #FFFFFF; border-radius: 15px 15px; margin-top: 20vh; margin-left: 10vw;opacity: 0.9;">
		<view class="username" style=" margin-top: 10vh; margin-left: 2.5vw; ">
			<input placeholder="请输入用户名" type="text" style="width: 70vw; height: 2.5rem; border: 1px solid #000000; border-radius: 5px 5px; padding-left: 1rem;" name="username" v-model="username" />
		</view>
		<view class="password" style=" margin-top: 5vh; margin-left: 2.5vw;">
			<input placeholder="请输入密码" password="true"  style="width: 70vw; height: 2.5rem; border: 1px solid #000000; border-radius: 5px 5px;padding-left: 1rem;" name="password" v-model="password" />
		</view>
		<view class="submit" style="margin-top: 30%; color: #FFFFFF;">
			<button plain="true" style="color: #FFFFFF;border: #000000 2rpx solid; width: 50%; margin-left: 25%; background-color: #000000;" @click="Login()">登陆</button>
		</view>
	</view>
	</view>
</template>
<script>
	export default {
		data() {
			return {
				username: '',
				password: ''
			}
		},
		methods: {
			Login: function() {
				// let that = this;

				if (this.username == '') {
					uni.showToast({
						title: '请输入账户',
						icon: 'none',
						duration: 1000
					});
					return false;
				}

				if (this.password == '') {
					uni.showToast({
						title: '请输入密码',
						icon: 'none',
						duration: 1000
					});
					return false;
				}

				uni.request({
					url: 'http://139.196.58.222:8080/demo/login',
					method: 'POST',
					data: {
						username: this.username,
						password: this.password
					},
					header: {
						'content-type': 'application/json;charset=utf-8' //自定义请求头信息
					},
					success: (res) => {
						let list = JSON.stringify(res.data);
						console.log("返回数据状态:" + list);
						// if (list == "[]") {
						// 	uni.showToast({
						// 		icon: 'none',
						// 		title: '用户名或密码错误'
						// 	});
						// 	return;
						// }
						if(res.data.code == 1){
							uni.showToast({
								icon:'none',
								title: res.data.msg
							});
							return;
						};
						uni.showToast({
							icon: 'none',
							title: '登录成功'
						});
						let data = res.data;
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
							 },
							 fail: () => {
							 	console.log("存储失败！");
							 }
						});
						uni.switchTab({
						    url: '/pages/index/index'
						});

					},
					fail: (err) => {
						uni.showToast({
							title: '网络异常！！'
						});
					}
				})




			}
		}
	}
</script>
<style>
	.all {
		background-image: url(../../static/passback.jpg);
		opacity: 1;
		background-position: center center;
		background-repeat: no-repeat;
		background-size: cover;
		width: 100vw;
		height: 100vh;
	}
	input::-webkit-input-placeholder, textarea::-webkit-input-placeholder {
	  color: #000;
	  font-size: 16px;
	}
</style>
