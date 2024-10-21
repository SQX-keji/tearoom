<template>
	<view>
		<view class="headtop">
			<u-tabs :list="list" :current="current" :scrollable="true" @click="click" :activeStyle="activeStyle"
				:inactiveStyle="inactiveStyle" lineColor="#01993A"></u-tabs>
		</view>

		<view class="" style="margin-top:110rpx" v-if="order.length!=0">
			<view class="orderbox" v-for="(item,index) in order" :key="index" @click="goDetail(item)">
				<view class="boxone">

					<view class="tit">{{item.tearoomName}}</view>
					<view class="stste" v-if="item.status==1">待支付</view>
					<view class="stste" v-if="item.status==2">待使用</view>
					<view class="stste" v-if="item.status==3">使用中</view>
					<view class="stste" v-if="item.status==4">已完成</view>
					<view class="stste" v-if="item.status==5">已取消</view>
					<view class="stste" v-if="item.status==6">待评价</view>
					<view class="stste" v-if="item.status==7">已退款</view>
				</view>
				<view class="line"></view>
				<view class="towbox">
					<image :src="item.goodsCoverImage" mode="aspectFit"></image>
					<view class="margin-left-sm" style="width: 80%;">
						<view class="tit">{{item.goodsName}}</view>
						<view class="data" v-if="item.ordersType !=2">预约时间：{{item.startTime}}</view>
					</view>
				</view>
				<view class="boxbot">
					<view class="prixe">
						{{item.status == 5 || item.status == 1?'应付':'实付'}}：<text>￥</text><text>{{item.payMoney}}</text>
					</view>
					<view class="flex align-center" v-if="item.ordersType !=2">
						<view class="btn" @tap.stop="bindorderOff(item)" v-if="item.status==1||item.status==2">取消订单
						</view>
						<view class="btn" @tap.stop="bindorderOffs(item)" v-if="item.status==3">提前结束
						</view>
						<view class="btn" @tap.stop="bindorderdete(item)" v-if="item.status==4">删除订单</view>
						<view class="btnn" @tap.stop="goDetail(item)" v-if="item.status==3">加时 </view>
						<view class="btnn" v-if="item.status==6" @tap.stop="bindcomment(item)">去评价</view>
						<view class="btnn" v-if="item.status==1" @click.stop="goDetail(item)">去支付</view>
					</view>
				</view>
			</view>
		</view>
		<empty v-if="order.length==0" style="margin-top:310rpx"></empty>
	</view>
</template>

