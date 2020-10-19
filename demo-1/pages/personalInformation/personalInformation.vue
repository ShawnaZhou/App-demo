<template>
	<view>
		<u-field class="text" v-model="nickname" label="昵称:" placeholder="请填写昵称" :disabled="disabled"></u-field>
		<u-field class="text" v-model="name" label="真实姓名:" placeholder="请填写姓名" :disabled="disabled"></u-field>
		<u-field class="text" v-model="bio" label="个性签名:" placeholder="请填写签名" :disabled="disabled"></u-field>
		<u-field class="text" v-model="sex" label="性别:" placeholder="请填写性别" :disabled="disabled"></u-field>
		<u-field class="text" v-model="birthday" label="生日:" placeholder="请填写生日" :disabled="disabled"></u-field>
		<u-field class="text" v-model="mobile" label="手机号:" placeholder="请填写手机号" :disabled="disabled"></u-field>
		<u-field class="text" v-model="idNum" label="身份证号:" placeholder="请填写身份证号" :disabled="disabled"></u-field>
		<u-field class="text" v-model="location" label="居住地:" placeholder="请填写居住地" :disabled="disabled"></u-field>
		<u-field class="text" v-model="job" label="职业:" placeholder="请填写职业" :disabled="disabled"></u-field>
		<u-button v-if="status == 0" ripple="true" plain="true" hair-line="true" style="position: fixed; bottom: 0; left: 0;margin: 5vh 5vw; width: 90vw;" @click="change">修改</u-button>
		<view v-if="status == 1" style="display: flex;flex-direction: row; position: fixed; width: 100vw;bottom: 0; left: 0; margin: 5vh 0;">
			<u-button @click="cancle">取消</u-button>
			<u-button @click="confirm">确认</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				status: '0',
				disabled: true,
				mobile: ' ',
				name: ' ',
				birthday: ' ',
				idNum: ' ',
				location: ' ',
				job: ' ',
				bio: ' ',
				nickname: ' ',
				sex: ' ',
				head_url: ' '
			}
		},
		onLoad() {
			this.status = 0;
			this.getData();
		},
		methods: {
			getData(){
				uni.request({
					url: this.$serverUrl + '/profile/',
					method: 'GET',
					success: (res) => {
						console.log("res:", res.data);
						this.name = res.data.profile.name;
						this.nickname = res.data.profile.nickName;
						this.mobile = res.data.profile.telNum;
						this.bio = res.data.profile.bio;
						this.birthday = res.data.profile.birthDay;
						this.idNum = res.data.profile.cardID;
						this.location = res.data.profile.location;
						this.job = res.data.profile.job;
						this.sex = res.data.profile.sex;
						this.head_url = res.data.profile.head_url;
					}
				})
			},
			change(){
				this.status = '1';
				this.disabled = false;
			},
			confirm(){
				this.status = '0',
				this.disabled = true;
				console.log("status:", this.status);
				uni.request({
					url: this.$serverUrl + '/profile/',
					method: 'PUT',
					data:{
						name: this.name,
						nickName: this.nickname,
						telNum: this.mobile,
						bio: this.bio,
						birthDay: this.birthday,
						cardID: this.idNum,
						location: this.location,
						job: this.job,
						sex: this.sex,
						head_url: this.head_url, 
					},
					
					success: (res) => {
						console.log("datadata");
						console.log("res:", res.data);
						this.name = res.data.profile.name;
						this.nickname = res.data.profile.nickName;
						this.mobile = res.data.profile.telNum;
						this.bio = res.data.profile.bio;
						this.birthday = res.data.profile.birthDay;
						this.idNum = res.data.profile.cardID;
						this.location = res.data.profile.location;
						this.job = res.data.profile.job;
						this.head_url =res.data.profile.head_url;
						this.sex = res.data.profile.sex;
					}
				})
			},
			cancle(){
				this.status = '0',
				this.disabled = true;
				this.getData();
			}
		}
	}
</script>
	
<style>
	.text{
		margin: 2vh 0;
	}
</style>
