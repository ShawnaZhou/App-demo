<template>
	<view style="width: 100vw;display: flex;flex-direction: column-reverse;margin-bottom: 8vh;">
		<block v-for="(item, index) in list" :key= "index">
			<view style="display: flex; flex-direction: column-reverse; width: 100vw; height: auto; ">
				<view style="display: flex; flex-direction: row; width: auto; max-width: 100vw; margin-top: 1vh;" v-if="item.user.id != userId">
					<u-avatar :src="item.user.head_url"></u-avatar>
					<view style="width: auto;background-color: #007AFF; line-height: 1.2rem;
					max-width: 60vw;padding-top: 5px;border-radius: 0 10px; height: auto;overflow: hidden; word-break:break-all;
					min-width: 5vw;padding: 5px 5px; margin-left: 2vw;">{{item.message.content}}</view>
				</view>
				<view style="display: flex; flex-direction: row-reverse; max-width: 100vw; width: auto; margin-top: 1vh;" v-if="item.user.id == userId">
					<u-avatar :src="item.user.head_url"></u-avatar>
					<view style="width: auto;background-color: #09BB07; line-height: 1.2rem;
					max-width: 60vw;padding-top: 5px;border-radius: 10px 0px;overflow: hidden;height: auto;
					min-width: 5vw;padding: 5px 5px; margin-right: 2vw;">{{item.message.content}}</view>
				</view>
				<view style="width: 100vw;line-height: 3rem; text-align: center; color: grey;" v-if="index % 10 ==0">{{item.message.createDate}}</view>
			</view>
		</block>
		<u-field v-model="chat" style="position: fixed; left: 0; bottom: 0; border-top: 1px solid #6D6D72; width: 100vw;background-color: #DBF1E1;">
			<u-button size="default" slot="right" type="success" @tap="postData(chat)">发送</u-button>
		</u-field>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				messId: '',
				chat: '',
				list: [],
				userId: '',
				title: '',
				otherUserId: ''
			}
		},
		onLoad(e) {
			console.log("e", e.data);
			this.otherUserId = e.data;
			console.log("otherUserId:", this.otherUserId);
			this.userInfo = uni.getStorageSync('userinfo');
			this.userId = this.userInfo.userId;
			if(this.userId < this.otherUserId){
				this.messId = this.userId + "-" + this.otherUserId;
			}else{
				this.messId = this.otherUserId + "-" + this.userId;
			}
			
			console.log("messId", this.messId);
			this.getData(this.messId);
			
			uni.pageScrollTo({
				scrollTop: 10000,
				duration: 0,
				success: (res) => {
					console.log("scroll:", res);
				},
				fail: (err) => {
					console.log("scrollErr:", err);
				}
			})
		},

		onShow() {
			uni.pageScrollTo({
				scrollTop: 10000,
				duration: 0,
				success: (res) => {
					console.log("scrolsl:", res);
				},
				fail: (err) => {
					console.log("scrollErr:", err);
				}
			});
			
		},
		methods: {
			getData(e) {
				console.log("eee", e);
				uni.request({
					url: this.$serverUrl + '/message/chat',
					method: 'POST',
					data: {
						messageId: e
					},
					success: (res) => {
						this.title = res.data.otherProfile.nickName;
						this.list = res.data.chatMessage;
						console.log("res", res.data.chatMessage);
						console.log("list ", this.list);
						uni.setNavigationBarTitle({
							title: this.title,
							success: (res) => {
								console.log("rrrrrr",res.data);
							},
							fail: (err) => {
								console.log("errrrrr",err);
							}
						});
						uni.pageScrollTo({
							scrollTop: 10000,
							duration: 0,
							success: (res) => {
								console.log("scroll:", res);
							},
							fail: (err) => {
								console.log("scrollErr:", err);
							}
						})
					}
				})
			},
			postData(e) {
				if (e.length > 0) {
					uni.request({
						url: this.$serverUrl + '/message/',
						method: 'POST',
						data: {
							toId: Number(this.otherUserId),
							content: e
						},
						success: (res) => {
							console.log("ToId:", Number(this.otherUserId));
							console.log("content:", e);
							console.log("postres", res.data);

							this.getData(this.messId);
							this.chat = "";
						},
						fail: (err) => {
							console.log(err);
						}
					})
				} else {
					uni.showToast({
						icon: 'none',
						title: '请输入内容'
					})
				}
			}
		}
	}
</script>

<style>

</style>
