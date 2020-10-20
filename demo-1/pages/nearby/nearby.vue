<template>
	<view class="wrap">
		<view>
			<view>
				<view style="margin-right: 5vw; font-weight: bolder; text-align: 0.6rem; float: right;"><u-icon name="map-fill" size="40"></u-icon>
 {{address.province}}-{{address.city}}</view>
				<view style="margin-left: 5vw; font-size: larger; font-weight: bold;">附近的人</view>
				<view style="display: flex; flex-direction: row;">
				<block v-for="(item,index) in list">
					<view style="display: flex;flex-direction: column; justify-content: center;" @click="moveToPersonal(item.profile.userId)">
					<image :src="item.profile.head_url" style="width: 50px; height: 50px; border-radius: 50% 50%; border: 2px solid #F76260;margin-left: 5vw;"></image>
					<view style="font-size: smaller;text-align: center;margin-left: 5vw;">{{item.meter}}km</view>
					</view>
				</block>
				</view>
			</view>
			<scroll-view class="scroll-view_H" scroll-x="true" style="background-color:  #FFFFFF;" @scroll="scroll" scroll-left="120">
				<block v-for="(item,index) in list">
					<view style="display: flex; flex-direction: column; margin-left: 0vw; width: 100vw;background-color:  #FFFFFF; color: #000000; margin-top: 2vh;" @click="moveTodetail(item.hotqueue.id)">
						<view style="display: flex; flex-direction: row; ">
							<image :src="item.profile.head_url" style="width: 40px;height: 40px; border-radius: 50% 50%; margin-left: 5vw;"></image>
							<text style="line-height: 50px;margin-left: 0.5rem;">{{item.profile.nickName}}</text>
						</view>
						<image :src="item.hotqueue.content" style="margin-left: 5vw;"></image>
						<view style="display: flex;flex-direction: row;width: 100vw;"> 
						<view style="margin-left: 5vw; max-width: 50vw; max-height:2.2rem;line-height: 1.1rem; overflow: hidden;">{{item.hotqueue.title}}</view>
						<view style="text-align: right;justify-content: end;position: absolute; right:5vw">距离你{{item.meter}}km</view>
						</view>
					</view>
				</block>
			</scroll-view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list: [],
				latitude: '',
				longitude: '',
				address: [],
				old: {
					scrollTop: 0
				}
			};
		},
		onShow() {
			this.getLocation();
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			 setTimeout(function () {
			            uni.stopPullDownRefresh();
			        }, 800);
			this.getLocation();
		},
		onReachBottom() {
			this.loadStatus = 'loading';

		},
		methods: {
			scroll: function(e) {
				console.log(e)
				this.old.scrollTop = e.detail.scrollTop
			},
			
			moveTodetail(e){
				uni.navigateTo({
					url: '../detail/detail?data=' + encodeURIComponent(JSON.stringify(e))
				});
			},
			
			getLocation() {
				uni.getLocation({
					type: 'gcj02',
					geocode: 'true',
					success: (res) => {
						this.latitude = res.latitude;
						this.longitude = res.longitude;
						this.address = res.address;
						console.log('当前位置的经度：' + this.longitude);
						console.log('当前位置的纬度：' + this.latitude);
						this.getData();
					},
					fail: (err) => {
						console.log("error2", err);
					}
				});
				console.log("trueLong" + this.longitude);
			},
			
			moveToPersonal(e){
				uni.navigateTo({
					url: '../personal/personal?data=' + JSON.stringify(e)
				});
			
			},
			
			getData(e) {
				uni.request({
					url: this.$serverUrl + '/around',
					method: 'POST',
					data: {
						city1: this.address.city,
						cityCode: this.address.cityCode,
						contry1: this.address.country,
						district: this.address.district,
						poiName: this.address.poiName,
						province1: this.address.province,
						street: this.address.street,
						streetNum: this.address.streetNum,
						longitude: this.longitude,
						latitude: this.latitude
					},
					success: (res) => {
						console.log("success", res);
						this.list = res.data.msg;
						for (var i = 0; i < this.list.length; i++) {
							this.list[i].hotqueue.content = this.list[i].hotqueue.content.split('|');
							this.list[i].hotqueue.content = this.list[i].hotqueue.content[0];
						}
						console.log("list", this.list);
					},
					fail: (err) => {
						console.log("err1:", err);
					}
				});
			},
		}
	};
</script>

<style>
	page {
		background-color: rgb(240, 240, 240);
	}
</style>

<style lang="scss" scoped>
	.demo-warter {
		border-radius: 8px;
		margin: 5px;
		background-color: #ffffff;
		padding: 8px;
		position: relative;
	}

	.u-close {
		position: absolute;
		top: 32rpx;
		right: 32rpx;
	}

	.demo-img-wrap {}

	.demo-image {
		width: 100%;
		border-radius: 4px;
	}

	.demo-title {
		font-size: 30rpx;
		margin-top: 5px;
		color: $u-main-color;
	}

	.demo-tag {
		display: flex;
		margin-top: 5px;
	}

	.demo-tag-owner {
		background-color: $u-type-error;
		color: #ffffff;
		display: flex;
		align-items: center;
		padding: 4rpx 14rpx;
		border-radius: 50rpx;
		font-size: 20rpx;
		line-height: 1;
	}

	.demo-tag-text {
		border: 1px solid $u-type-primary;
		color: $u-type-primary;
		margin-left: 10px;
		border-radius: 50rpx;
		line-height: 1;
		padding: 4rpx 14rpx;
		display: flex;
		align-items: center;
		border-radius: 50rpx;
		font-size: 20rpx;
	}

	.demo-price {
		font-size: 30rpx;
		color: $u-type-error;
		margin-top: 5px;
	}

	.demo-shop {
		font-size: 22rpx;
		color: $u-tips-color;
		margin-top: 5px;
	}
</style>