<script>
	import empty from '@/components/empty.vue'
	export default {
		components: {
			empty
		},
		data() {
			return {
				list: [{
					name: '全部',
					id: ''
				}, {
					name: '待支付',
					id: 1
				}, {
					name: '待使用',
					id: 2
				}, {
					name: '使用中',
					id: 3
				}, {
					name: '待评价',
					id: 6
				}, {
					name: '已完成',
					id: 4
				}, {
					name: '已取消',
					id: 5
				}],
				current: 0,
				activeStyle: {
					color: '#333333'
				},
				inactiveStyle: {
					color: '#999999'
				},
				userId: '',
				page: 1,
				limit: 10,
				order: [],
				totalPage: '',
				token: '',
				arr: [], //通知订阅
				showModal1: true,
				tuiguang: '',
				backgroundImage: '',
				invitationCode: '', //邀请码
			}
		},
		onShareAppMessage(res) {
			return {
				path: '/pages/index/index?invitation=' + this.invitationCode, //这是为了传参   onload(data){let id=data.id;} 
				title: this.tuiguang,
				imageUrl: this.backgroundImage
			}
		},
		onShareTimeline() {
			return {
				path: '/pages/index/index?invitation=' + this.invitationCode, //这是为了传参   onload(data){let id=data.id;} 
				title: this.tuiguang,
				imageUrl: this.backgroundImage
			}
		},
		onLoad() {
			//获取邀请码
			this.invitationCode = uni.getStorageSync('invitationCode')
			//获取分享文案
			this.$Request.getT('/app/common/type/116').then(res => {
				if (res.code === 0) {
					if (res.data && res.data.value) {
						this.tuiguang = res.data.value;
					}
				}
			});
			//获取分享图片
			this.$Request.getT('/app/banner/selectBannerList?state=-1&classify=5').then(res => {
				if (res.code === 0) {
					this.backgroundImage = res.data[0].imageUrl;
				}
			});
		},
		onShow() {
			this.token = uni.getStorageSync('token')
			if (!uni.getStorageSync('token')) {
				this.page = 1
				this.order = []
				return
			}
			if (uni.getStorageSync('orderIndex')) {
				let data = uni.getStorageSync('orderIndex')
				this.current = data
				this.page = 1;
				this.orderList()
				uni.removeStorageSync('orderIndex')
			} else {
				this.current = 0
				this.page = 1;
				this.orderList()
				// //下单通知 818
				this.$Request.get('/app/common/type/818').then(res => {
					if (res.code == 0 && res.data && res.data.value) {
						if (res.data && res.data.value) {
							this.arr.push(res.data.value)
						}
					}
				})
				//订单状态通知 819
				this.$Request.get('/app/common/type/819').then(res => {
					if (res.code == 0 && res.data && res.data.value) {
						if (res.data && res.data.value) {
							this.arr.push(res.data.value)
						}
					}
				})
				//取消订单通知 820
				this.$Request.get('/app/common/type/820').then(res => {
					if (res.code == 0 && res.data && res.data.value) {
						if (res.data && res.data.value) {
							this.arr.push(res.data.value)
						}
					}
				})
				if (this.showModal1) {
					// #ifdef MP-WEIXIN
					this.openMsg()
					// #endif
				}
			}

		},
		methods: {
			// 开启订阅消息
			openMsg() {
				console.log('订阅消息')
				var that = this
				uni.getSetting({
					withSubscriptions: true, //是否获取用户订阅消息的订阅状态，默认false不返回
					success(ret) {
						console.log(ret.subscriptionsSetting, '------------------')
						// if (ret.subscriptionsSetting.itemSettings && Object.keys(ret.subscriptionsSetting.itemSettings).length == 2) {
						if (ret.subscriptionsSetting.itemSettings) {
							uni.setStorageSync('sendMsg', true)
							uni.openSetting({ // 打开设置页 
								success(rea) {
									console.log(rea.authSetting)
								}
							});
						} else { // 用户没有点击“总是保持以上，不再询问”则每次都会调起订阅消息
							console.log(99999)
							uni.setStorageSync('sendMsg', false)
							uni.showModal({
								title: '提示',
								content: '为了更好的体验,请绑定消息推送',
								confirmText: '确定',
								cancelText: '取消',
								success: function(res) {
									if (res.confirm) {
										console.log(that.arr)
										wx.requestSubscribeMessage({
											tmplIds: that.arr,
											success(re) {
												console.log(JSON.stringify(re),
													'++++++++++++++')
												var datas = JSON.stringify(re);
												if (datas.indexOf("accept") != -1) {
													console.log(re)
													// uni.setStorageSync('sendMsg', true)
												}
											},
											fail: (res) => {
												console.log(res)
											}
										})
										// uni.setStorageSync('sendMsg', true)
										console.log('确认')
										that.showModal1 = false
									} else if (res.cancel) {
										console.log('取消')
										// uni.setStorageSync('sendMsg', false)
										that.showModal1 = true
									}
								}
							})
						}
					}
				})
			},
			click(item) {
				console.log('item', item);
				this.current = item.index
				this.page = 1;
				this.orderList()
			},
			goDetail(item) {
				if (uni.getStorageSync('sendMsg')) {
					console.log('授权+1')
					wx.requestSubscribeMessage({
						tmplIds: this.arr,
						success(re) {
							console.log(JSON.stringify(re), 111111111111)
							var datas = JSON.stringify(re);
							if (datas.indexOf("accept") != -1) {
								console.log(re)
							}
						},
						fail: (res) => {
							console.log(res)
						}
					})
				}
				if (item.ordersType != 2) {
					uni.navigateTo({
						url: '/my/order/pay?ordersNo=' + item.ordersNo
					})
				} else {
					uni.navigateTo({
						url: '/my/order/myCabinetOrder?ordersNo=' + item.ordersNo
					})
				}

			},
			// 订单获取
			orderList() {
				uni.showLoading({
					title: '加载中...',
					mask: false
				});
				let type = this.list[this.current].id
				this.$Request.getT('/app/orders/selectOrdersList?page=' + this.page + '&limit=' + this.limit +
					'&status=' + type).then(res => {
					if (res.code == 0) {
						if (this.page == 1) {
							this.order = res.data.list
						}
						if (this.page > 1) {
							if (res.data.list.length > 0) {
								this.order = this.order.concat(res.data.list)

							}
						}
						this.totalPage = res.data.totalPage
					}
					uni.hideLoading()
					uni.stopPullDownRefresh();

				});
			},
			// 取消订单
			bindorderOff(e) {
				let orderId = e.ordersNo
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定取消订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							that.$Request.postT('/app/orders/cancelOrders?ordersNo=' + orderId)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										that.page = 1;
										that.orderList()
									} else {
										uni.hideLoading();
										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
								});
						}
					}
				});
			},
			// 提前结束
			bindorderOffs(e) {
				let orderId = e.ordersId
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定提前结束订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							uni.showLoading({
								title: '加载中'
							});
							that.$Request.postT('/app/orders/closeOrders?ordersId=' + orderId)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单已完成'
										});
										that.page = 1;
										that.orderList()
									} else {

										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
									uni.hideLoading();
								});
						}
					}
				});
			},
			// 去评价
			bindcomment(e) {
				uni.navigateTo({
					url: '/my/order/comments?orderId=' + e.ordersId
				})
			},
			// 删除订单
			bindorderdete(e) {
				let orderId = e.ordersId
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定删除订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							that.$Request.postT('/app/orders/removeOrders?ordersId=' + orderId)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单删除成功'
										});
										that.page = 1;
										that.orderList()
									} else {
										uni.hideLoading();
										uni.showModal({
											showCancel: false,
											title: '订单失败',
											content: res.msg
										});
									}
								});
						}
					}
				});
			}
		},
		onReachBottom: function() {
			if (this.token) {
				if (this.page < this.totalPage) {
					this.page = this.page + 1;
					this.orderList();
				}
			}
		},
		onPullDownRefresh: function() {
			if (this.token) {
				this.page = 1;
				this.orderList();
			}

		},
	}
