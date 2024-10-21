<template>
	<view>
		<!-- 当前定位城市 -->
		<view class="dinwei flex align-center justify-center">
			<view class="dinwei-box">
				<view class="dinwei-box-title">
					当前定位城市
				</view>
				<view class="dinwei-box-city flex align-center">
					<view class="dinwei-box-city-item" @click="selectCity(city)">
						{{city}}
					</view>
				</view>
			</view>
		</view>
		<!-- 已开通的城市 -->
		<view class="dinwei flex align-center justify-center">
			<view class="dinwei-box">
				<view class="dinwei-box-title" style="color: #000;">
					已开通的城市
				</view>
				<view class="dinwei-box-city flex align-center flex-wrap">
					<view class="dinwei-box-city-item" @click="selectCity(item.city)" v-for="(item,index) in cityList"
						:key="index">
						{{item.city}}
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				city: '',
				cityList: [],
			}
		},
		onLoad() {
			this.getCity()
			this.getCityList()
		},
		methods: {
			//选择城市
			selectCity(city) {
				uni.$emit('city', city)
				uni.navigateBack()
			},
			//获取已经开通的城市列表
			getCityList() {
				let data={
					status:1
				}
				this.$Request.getT('/app/city/selectCityList',data).then(res => {
					if (res.code == 0) {
						this.cityList = res.data
					}
				})
			},
			//根据经纬度获取城市
			getCity() {
				let that = this
				uni.getLocation({
					type: 'gcj02', //返回可以用于uni.openLocation的经纬度
					success(res) {
						let data = {
							lng: res.longitude,
							lat: res.latitude
						}
						that.$Request.get('/app/Login/selectCity?lat=' + res.latitude + '&lng=' + res.longitude)
							.then(
								res => {
									if (res.code == 0) {
										that.city = res.data ? res.data : '未知'
									} else {
										uni.showToast({
											title: res.msg,
											icon: 'none'
										})
									}
								});


					}
				});
			},
		}
	}
</script>

<style lang="scss">
	.dinwei {
		width: 100%;
		height: auto;
		padding-top: 30rpx;

		.dinwei-box {
			width: 686rpx;
			height: 100%;
		}

		.dinwei-box-title {
			color: #999999;
		}

		.dinwei-box-city {
			width: 100%;
		}

		.dinwei-box-city-item {
			margin-top: 20rpx;
			padding: 20rpx 30rpx;
			background-color: #ffffff;
			border-radius: 8rpx;
			margin-right: 20rpx;
			margin-bottom: 10rpx;
		}
	}
</style>