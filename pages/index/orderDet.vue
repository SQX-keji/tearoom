<template>
	<view>
		<view class="box">
			<view class="flex">
				<image :src="order.goods_image" style="width: 185rpx;height: 145rpx;border-radius: 12rpx;"></image>
				<view class="margin-left-sm">
					<view class="text-30 text-bold">{{order.title}}</view>
					<view class="flex text-sm margin-tb-xs">
						<view>随时可退</view>
						<view class="margin-left-sm" style="color: #999999;">已售{{order.goods_sales}}</view>
					</view>
					<view class="price"><text>￥</text>{{order.package_price}}</view>
				</view>
			</view>
			<view class=" flex align-center justify-between margin-tb" @click="godata()">
				<view>开始时间</view>
				<view class="flex align-center justify-center">
					<text v-if="time.length!=0">{{time.startdata}}</text>
					<text style="color: #999999;" v-else>选择预约时间</text>
					<image src="../../static/images/my/right.png"
						style="width: 13rpx;height: 22rpx;margin-left: 15rpx;"></image>
				</view>
			</view>
			<view class=" flex align-center justify-between padding-bottom" @click="godata()">
				<view>结束时间</view>
				<view class="flex align-center justify-center">
					<text v-if="time.length!=0">{{time.enddata}}</text>
					<text style="color: #999999;" v-else>选择预约时间</text>
					<image src="../../static/images/my/right.png"
						style="width: 13rpx;height: 22rpx;margin-left: 15rpx;"></image>
				</view>
			</view>
			<view class=" flex align-center justify-between padding-bottom">
				<view>预约号码</view>
				<view class="text-sm">
					<u--input type="number" placeholder="请填写" border="none" v-model="order_phone" @change="change"
						inputAlign="right"></u--input>
				</view>
			</view>
			<view class=" flex align-center justify-between padding-bottom">
				<view>姓名</view>
				<view class="text-sm">
					<u--input placeholder="请填写" border="none" v-model="order_name" @change="change"
						inputAlign="right"></u--input>
				</view>
			</view>
			<view class="">
				<view>备注</view>
				<view class="margin-top-sm" style="border-radius:10rpx;">
					<u--textarea :confirmType="null" v-model="remarks" placeholder="选填，可以告诉我你的需求" border="none"
						height="120" maxlength="100" style="background: #f5f5f5;" :count="true" />
				</view>
			</view>
		</view>
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
							<radio color="#ff0000" :checked="openWay === item.id ? true : false" />
						</label>
					</radio-group>
				</view>
			</view>
		</view>
		<view class="taber">
			<view class="price">
				实付款：
				<!-- <view class="pp"><text>￥</text>{{order.package_price}}</view> -->
				<view class="pp" v-if="time.length!=0"><text>￥</text>{{totalprice}}</view>
				<view class="pp" v-else><text>￥</text>{{order.goods_price}}</view>
			</view>
			<view class="butt" @click="orderpay()">确定并支付</view>
		</view>
	</view>
</template>

