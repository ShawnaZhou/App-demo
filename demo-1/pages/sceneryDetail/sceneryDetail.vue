<template>
	<view class="content">
		<block v-for="item in list">
			<view style="width: 100vw;">
				<image mode="aspectFill" style="width: 90vw; margin-left: 5vw; margin-top: 2vh; border-radius: 5px 5px;" :src="item.pic"></image>
			</view>
		</block>
		<view style="width: 90vw; margin-left: 5vw; margin-top: 10vh;">
			<text style="width: 90vw; margin-left: 5vw; line-height: 1.2rem;padding-bottom: 20vh; ">{{list[0].descri}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: []
			}
		},
		onLoad(e) {
			console.log("e:",e.name);
			JSON.stringify(e);
			console.log("e2:",e.name);
			this.getData(e);
		},
		methods: {
			getData(e){
				uni.request({
					url:  this.$serverUrl + '/view/'+'whichName=' + e.name.slice(1, e.name.length - 1),
					method: 'GET',
					success:(res) => {
						if(res.data.code != 0){
							uni.showToast({
								title: '加载异常请稍后再试',
								icon: 'none'
							});
						}else {
							this.list = res.data.msg;
							console.log("list:",this.list);
						}
						console.log("res.data.code",res.data.code);
						console.log("list2:",this.list);
						console.log("res.data:",res.data);
						console.log('url', this.$serverUrl + '/view/'+'whichName=' + e.name.slice(1, e.name.length - 1));
					}
				})
			}
		}
	}
</script>

<style>

</style>
