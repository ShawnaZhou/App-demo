<template>
	<view class="content" style="width: 100vw;">
		<view style="box-shadow: 1px 1px grey; height: 15vh;  width: 100vw; z-index: 10000;  top: 5vh; background-color: transparent;">
		<view style="display: flex; flex-direction: row; ">
		<u-select v-model="show1" mode="single-column" :default-value="defaultValue1" :list="list1" @confirm="confirm1"></u-select>
		<u-button @click="show1 = true" style="width: 35vw;float: left;margin-left: 15vw; border-color: #000000;">{{result1}}<u-icon name="arrow-down-fill" size="20"></u-icon></u-button>
		<u-picker v-model="show2" mode="region" :params="params" @confirm="confirm2"></u-picker>
		<u-button @click="show2 = true" style="width: 35vw;float: left; margin-left: -15vw;border-color: #000000;">{{result2}}<u-icon name="arrow-down-fill" size="20"></u-icon></u-button>
		<!-- 		<u-select v-model="show3" mode="single-column" :default-value="defaultValue3" :list="list3" @confirm="confirm3"></u-select>
		<u-button @click="show3= true" style="width: 25vw;float: left;">{{result3}}</u-button> -->
		</view>
		<u-field v-model="name"
				placeholder="请输入景点名称"
				@blur="confirm3"
				style="border: 1px solid #000000; width: 70vw; margin-left: 15vw;
				margin-top: 2vh; border-radius: 5px 5px; border-color: grey; background-color: #FFFFFF;"
				placeholder-style="color: grey;"
				>
		</u-field>
		</view>
		<view style="position: relative;  width: 90vw; margin-left: 5vw; border-radius: 10px 10px;margin-top: 2vh;" >
		<block v-for="(item,index) in list" :key="item.id">
			<view style="width: 90vw; height: 40vh; box-shadow: 0px 1px grey; margin-top: 2vh; background-color: #fff;" 
			@click="goDetail(item.name)">
				<text style="line-height: 5vh; margin-left: 2vw;">{{item.name}}</text>
				<view style="width: 100vw; margin-left: 2vw;">
					<image  v-if="item.id != 0" :src="item.pic" style="border-radius: 5px 5px;" mode="aspectFill"></image>
				</view>
			</view>
		</block>
		</view>
		<u-loadmore status="loading" height="20vh"/>
	</view>
	
</template>

<script>
	export default {
		data() {
			return {
				weatherShow: false,
				list: [],
				name: '',
				show1: false,
				show2: false,
				result1: '',
				result2: '',
				result3: '',
				defaultValue1: [1],
				defaultValue2: [1],
				defaultValue3: [1],
				resultValue1: '',
				resultValue2: '',
				resultValue3: '',
				refreshing: false,
				fetchPageNum: 0,
				data: [],
				list1: [{
						value: '0',
						label: '选择景区等级'
					},
					{
						value: '5A',
						label: '5A'
					},
					{
						value: '4A',
						label: '4A'
					},
					{
						value: '3A',
						label: '3A'
					},
					{
						value: '2A',
						label: '2A'
					},
					{
						value: '1A',
						label: '1A'
					}
				],
				params: {
					province: true,
					city: false,
					area: false
				},
			}
		},
		onReachBottom() {
			console.log('滑动到页面底部');
			this.getData();
		},
		onPullDownRefresh() {
			console.log('下拉刷新');
			this.refreshing = true;
			this.getData();
		},
		onLoad() {
			this.result1 = this.list1[0].label;
			this.resultValue1 = '0';
			this.result2 = "选择省份";
			this.resultValue2 = "0";
			this.resultValue3 = "0";
			this.getData();
		},
		methods: {
			confirm1(e) {
				console.log(e);
				this.result1 = '';
				this.refreshing = true;
				e.map((val, index) => {
					this.result1 += this.result1 == '' ? val.label : '-' + val.label;
				})
				this.resultValue1 = e[0].value;
				console.log("resultValue1", this.resultValue1);
				console.log("result1", this.result1);
				this.getData();
			},
			confirm2(e) {
				console.log(e.province);
				this.result2 = '';
				this.refreshing = true;
				this.result2 = e.province.label;
				this.resultValue2 = e.province.value;
				console.log("resultValue2", this.resultValue2);
				console.log("result2", this.result2);
				this.getData();
			},
			confirm3() {
				console.log("name:",this.name);
				this.refreshing = true;
				console.log("result3-0",this.result3);
				this.result3 = '';
				this.result3 = this.name;
				console.log("result3-1",this.result3);
				this.resultValue3 = this.result3;
				console.log("resultValue3", this.resultValue3);
				console.log("result3", this.result3);
				this.getData();
			},
			getData() {
				console.log("r1:", this.result1);
				console.log("rv1:", this.resultValue1);
				console.log("r2:", this.result2);
				console.log("rv2:", this.resultValue2);
				console.log("r3:" ,this.result3);
				console.log("rv3:",this.resultValue3);
				uni.request({
					url: this.$serverUrl + '/view/' + ( this.resultValue1 == 0 ? '' : ('level=' + this.resultValue1)) + ( this.resultValue3 == 0 ? '' : ( '/name=' +
						this.resultValue3 )) + ( this.resultValue2 == 0 ? '':('/mit=' + this.resultValue2)) + '-' + 'offset=' + (this.refreshing ? 0 : this.fetchPageNum) ,
					success: (ret) => {
						console.log('data11:', ret.data.msg);
						console.log("url",this.$serverUrl + '/view/' + ( this.resultValue1 == 0 ? '' : ('level=' + this.resultValue1)) + ( this.resultValue3 == 0 ? '' : ( '/name=' +
						this.resultValue3 )) + ( this.resultValue2 == 0 ? '':('/mit=' + this.resultValue2)) + '-' + 'offset=' + (this.refreshing ? 0 : this.fetchPageNum));
						this.data = ret.data.msg;
						console.log("dataId:",this.data[0].id);
						console.log('data22:',this.data);
						if (ret.statusCode !== 200) {
							console.log('失败!');
						} else {
							// if (this.refreshing && this.data[0].id === this.list[0].id) {
							// 	uni.showToast({	
							// 		title: '已经最新',
							// 		icon: 'none',
							// 	})
							// 	this.refreshing = false;
							// 	uni.stopPullDownRefresh();
							// 	return;
							// }
							if (this.refreshing) {
								this.refreshing = false;
								uni.stopPullDownRefresh()
								this.list = this.data;
								console.log("list1:",this.list);
								console.log("data1:",this.data);
								this.fetchPageNum = 0;
							} else {
								this.list = this.list.concat(this.data);
								console.log("list3:",this.list);
								this.fetchPageNum += 5;
							}

							console.log("list:",this.list);
							console.log('Listid:', this.list[0].id);
							
						
						}
					}
				});
			},
			goDetail(e){
				uni.navigateTo({
					url: '../sceneryDetail/sceneryDetail?name=' + JSON.stringify(e)
					// ?data='+ encodeURIComponent(JSON.stringify(e))  + JSON.stringify(e)
					});
				console.log("nameInUrl", JSON.stringify(e));
			},
		}
	}
</script>

<style lang="scss" scoped>
	page{
		// background-image: url(../../static/scenery-back.jpg);
		background-color: #FFFFFF;
		background-position: center center;
		background-repeat: no-repeat;
		background-size: cover;
		background-attachment: fixed;
		opacity: 1;
	}
</style>
