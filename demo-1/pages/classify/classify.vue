<template>
	<view>
		<view class="content">
			<u-grid :col="col" @click="click" :border="border">
				<u-grid-item name="item1" :index="0" @click="itemClick">
					<u-badge count="9" :offset="[col == 3 ? 20 : 14, col == 3 ? 30 : 20]"></u-badge>
					<u-icon name="photo" :size="46"></u-icon>
					<view class="grid-text">A</view>
				</u-grid-item>
				<u-grid-item :index="1">
					<u-icon name="lock" :size="46"></u-icon>
					<view class="grid-text">B</view>
				</u-grid-item>
				<u-grid-item :index="2">
					<image src="https://cdn.uviewui.com/uview/grid/hot5.png" :style="{
						top: col == 3 ? '14rpx' : '8rpx',
						right: col == 3 ? '40rpx' : '28rpx'
					}" style="width: 34rpx;height: 34rpx;" class="badge-icon" mode="widthFix"></image>
					<u-icon name="hourglass" :size="46"></u-icon>
					<view class="grid-text">C</view>
				</u-grid-item>
				<u-grid-item :index="3">
					<u-icon name="home" :size="46"></u-icon>
					<view class="grid-text">D</view>
				</u-grid-item>
				<u-grid-item :index="4">
					<u-icon name="star" :size="46"></u-icon>
					<view class="grid-text">E</view>
				</u-grid-item>
				<u-grid-item :index="5">
					<u-icon name="volume-up" :size="46"></u-icon>
					<view class="grid-text">F</view>
				</u-grid-item>
				<u-grid-item :index="6">
					<image src="https://cdn.uviewui.com/uview/grid/hot6.png" style="width: 44rpx;height: 44rpx;top:0;right:0;" class="badge-icon"></image>
					<u-icon name="trash" :size="46"></u-icon>
					<view class="grid-text">G</view>
				</u-grid-item>
				<u-grid-item :index="7">
					<u-icon name="rewind-right" :size="46"></u-icon>
					<view class="grid-text">H</view>
				</u-grid-item>
				<u-grid-item :index="8">
					<u-icon name="shopping-cart" :size="46"></u-icon>
					<view class="grid-text">J</view>
				</u-grid-item>
			</u-grid>
			
		</view>
		
		
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
				}
		},
		
		onLoad() {

		},
		methods: {
			itemClick(index) {
				// console.log(index);
			}
		}
	}
</script>

<style>

</style>