</script>

<style lang="less">
	.headtop {
		background: #FFFFFF;
		position: fixed;
		top: 0rpx;
		left: 0;
		right: 0;
		z-index: 99;
	}

	.orderbox {
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 30rpx 0;
		margin: 20rpx 30rpx;

		.boxone {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 0 30rpx;

			.tit {
				font-size: 32rpx;
				font-family: PingFang SC;
				font-weight: 600;
			}

			.stste {
				font-size: 28rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #01993A;
			}
		}

		.line {
			width: 100%;
			background: #EEEEEE;
			height: 1rpx;
			margin: 30rpx 0;
		}

		.towbox {
			display: flex;
			align-items: center;
			padding: 0 30rpx;

			image {
				width: 100rpx;
				height: 100rpx;
				border-radius: 8rpx;
			}

			.tit {
				width: 100%;
				font-size: 32rpx;
				font-family: PingFang SC;
				font-weight: 800;
				color: #333333;
				display: -webkit-box;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 1;
				overflow: hidden;
			}

			.data {
				color: #999999;
			}
		}

		.boxbot {
			display: flex;
			align-items: center;
			justify-content: space-between;
			padding: 0 30rpx;
			margin-top: 30rpx;

			.prixe {
				font-size: 24rpx;

				text:nth-child(1) {
					color: #FF1536;
					font-size: 24rpx;
				}

				text:nth-child(2) {
					color: #FF1536;
					font-size: 32rpx;
				}
			}

			.btn {
				width: 150rpx;
				height: 60rpx;
				border: 2rpx solid #999999;
				border-radius: 30rpx;
				text-align: center;
				display: flex;
				align-items: center;
				justify-content: center;
				color: #999999;
				margin-right: 20rpx;
			}

			.btnn {
				width: 150rpx;
				height: 60rpx;
				background: #01993A;
				border-radius: 30rpx;
				text-align: center;
				display: flex;
				align-items: center;
				justify-content: center;
				color: #FFFFFF;
			}
		}

	}
</style>