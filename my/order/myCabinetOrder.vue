<template>
	<view>
		<!-- 状态 -->
		<view class="status flex align-center justify-center">
			<view class="status-box flex align-center justify-center">
				{{order.status == 4?'已开柜...':'待支付...'}}
			</view>
		</view>
		<!-- 商品详情 -->
		<view class="goodsInfo flex align-center justify-center">
			<view class="goodsInfo-box">
				<!-- 柜子名称 -->
				<view class="goodsInfo-box-title flex align-center">
					<image src="../../static/images/my/guiziIcon.png" mode=""></image>
					{{info.name}}
				</view>
				<u-line color="#E5E5E5"></u-line>
				<!-- 商品详情 -->
				<view class="goodsInfo-box-goods flex align-center justify-between">
					<view class="goodsInfo-box-goods-l flex align-center">
						<image :src="smallGoods.image" mode="aspectFill"></image>
						<view class="goodsInfo-box-goods-l-r">
							<view class="goodsInfo-box-goods-l-r-t">
								{{smallGoods.title}}
							</view>
							<view class="goodsInfo-box-goods-l-r-b">
								数量：x{{smallGoods.goodsNum}}
							</view>
						</view>
					</view>
					<view class="goodsInfo-box-goods-r">
						<text style="color: #333333;font-size: 24rpx;">¥</text>42.00
					</view>
				</view>
				<u-line color="#E5E5E5"></u-line>
				<view class="goodsInfo-box-price flex align-center justify-between">
					<view class="goodsInfo-box-price-l">
						{{order.status == 4?'实付':'应付'}}
					</view>
					<view class="goodsInfo-box-price-r">
						<text style="color: #FF130A;font-size: 24rpx;">¥</text>
						{{smallGoods.money}}
					</view>
				</view>
			</view>
		</view>
		<!-- 其它信息 -->
		<view class="orderInfo flex align-center justify-center">
			<view class="orderInfo-box">
				<view class="orderInfo-box-title">
					其他信息
				</view>
				<view v-if="order.payTime" class="orderInfo-box-item flex align-center justify-between">
					<view class="orderInfo-box-item-l">
						开柜时间
					</view>
					<view class="orderInfo-box-item-r">
						{{order.payTime}}
					</view>
				</view>
				<view class="orderInfo-box-item flex align-center justify-between">
					<view class="orderInfo-box-item-l">
						订单号
					</view>
					<view class="orderInfo-box-item-r">
						{{order.ordersNo}}
					</view>
				</view>
				<view class="orderInfo-box-item flex align-center justify-between">
					<view class="orderInfo-box-item-l">
						下单时间
					</view>
					<view class="orderInfo-box-item-r">
						{{order.createTime}}
					</view>
				</view>
				<view v-if="order.payWay" class="orderInfo-box-item flex align-center justify-between">
					<view class="orderInfo-box-item-l">
						支付方式
					</view>
					<view class="orderInfo-box-item-r">
						{{order.payWay==1?'支付宝':(order.payWay==2?'微信':'零钱')}}
					</view>
				</view>

			</view>
		</view>
		<!-- 底部按钮 -->
		<view v-if="order.status != 4" class="btn flex align-center justify-center">
			<view class="btn-box flex align-center justify-end">
				<view @click="show = true" class="btn-box-item flex align-center justify-center">
					立即支付
				</view>
			</view>
		</view>
		<u-popup :show="show" @close="show = false">
			<view class="box">
				<view class="text-lg text-bold">
					支付方式
				</view>
				<view style="display: flex;height: 100upx;align-items: center;padding: 20upx 0rpx;"
					v-for="(item,index) in openLists" :key='index'>
					<view style="display: flex;width:80%;align-items: center;">
						<image :src="item.image" style="width: 55upx;height: 55upx;border-radius: 50upx;"></image>
						<view style="font-size: 30upx;margin-left: 20upx;width: 70%;">{{item.text}}
						</view>
					</view>
					<view style="width: 20%;display: flex;justify-content: flex-end;">
						<radio-group name="openWay" style="margin-left: 20upx;" @tap='selectWay(item.id)'>
							<label class="tui-radio">
								<radio color="#01993A" :checked="openWay === item.id ? true : false" />
							</label>
						</radio-group>
					</view>
				</view>
				<view class="btnpay" @click="orderpay">立即下单</view>
			</view>
		</u-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				ordersNo: '', //订单号
				order: {}, //订单详情
				tearoom: {}, //棋牌室详情
				info: {},
				smallGoods: {},
				show: false,
				openWay: 2,
			};
		},
		onLoad(option) {
			if (option.ordersNo) {
				this.ordersNo = option.ordersNo
				this.getDetail()
			}
			// #ifdef APP-PLUS
			this.openLists = [{
				image: '/static/images/zhifubao.png',
				text: '支付宝支付',
				id: 1
			}, {
				image: '/static/images/icon_weixin.png',
				text: '微信支付',
				id: 2
			}, {
				image: '/static/images/lingqian.png',
				text: '会员卡支付',
				id: 3
			}];
			this.openWay = 1;
			// #endif

			// #ifdef MP-WEIXIN
			this.openLists = [{
				image: '/static/images/icon_weixin.png',
				text: '微信支付',
				id: 2
			}, {
				image: '/static/images/lingqian.png',
				text: '会员卡支付',
				id: 3
			}];
			this.openWay = 2;
			// #endif
			// #ifdef H5
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				//公众号是否自动登录  108
				this.$Request.getT('/app/common/type/108').then(res => {
					if (res.data && res.data.value && res.data.value == '是') {
						this.openLists = [{
							image: '../../static/images/zhifubao.png',
							text: '支付宝',
							id: 1
						}, {
							image: '../../static/images/icon_weixin.png',
							text: '微信',
							id: 2
						}, {
							image: '../../static/images/lingqian.png',
							text: '会员卡支付',
							id: 3
						}];
						this.openWay = 2;
					} else {
						this.openLists = [{
							image: '../../static/images/zhifubao.png',
							text: '支付宝',
							id: 1
						}, {
							image: '../../static/images/lingqian.png',
							text: '会员卡支付',
							id: 3
						}];
						this.openWay = 1;
					}
				})
			} else {
				this.openLists = [{
					image: '../../static/images/zhifubao.png',
					text: '支付宝',
					id: 1
				}, {
					image: '../../static/images/lingqian.png',
					text: '会员卡支付',
					id: 3
				}];
				this.openWay = 1;
			}
			// #endif
		},
		methods: {
			orderpay() {
				this.$queue.showLoading('支付中...')
				this.show = false
				let that = this

				if (that.openWay == 1) { //支付宝
					// #ifdef H5
					let data = {
						ordersNo: that.ordersNo,
						classify: 5 //支付宝 classify 4app 5h5
					}
					that.$Request.postT('/app/aliPay/payOrders', data).then(red => {
						uni.hideLoading();
						if (red.code == 0) {
							const div = document.createElement('div')
							div.innerHTML = red.data //此处form就是后台返回接收到的数据
							document.body.appendChild(div)
							document.forms[0].submit()
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
					// #ifdef APP
					let data = {
						ordersNo: that.ordersNo,
						classify: 4 //支付宝 classify 4app 5h5
					}
					that.$Request.postT('/app/aliPay/payOrders', data).then(red => {
						uni.hideLoading();
						if (red.code == 0) {
							that.setPayment('alipay', red.data);
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
				} else if (that.openWay == 2) { //2微信
					// #ifdef MP-WEIXIN
					let data = {
						ordersNo: that.ordersNo,
						classify: 3 //微信 classify 1app 2公众号 3小程序
					}
					that.$Request.postT('/app/wxPay/payOrders', data).then(red => {
						uni.hideLoading();
						if (red.code == 0) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: red.data.timestamp,
								nonceStr: red.data.noncestr,
								package: red.data.package,
								signType: red.data.signType,
								paySign: red.data.sign,
								success: function(redd) {

									uni.hideLoading();
									uni.showToast({
										title: '支付成功',
										icon: 'none',
										duration: 2000
									})
									that.getDetail()
								},
								fail: function(err) {
									uni.hideLoading();
									that.$queue.showToast(
										'支付失败');
								}
							});
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
					// #ifdef H5
					let data = {
						ordersNo: that.ordersNo,
						classify: 2 //微信 classify 1app 2公众号 3小程序
					}
					that.$Request.postT('/app/wxPay/payOrders', data).then(red => {
						uni.hideLoading();
						if (red.code == 0) {
							that.callPay(red);
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
					// #ifdef APP
					let data = {
						ordersNo: that.ordersNo,
						classify: 1 //微信 classify 1app 2公众号 3小程序
					}
					that.$Request.postT('/app/wxPay/payOrders', data).then(red => {
						uni.hideLoading();
						if (red.code == 0) {
							that.setPayment('wxpay', JSON.stringify(red.data));
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
				} else if (that.openWay == 3) { //3零钱
					let data = {
						ordersNo: that.ordersNo
					}
					that.$Request.postT('/app/orders/payOrders', data).then(res => {
						uni.hideLoading();
						if (res.code == 0) {
							uni.showToast({
								title: '购买成功',
								icon: 'none',
								duration: 1200
							})
							uni.hideLoading();
							that.getDetail()

						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
				}

			},
			selectWay: function(id) {
				this.openWay = id;
			},
			//获取柜子详情
			getGoodsInfo() {
				// this.$queue.showLoading('加载中...')
				let data = {
					id: this.order.smallHardwareId
				}
				this.$Request.getT('/app/smallHardware/selectSmallHardwareDetails', data).then(res => {
					if (res.code == 0) {
						this.info = res.data
						this.smallGoods = res.data.smallGoods
					} else {
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			//订单详情
			getDetail() {
				let data = {
					ordersNo: this.ordersNo
				}
				this.$Request.getT('/app/orders/selectOrdersDetails', data).then(res => {
					if (res.code === 0) {
						this.order = res.data
						this.tearoom = res.data.tearoom
						this.getGoodsInfo()
					}
				});
			},
			callPay: function(response) {
				if (typeof WeixinJSBridge === "undefined") {
					if (document.addEventListener) {
						document.addEventListener('WeixinJSBridgeReady', this.onBridgeReady(response), false);
					} else if (document.attachEvent) {
						document.attachEvent('WeixinJSBridgeReady', this.onBridgeReady(response));
						document.attachEvent('onWeixinJSBridgeReady', this.onBridgeReady(response));
					}
				} else {
					this.onBridgeReady(response);
				}
			},
			onBridgeReady: function(response) {
				let that = this;
				if (!response.package) {
					return;
				}
				WeixinJSBridge.invoke(
					'getBrandWCPayRequest', {
						"appId": response.appid, //公众号名称，由商户传入
						"timeStamp": response.timestamp, //时间戳，自1970年以来的秒数
						"nonceStr": response.noncestr, //随机串
						"package": response.package,
						"signType": response.signType, //微信签名方式：
						"paySign": response.sign //微信签名
					},
					function(res) {
						if (res.err_msg === "get_brand_wcpay_request:ok") {
							// 使用以上方式判断前端返回,微信团队郑重提示：
							//res.err_msg将在用户支付成功后返回ok，但并不保证它绝对可靠。
							uni.removeStorageSync('EditAddress')
							that.paySel = false;
							uni.showLoading({
								title: '支付成功'
							});
							uni.hideLoading();
							that.getDetail()
						} else {
							that.paySel = false;
							uni.hideLoading();
						}
						WeixinJSBridge.log(response.err_msg);
					}
				);
			},
			setPayment(name, order) {
				let that = this;
				uni.requestPayment({
					provider: name,
					orderInfo: order, //微信、支付宝订单数据
					success: function(res) {
						that.paySel = false;
						uni.removeStorageSync('EditAddress')
						uni.showLoading({
							title: '支付成功'
						});
						uni.hideLoading();
						that.getDetail()
					},
					fail: function(err) {
						that.paySel = false;
						uni.hideLoading();
						// console.log(12)
					}
				});
			}
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #F7F7F7;
	}

	.status {
		width: 100%;
		height: 88rpx;
		margin-top: 20rpx;

		.status-box {
			width: 686rpx;
			height: 100%;
			background: #FFFFFF;
			border-radius: 16rpx;
			color: #333333;
			font-size: 38rpx;
			font-weight: bold;
		}
	}

	.goodsInfo {
		width: 100%;
		height: auto;
		margin-top: 20rpx;

		.goodsInfo-box {
			width: 686rpx;
			height: 100%;
			background-color: #FFFFFF;
			border-radius: 20rpx;
		}

		.goodsInfo-box-title {
			padding: 30rpx;
			color: #333333;
			font-size: 32rpx;
			font-weight: bold;

			image {
				width: 34rpx;
				height: 42rpx;
				margin-right: 20rpx;
			}
		}

		.goodsInfo-box-goods {
			padding: 30rpx;
		}

		.goodsInfo-box-goods-l {
			image {
				width: 120rpx;
				height: 120rpx;
				border-radius: 20rpx;
				margin-right: 20rpx;
			}
		}

		.goodsInfo-box-goods-l-r-t {
			color: #333333;
			font-size: 32rpx;
			font-weight: bold;
		}

		.goodsInfo-box-goods-l-r-b {
			color: #999999;
			font-size: 24rpx;
			margin-top: 10rpx;
		}

		.goodsInfo-box-goods-r {
			color: #333333;
			font-size: 38rpx;
			font-weight: 500;
		}
	}

	.goodsInfo-box-price {
		padding: 30rpx;

		.goodsInfo-box-price-l {
			color: #999999;
			font-size: 26rpx;
		}

		.goodsInfo-box-price-r {
			color: #FF130A;
			font-size: 38rpx;
			font-weight: 500;
		}
	}

	.orderInfo {
		width: 100%;
		height: auto;
		margin-top: 20rpx;

		.orderInfo-box {
			width: 686rpx;
			height: 100%;
			background-color: #FFFFFF;
			border-radius: 24rpx;
			padding: 40rpx 30rpx;
		}

		.orderInfo-box-title {
			color: #333333;
			font-size: 30rpx;
			font-weight: bold;
		}

		.orderInfo-box-item {
			width: 100%;
			margin-top: 40rpx;
			font-size: 30rpx;

			.orderInfo-box-item-l {
				color: #999999;
			}

			.orderInfo-box-item-r {
				color: #333333;
			}
		}
	}

	.btn {
		width: 100%;
		height: 128rpx;
		background-color: #FFFFFF;
		position: fixed;
		bottom: 0;
		left: 0;

		.btn-box {
			width: 686rpx;
			height: 80rpx;

		}

		.btn-box-item {
			height: 100%;
			padding: 0 40rpx;
			border-radius: 50rpx;
			background-color: #01993A;
			color: #FFFFFF;
			font-size: 32rpx;
		}
	}

	// 支付弹框
	.box {
		background: #FFFFFF;
		border-radius: 24rpx;
		// padding: 30rpx;
		margin: 30rpx;
	}

	.btnpay {
		width: 100%;
		height: 78rpx;
		background: #01993A;
		border-radius: 55rpx;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 30rpx;
		font-weight: 500;
		color: #FFFFFF;
		margin-top: 30rpx;
	}
</style>