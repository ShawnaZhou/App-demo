<template>
	<view>
		<view style="background-image: url(../../static/hotback.jpg); background-position: center; background-size: cover;background-repeat: no-repeat; width: 100vw; height: 30vh;"></view>
		<view style="width: 100vw; display: flex; flex-direction: column;">
			<block  v-for="(item,index) in list" :key="index">
				<view style="display: flex; flex-direction: row; height: 2.5rem;"@click="moveToSearch(item.question)">
					<view style="width: 3rem; padding-left: 0.7rem;font-size: 1.1rem; line-height: 2.5rem; text-align: center;">{{index + 1}}</view>
					<view style="width: 60vw; line-height: 2.5rem; overflow: hidden;">{{item.question}}</view>
					<view style="width: 2rem; line-height: 2.5rem;text-align: end;">{{item.cnt}}</view>
					<view  v-if="index<3" style="width: 2rem; display: flex;align-items: center; justify-content: center;">
						<image src="../../static/hot.png" style="width: 1rem; height: 1rem;">
						</image>
					</view>
				</view>
				<u-line color="grey" />
			</block>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[],
			}
		},
		onLoad(){
			this.getData();
		},
		methods: {
			getData(){
				uni.request({
					url:this.$serverUrl + '/hot',
					success: (res) => {
						console.log(res.data.msg);
						this.list = res.data.msg;
					}
				})
			},
			moveToSearch(e){
				uni.navigateTo({
					url:'../search/search?data=' + e
				})
			}
		}
	}
</script>

<style>

</style>
