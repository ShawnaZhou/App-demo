<template>	
	 <u-tabbar v-model="current" :list="list" :mid-button="true" bg-color="#1f1f1d" @change="tabChange(current)"></u-tabbar>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
						iconPath: "home",
						selectedIconPath: "home-fill",
						text: '首页',
						count: '',
						isDot: true,
						customIcon: false,
						current: 0,
					},
					{
						iconPath: "grid",
						selectedIconPath: "grid-fill",
						text: '分类',
						customIcon: false,
						current: 1,
					},
					{
						iconPath: "https://cdn.uviewui.com/uview/common/min_button.png",
						selectedIconPath: "https://cdn.uviewui.com/uview/common/min_button_select.png",
						text: '发布',
						midButton: true,
						customIcon: false,
						current: 2,
					},
					{
						iconPath: "map",
						selectedIconPath: "map-fill",
						text: '附近',
						customIcon: false,
						current: 3,
					},
					{
						iconPath: "account",
						selectedIconPath: "account-fill",
						text: '我的',
						count: 23,
						isDot: true,
						customIcon: false,
						current: 4,
					},
				],
				current: 0,
				title: 'Hello'
			}
		},
		
		onLoad() {

		},
		methods: {
			tabChange(index){
				if(index == 0){
					uni.switchTab({
						url:'../index/index'
					})
				}else if(index == 1){
					uni.switchTab({
						url:'../classify/classify'
					})
				}else if(index == 2){
					uni.switchTab({
						url:'../upload/upload'
					})
				}else if(index == 3){
					uni.switchTab({
						url: '../nearby/nearby'
					})
				}else if(index == 4){
					uni.switchTab({
						url: '../center/index'
					})
				}
			},
		}
	}
</script>


<style>

</style>
