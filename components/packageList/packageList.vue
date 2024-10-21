<template>
	<view>
		<view class="flex align-center justify-center" style="margin-bottom: 20rpx;">
			<view class="bg-box">
				<view class="flex" style="width: 100%;" v-if="listData.goodsCoverImage">
					<image class="screen-swiper" :src="listData.goodsCoverImage" mode="aspectFill"
						style="border-radius: 24rpx;"></image>
					<view class="" style="border-radius: 24rpx;padding:10rpx 30rpx;width: 450rpx;">
						<view class="tit">{{listData.goodsName}}</view>
						<view class="label flex align-center flex-wrap">
							<view v-if="listData.goodsType" class="label-item"
								v-for="(item,index) in listData.goodsType?listData.goodsType.split(','):[]"
								:key="index">
								{{item}}
							</view>
							<view class="label-item">
								已售{{listData.sales}}
							</view>
						</view>
						<view class="priceStatus flex align-center justify-between">
							<view class="priceStatus-l">
								<text>¥</text>{{listData.price}}/<text>小时</text>
							</view>
							<view class="priceStatus-r flex align-center justify-between">
								<!-- <view class="priceStatus-r-txt">
									{{isKongxian==true?'空闲中':'已满'}}
								</view> -->
								<view class="priceStatus-r-txt" v-if="listData.status==1">空闲中</view>
								<view class="priceStatus-r-txt" v-else>已满</view>
								<text v-if="isKongxian">订</text>
							</view>
						</view>
					</view>
				</view>
				<!-- end_time: "01:00"
				id: 1
				is_busy: 0
				str_time: "00:00" -->
				<!-- 时间段状态 -->
				<view class="timeStatus flex justify-between" v-if="listData.roomTimeList.length>0">
					<view class="timeStatus-item" v-for="(item,index) in listData.roomTimeList" :key="index">
						<view class="timeStatus-item-k" v-if="item.is_busy==0">

						</view>
						<view class="timeStatus-item-k ding" v-else>

						</view>
						<view class="timeStatus-item-t">
							{{index==0?'次日':index}}
						</view>
					</view>
				</view>
				<!-- 状态标识 -->
				<view class="timeStatusbs flex align-center justify-end">
					<view class="timeStatusbs-item flex align-center">
						<view class="timeStatusbs-item-h">

						</view>
						不可用
					</view>
					<view class="timeStatusbs-item flex align-center">
						<view class="timeStatusbs-item-h">

						</view>
						可用时段
					</view>
				</view>

			</view>

		</view>
	</view>
</template>

<script>
	export default {
		name: "packageList",
		props: {
			listData: {
				type: Object,
				default: {}
			}
		},
		data() {
			return {
				isKongxian: true
			};
		},
		watch: {
			listData() {
				console.log(this.listData, '132135')
				this.isKongxian = this.isIdle(this.listData.roomTimeList)
			}
		},
		methods: {
			/**
			 * @param {Array} arr
			 * 判断是否是空闲中
			 */
			isIdle(arr) {
				let flag = true
				let index = arr.findIndex(item => Number(item.is_busy) === 0)
				if (index != -1) {
					flag = true
				} else {
					flag = false
				}
				return flag
			},
		}
	}
</script>

<style lang="scss">
	.bg-box {
		width: 686rpx;
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 24rpx 30rpx;
	}

	.screen-swiper {
		width: 195rpx;
		height: 177rpx;
	}

	.tit {
		width: 420rpx;
		font-size: 32rpx;
		font-weight: 800;
		margin-bottom: 10rpx;
		text-overflow: ellipsis;
		white-space: nowrap;
		overflow: hidden;
	}

	.timeStatusbs {
		width: 100%;
		font-size: 20rpx;
		color: #151515;

		.timeStatusbs-item {
			margin-left: 10rpx;
		}

		.timeStatusbs-item-h {
			width: 24rpx;
			height: 24rpx;
			margin-right: 10rpx;
		}

		.timeStatusbs-item:nth-of-type(1) {
			.timeStatusbs-item-h {
				background-color: #01993A;
			}

		}

		.timeStatusbs-item:nth-of-type(2) {
			.timeStatusbs-item-h {
				border: 1px solid #A0A0A0;
			}

		}

	}

	.timeStatus {
		margin-top: 20rpx;
		font-size: 20rpx;

		.timeStatus-item {
			width: 24rpx;
		}

		.ding {
			background-color: #01993A;
			// border: 1px solid #442916;
			border-color: #01993A !important;
		}

		.timeStatus-item-k {
			width: 24rpx;
			height: 24rpx;
			border: 1px solid #BFBFBF;
		}

		.timeStatus-item-t {
			width: 24rpx;
			text-align: center;
		}
	}

	.priceStatus {
		margin-top: 15rpx;
		width: 420rpx;

		.priceStatus-l {
			color: #FF1536;
			font-size: 38rpx;
			font-weight: bold;

			text:nth-of-type(1) {
				font-size: 24rpx;
			}

			text:nth-of-type(2) {
				font-size: 26rpx;
				font-weight: 400;
			}
		}

		.priceStatus-r {
			background-color: #01993A;
			font-size: 26rpx;
			border-radius: 12rpx;
			color: #ffffff;
			padding: 4rpx;

			.priceStatus-r-txt {
				background-color: #ffffff;
				border-radius: 12rpx;
				color: #01993A;
				padding: 10rpx 20rpx;
			}

			text {
				margin-left: 18rpx;
				margin-right: 18rpx;
			}
		}
	}

	.label {

		.label-item {
			background-color: #E6FFEF;
			font-size: 20rpx;
			color: #01993A;
			border-radius: 4rpx;
			margin-right: 5rpx;
			margin-bottom: 5rpx;
			padding: 5rpx 10rpx;
		}
	}
</style>