<template>
	<view style="width: 100vw;display: flex;flex-direction: column-reverse;margin-bottom: 8vh;">
		<block v-for="(item, index) in list" :key= "index">
			<view style="display: flex; flex-direction: column-reverse; width: 100vw; height: auto; ">
				<view style="display: flex; flex-direction: row; width: auto; max-width: 100vw; margin-top: 1vh; padding-left: 0.5rem;" v-if="item.message.fromId != userId">
					<u-avatar :src="otherHead"></u-avatar>
					<view style="width: auto;background-color: white; line-height: 1.2rem;
					max-width: 60vw;padding-top: 5px;border-radius: 0 10px 10px 10px; height: auto;overflow: hidden; word-break:break-all;
					min-width: 5vw;padding: 0.7rem 0.5rem; margin-left: 2vw;">{{item.message.content}}</view>
				</view>
				<view style="display: flex; flex-direction: row-reverse; max-width: 100vw; width: auto; margin-top: 1vh; padding-right: 0.5rem;" v-if="item.message.fromId == userId">
					<u-avatar :src="myHead"></u-avatar>
					<view style="width: auto;background-color: #3385c6; line-height: 1.2rem;max-width: 60vw;padding-top: 5px;border-radius: 10px 0 10px 10px;overflow: hidden;height: auto;
					min-width: 5vw;padding: 0.7rem 0.5rem; margin-right: 2vw !important; color: #FFFFFF;">{{item.message.content}}</view>
				</view>
				<view style="width: 100vw;line-height: 3rem; text-align: center; color: grey;" v-if="index % 10 ==0">{{item.message.CreateDate}}</view>
			</view>
		</block>
		<u-field v-model="chat" style="position: fixed; left: 0; bottom: 0; border-top: 1px solid #6D6D72; width: 100vw;background-color: #fafafa;">
			<u-button size="mini" slot="right" type="success" @tap="postData(chat)">发送</u-button>
		</u-field>
	</view>

</template>

<script>
	export default {
		data() {
			return {
				messId: '',
				myHead: '',
				otherHead: '',
				chat: '',
				list: [],
				userId: '',
				title: '',
				otherUserId: '',
				pageNum: 0,
				endPageNum: 20,
			}
		},
		
		 onPullDownRefresh() {
			 this.endPageNum = this.endPageNum + 10;
			 this.getData(this.messId);
		        setTimeout(function () {
		            uni.stopPullDownRefresh();
		        }, 500);
		    },
		onLoad(e) {
			console.log("e", e.data);
			this.otherUserId = e.data;
			console.log("otherUserId:", this.otherUserId);
			this.userInfo = uni.getStorageSync('userinfo');
			this.userId = this.userInfo.userId;
			this.messId = this.otherUserId + "-" + this.userId;
			console.log("messId", this.messId);
			this.getData(this.messId);
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
						messageId: e,
						start: this.pageNum,
						end: this.endPageNum,
					},
					success: (res) => {
						console.log("HELLO!",res);
						this.endPageNum = this.endPageNum + 1;
						this.title = res.data.otherProfile.nickName;
						this.list = res.data.chatMessage;
						console.log("res", res.data.chatMessage);
						console.log("list ", this.list);
						uni.setNavigationBarTitle({
							title: this.title,
						});
						uni.pageScrollTo({
							scrollTop: 10000,
							duration: 0,
						});
						if(this.userId != res.data.fromUser.id){
							this.myHead = res.data.toUser.head_url;
							this.otherHead = res.data.fromUser.head_url;
						}else{
							this.myHead = res.data.fromUser.head_url;
							this.otherHead = res.data.toUser.head_url;
						}
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
							console.log(this.messId);
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
	page{
		background-color: #e5e6eb;
	}
</style>
