<template>
	<view class="content">
		<u-toast ref="uToast" />
		<block v-for="item in contentList">
			<view style="width: 100vw;">
				<image mode="aspectFill" style="width: 90vw; margin-left: 5vw; margin-top: 2vh; border-radius: 5px 5px;" :src="item.img"></image>
				<text v-if="item.text != 'null'" style="text-align: center;width: 90vw; margin-left: 5vw;">{{item.text}}</text>
			</view>
		</block>
		<view >
			<view class="download" v-if="list.flag == false" style="margin-left: 5vw; line-height: 5vh; width: 4em; text-align: center; background-color: #000000;color: white; height: 5vh; border-radius: 5px 5px; margin-top: 2.5vh;" @click="loveit">收藏</view>
			<view class="download" v-else-if="list.flag == true" style="margin-left: 5vw; line-height: 5vh; width: 5em; text-align: center; background-color: #000000;color: white; height: 5vh; border-radius: 5px 5px; margin-top: 2.5vh;" @click="unloveit">已收藏</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				contentList: [],
				imgList: [],
				textList: [],
				sceneryId: ''
			}
		},
		onLoad(e) {
			console.log("e", e);
			this.getData(e.data);
		},
		methods: {
			getData(e) {
				this.contentList = [];
				uni.request({
					url: this.$serverUrl + '/scenery/' + e,
					success: (res) => {
						console.log("enew:", e.data);
						console.log("res", res.data);
						if (res.statusCode == 200) {
							this.list = res.data;
							console.log("list:", this.list);
							this.imgList = this.list.scenery.content.split("\|");
							this.imgList.pop();
							this.textList = this.list.scenery.page.split("&&-|");
							this.textList.pop();
							this.sceneryId = this.list.scenery.id;
							console.log(this.list.scenery.content);
							console.log("imglist", this.imgList);
							console.log("textlist", this.textList);
							uni.setNavigationBarTitle({
								title: this.list.nickName + '发布的小景'
							})
							console.log("this.list.nickName", this.list.nickName);
							for (var i = 0; i < this.imgList.length; i++) {
								this.contentList.push({
									'img': this.imgList[i],
									'text': this.textList[i]
								}, );
								console.log("content:", this.contentList[i]);
								console.log("index:", i);
							}
							console.log("content:", this.contentList);
						}
					}
				})
			},
			loveit(){
				uni.request({
					url: this.$serverUrl + '/collectScenery',
					method: 'POST',
					data:{
						sceneryId: this.sceneryId
					},
					success: (res) => {
						console.log("collecting",res.data);
						if (res.data.code == 0){
							this.$refs.uToast.show({
								title: '收藏成功',
								type: 'success',
							});
							this.getData(this.sceneryId);
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
					url: this.$serverUrl + '/uncollectScenery',
					method: 'POST',
					data:{
						sceneryId: this.sceneryId
					},
					success: (res) => {
						console.log("collecting",res.data);
						if (res.data.code == 0){
							this.$refs.uToast.show({
								title: '取消收藏成功',
								type: 'warning',
							});
							this.getData(this.sceneryId);
						}else{
							this.$refs.uToast.show({
								title: '取消收藏失败',
								type: 'error',
							})
						}
					}
				})
			},
		}
	}
</script>

<style>

</style>