<script>
	import moment from 'moment'
	export default {
		data() {
			return {
				order_name: '',
				order_phone: '',
				remarks: '',

				openWay: 0,
				openLists: [],
				goodsId: '',
				time: [],
				order: [],
				totalprice: 0
			}
		},
		onLoad(option) {
			if (option.goodsId) {
				this.goodsId = option.goodsId
				this.getGoodsInfo()
			}
			if (option.time) {
				this.time = JSON.parse(option.time)
				this.totalprice = option.totalprice
				
			}

			// #ifdef APP-PLUS
			this.openLists = [{
				image: '../../static/images/zhifubao.png',
				text: '支付宝支付',
				id: 1
			}, {
				image: '../../static/images/icon_weixin.png',
				text: '微信支付',
				id: 2
			}, {
				image: '../../static/images/lingqian.png',
				text: '会员卡支付',
				id: 3
			}];
			this.openWay = 1;
			// #endif

			// #ifdef MP-WEIXIN
			this.openLists = [{
				image: '../../static/images/icon_weixin.png',
				text: '微信支付',
				id: 2
			}, {
				image: '../../static/images/lingqian.png',
				text: '会员卡支付',
				id: 3
			}];
			this.openWay = 2;
			// #endif

			// #ifdef H5
			this.openLists = [{
				image: '../../static/images/zhifubao.png',
				text: '支付宝支付',
				id: 1
			}, {
				image: '../../static/images/lingqian.png',
				text: '会员卡支付',
				id: 3
			}];
			this.openWay = 1;
			// #endif
		},
		onShow() {
			uni.$on('time', data => {
				// console.log(data, '时间')
				this.time = data
				const Time = moment(data.enddata).diff(moment(data.startdata), 'hours')
				this.totalprice = this.order.goods_price * Time
				console.log(data, '时间', Time, '总价：' + this.totalprice)
			})
		},
		methods: {
			//预约时间
			godata() {
				uni.navigateTo({
					url: '/pages/index/data?goodsId=' + this.goodsId
				})
			},
			change(e) {
				console.log(e)
			},
			selectWay: function(id) {
				this.openWay = id;
			},
			//获取茶室详情
			getGoodsInfo() {
				let data = {
					goods_id: this.goodsId
				}
				this.$Request.postT('api/index/index/tea_room_info', data).then(res => {
					if (res.code == 1) {
						this.order = res.data
					} else {
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			orderpay() {
				if (this.time.length == 0) {
					uni.showToast({
						title: '请选择预约时间',
						icon: 'none'
					})
					return
				}

				if (!this.order_phone) {
					uni.showToast({
						title: '请填写预约手机号码',
						icon: 'none'
					})
					return
				}
				if (!this.order_name) {
					uni.showToast({
						title: '请填写预约姓名',
						icon: 'none'
					})
					return
				}
				if (this.openWay == 1) { //支付宝
					// #ifdef H5
					let data = {
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'alipay',
						amount: this.order.package_price,
						method: 'wap',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(red => {
						if (red.code == 1) {
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
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'alipay',
						amount: this.order.package_price,
						method: 'app',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(red => {
						if (red.code == 1) {
							this.setPayment('alipay', red.data);
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
				} else if (this.openWay == 2) { //2微信
					// #ifdef MP-WEIXIN
					let data = {
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'wechat',
						amount: this.order.package_price,
						method: 'miniapp',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(red => {
						if (red.code == 1) {
							uni.requestPayment({
								provider: 'wxpay',
								timeStamp: red.data.timestamp,
								nonceStr: red.data.noncestr,
								package: red.data.package,
								signType: red.data.signType,
								paySign: red.data.sign,
								success: function(redd) {
									uni.showLoading({
										title: '支付成功'
									});
									uni.hideLoading();
									// setTimeout(function() {
									// 	uni.redirectTo({
									// 		url: '/my/order/index'
									// 	})
									// }, 1000)
								},
								fail: function(err) {
									uni.hideLoading();
									this.$queue.showToast(
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
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'wechat',
						amount: this.order.package_price,
						method: 'wap',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(red => {
						if (red.code == 1) {
							this.callPay(red);
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
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'alipay',
						amount: this.order.package_price,
						method: 'app',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(red => {
						if (red.code == 1) {
							that.setPayment('wxpay', JSON.stringify(red.data));
						} else {
							uni.showToast({
								title: red.msg,
								icon: 'none'
							})
						}
					})
					// #endif
				} else if (this.openWay == 3) { //3零钱
					let data = {
						goods_id: this.goodsId,
						str_time: this.time.startdata,
						end_time: this.time.enddata,
						order_name: this.order_name,
						order_phone: this.order_phone,
						pay_type: 'lingqian',
						amount: this.order.package_price,
						method: '',
						remarks: this.remarks,
						coupon_id: ''
					}
					this.$Request.postT('/api/index/user/tijiao_order', data).then(res => {
						if (res.code == 1) {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 1200
							})
							uni.hideLoading();
							setTimeout(function() {
								uni.setStorageSync('orderIndex', Number(0))
								uni.switchTab({
									url: '/pages/order/index'
								})
							}, 1000)

						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
				}

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
							setTimeout(function() {
								uni.setStorageSync('orderIndex', Number(0))
								uni.switchTab({
									url: '/pages/order/index'
								})
							}, 1000)
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
						setTimeout(function() {
							uni.setStorageSync('orderIndex', Number(0))
							uni.switchTab({
								url: '/pages/order/index'
							})
						}, 1000)
					},
					fail: function(err) {
						that.paySel = false;
						uni.hideLoading();
						console.log(12)
					}
				});
			}
		}
	}
</script>

<style lang="less">
	/deep/.uni-input-placeholder {
		font-size: 28rpx !important;
		color: #CCCCCC !important;
	}

	.box {
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 30rpx;
		margin: 30rpx;
	}

	.price {
		font-size: 38rpx;
		font-weight: bold;
		color: #FF1536;

		text {
			font-size: 24rpx;
		}
	}

	.taber {
		background: #FFFFFF;
		padding: 20rpx 30rpx;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		z-index: 99;
		display: flex;
		align-items: center;
		justify-content: space-between;

		.price {
			display: flex;
			align-items: center;
			color: #333333;
			font-size: 24rpx;
			font-weight: 500;

			.pp {
				color: #FF1536;
				font-size: 48rpx;
				font-weight: bold;

				text {
					font-size: 30rpx;
				}
			}

		}

		.butt {
			width: 260rpx;
			height: 78rpx;
			background: linear-gradient(-90deg, #74543E 0%, #442916 100%);
			border-radius: 55rpx;
			text-align: center;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 30rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
		}
	}
</style>