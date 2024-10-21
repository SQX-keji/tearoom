<template>
	<view>
		<view class="top_view">
			<view class="userbox">
				<view>
					<image :src="avatar" style="width: 100rpx;height: 100rpx;border-radius: 100rpx;"
						@click="goNav('/pages/my/userinfo')"></image>
				</view>
				<view class="margin-left-sm" v-if="!isLogin">
					<view class="u-font-18  text-bold" @click="goNav('/pages/my/userinfo')">
						{{userName}}
					</view>
				</view>
				<view v-else class="text-xl margin-left-sm text-bold" style="color: #FFFFFF;"
					@click="goLogin('/pages/public/login')">
					登录
				</view>
			</view>
			<!-- #ifdef MP-WEIXIN -->
			<image src="https://qipaishi.xianmxkj.com/upload/mytopimg.png"
				style="width: 189rpx;height: 172rpx;position: absolute;right: 50rpx;top: 10rpx;"></image>
			<!-- #endif -->
			<!-- #ifndef MP-WEIXIN -->
			<image src="https://qipaishi.xianmxkj.com/upload/mytopimg.png"
				style="width: 189rpx;height: 172rpx;position: absolute;right: 50rpx;top: -30rpx;"></image>
			<!-- #endif -->

			<view class="top_centerView">
				<view class="heddle" v-if="shangxian != '否'">
					<view class="text-center" @click="goNav('/my/youhuijuan/index')">
						<view class="num">{{yhjCount?yhjCount:'0'}}</view>
						<view>优惠劵</view>
					</view>
					<view class="text-center" @click="goNav('/my/tuangoujuan/index')">
						<view class="num">{{tgjCount?tgjCount:'0'}}</view>
						<view>团购劵</view>
					</view>
					<view class="text-center" @click="goNav('/my/wallet/money')">
						<view class="num">{{userMoney ? userMoney : '0'}}</view>
						<view>会员卡</view>
					</view>
					<view class="text-center" @click="goNav('/my/jifen/index')">
						<view class="num">{{userIntegral?userIntegral:'0'}}</view>
						<view>我的积分</view>
					</view>
				</view>
				<view class="order_name">预约订单</view>
				<view class="flex justify-between align-center margin-top-xs">
					<view class="bb" @click="goOrder(1)">
						<image src="../../static/images/my/dfk.png" mode=""></image>
						<view>待支付</view>
						<u-badge type="error" :absolute="true" :offset="[0,0]" max="99" :value="dzfCount"></u-badge>
					</view>
					<view class="bb" @click="goOrder(2)">
						<image src="../../static/images/my/dhx.png" mode=""></image>
						<view>待使用</view>
						<u-badge type="error" :absolute="true" :offset="[0,0]" max="99" :value="dsyCount"></u-badge>
					</view>
					<view class="bb" @click="goOrder(3)">
						<image src="../../static/images/my/ywc.png" mode=""></image>
						<view>使用中</view>
						<u-badge type="error" :absolute="true" :offset="[0,0]" max="99" :value="syzCount"></u-badge>
					</view>
					<view class="order_btn" @click="goOrder(0)">全部订单</view>
				</view>
			</view>
		</view>

		<view class="flex justify-between" style="width: 686rpx;margin: 0 auto;margin-top: 30rpx;"
			v-if="shangxian != '否'">
			<!-- classify 1美团  2抖音 -->
			<image src="https://qipaishi.xianmxkj.com/upload/dyyq.png" @click="goNav('/pages/index/yanjuan?classify=2')"
				style="width: 215rpx;height: 180rpx;"></image>
			<image src="https://qipaishi.xianmxkj.com/upload/mtyq.png" @click="goNav('/pages/index/yanjuan?classify=1')"
				style="width: 215rpx;height: 180rpx;"></image>
			<image src="https://qipaishi.xianmxkj.com/upload/sqrz.png" @click="goNav('/my/setting/ruzhu')"
				style="width: 215rpx;height: 180rpx;"></image>
		</view>


		<view class="headbot">
			<!-- <view class="userbox">
				<view class=" " style="margin-left: 10upx;">
					<image :src="avatar" style="width: 100rpx;height: 100rpx;border-radius: 100rpx;"
						@click="goNav('/pages/my/userinfo')"></image>
				</view>
				<view class="margin-left-xs" v-if="!isLogin">
					<view class="u-font-18  text-bold" @click="goNav('/pages/my/userinfo')">
						{{userName}}
					</view>
				</view>
				<view v-else class="text-xl margin-left-sm text-bold" @click="goLogin('/pages/public/login')">
					登录
				</view>
			</view>
			<view class="heddle" v-if="shangxian === '否'">
				<view class="text-center" @click="goNav('/my/youhuijuan/index')">
					<view class="num">{{coupon_num?coupon_num:'0'}}</view>
					<view>优惠劵</view>
				</view>
				<view class="text-center" @click="goNav('/my/tuangoujuan/index')">
					<view class="num">{{team_coupon_num?team_coupon_num:'0'}}</view>
					<view>团购劵</view>
				</view>
				<view class="text-center" @click="goNav('/my/wallet/money')">
					<view class="num">{{money}}</view>
					<view>我的钱包</view>
				</view>
				<view class="text-center" @click="goNav('/my/jifen/index')">
					<view class="num">{{score?score:'0'}}</view>
					<view>我的积分</view>
				</view>
			</view>

			<view class="orderbox" v-if="shangxian === '否'">
				<view class="flex justify-between align-center padding-lr " @click="goOrder(0)">
					<view>我的订单</view>
					<image src="../../static/images/my/right.png" style="width: 16rpx;height: 28rpx;"></image>
				</view>
				<view class="margin-tb-sm" style="background: #E6E6E6;width: 100%;height: 1rpx;"></view>
				<view class="flex justify-around">
					<view class="bb" @click="goOrder(1)">
						<image src="../../static/images/my/dfk.png" mode=""></image>
						<view>待支付</view>
						<view class="numbox" v-if="order_pay_num">{{order_pay_num?order_pay_num:'0'}}</view>
					</view>
					<view class="bb" @click="goOrder(2)">
						<image src="../../static/images/my/dhx.png" mode=""></image>
						<view>待使用</view>
						<view class="numbox" v-if="order_receive_num">{{order_receive_num?order_receive_num:'0'}}</view>
					</view>
					<view class="bb" @click="goOrder(3)">
						<image src="../../static/images/my/ywc.png" mode=""></image>
						<view>使用中</view>
						<view class="numbox" v-if="order_useing_num">{{order_useing_num?order_useing_num:'0'}}</view>
					</view>
					<view class="bb" @click="goOrder(4)">
						<image src="../../static/images/my/dpj.png" mode=""></image>
						<view>待评价</view>
						<view class="numbox" v-if="order_evaluate_num">{{order_evaluate_num?order_evaluate_num:'0'}}
						</view>
					</view>
					<view class="bb" @click="goOrder(5)">
						<image src="../../static/images/my/yqx.png" mode=""></image>
						<view>已完成</view>
					</view>
				</view>
			</view> -->


			<view class="orderbox">
				<view class="title">常用工具</view>
				<view class="flex flex-wrap margin-top-xs">
					<!-- /my/cabinet/cabinet?id=1 -->
					<view v-if="tearoomId && tearoomId!=0" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/my/guiziManage/guiziList?tearoomId='+tearoomId)">
						<image src="../../static/images/my/vipMoney.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">柜子</view>
					</view>
					<view v-if="isShop" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goShopCenter()">
						<image src="../../static/images/my/shopCenter.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">商家中心</view>
					</view>
					<view class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/pages/my/invitationUser')">
						<image src="../../static/images/my/share.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">分享好友</view>
					</view>
					<view v-if="shangxian != '否'" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/my/team/team')">
						<image src="../../static/images/my/team.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">我的团队</view>
					</view>
					<!-- #ifdef MP-WEIXIN -->
					<view v-if="SelKeFu==3" style="width: 25%;">
						<button type="default" open-type="contact">
							<image src="../../static/images/my/kefu.png" style="width: 50upx;height: 50upx;">
							</image>
							<view style="color: #666666;margin-top: -6px;">联系客服</view>
						</button>
					</view>

					<view class="text-center margin-bottom-sm" style="width: 25%;" v-else @click="selectKeFu()">
						<image src="../../static/images/my/kefu.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">联系客服</view>
					</view>
					<!-- #endif -->

					<!-- #ifndef MP-WEIXIN -->
					<view class="text-center margin-bottom-sm" style="width: 25%;" @click="selectKeFu()">
						<image src="../../static/images/my/kefu.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">联系客服</view>
					</view>
					<!-- #endif -->


					<view class="text-center margin-bottom-sm" style="width: 25%;" @click="goNav('/my/setting/baojie')"
						v-if="tearoomId && tearoomId!=0">
						<image src="../../static/images/my/fx.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">保洁</view>
					</view>
					<view class="text-center margin-bottom-sm" style="width: 25%;" @click="goNav('/my/feedback/index')">
						<image src="../../static/images/my/bianji.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">意见反馈</view>
					</view>
					<view class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/my/feedbackIndex/feedbackIndex')">
						<image src="../../static/images/my/help.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">帮助中心</view>
					</view>
					<!-- <view v-if="shangxian === '否'" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/my/setting/ruzhu')">
						<image src="../../static/images/my/ruzhu.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">申请入驻</view>
					</view>
					<view v-if="shangxian === '否'" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/pages/index/yanjuan')">
						<image src="../../static/images/my/dy.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">抖音验劵</view>
					</view>
					<view v-if="shangxian === '否'" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/pages/index/yanjuan')">
						<image src="../../static/images/my/mt.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">美团验劵</view>
					</view> -->
					<view v-if="shangxian != '否'" class="text-center margin-bottom-sm" style="width: 25%;"
						@click="goNav('/my/setting/mypl')">
						<image src="../../static/images/my/pj.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">我的评价</view>
					</view>
					<view class="text-center margin-bottom-sm" style="width: 25%;" @click="goOut()">
						<image src="../../static/images/my/set.png"
							style="width: 50upx;height: 50upx;margin-top: 10upx;">
						</image>
						<view style="color: #666666;margin-top: 10upx;">退出登录</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 底部banner -->
		<view class="swiper flex align-center justify-center" v-if="swiperList.length>0 && shangxian!='否'">
			<view class="swiper-box">
				<swiper class="swiper-box-swiper" :circular="true" :autoplay="true" interval="2500" duration="800">
					<swiper-item @click="goNavSwiper(item.url)" v-for="(item,index) in swiperList" :key="index">
						<image style="width: 100%;height: 100%;" :src="item.imageUrl" mode="widthFix" class="radius">
						</image>
					</swiper-item>
				</swiper>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				avatar: '../../static/logo.png',
				isLogin: false,
				userName: '匿名',
				XCXIsSelect: '否',
				userId: '',
				token: '',
				// money: 0,
				// coupon_num: 0,
				// score: 0,
				user_type: '',
				// team_coupon_num: 0,
				// order_pay_num: 0, //待付款数量
				// order_receive_num: 0, //待使用数量
				// order_evaluate_num: 0, //待评价数量
				// order_finish_num: 0, //已完成数量
				// order_useing_num: 0, //使用中数量
				swiperList: [], //底部banner图
				arr: [], //通知订阅
				showModal1: true,
				shangxian: '否',
				tgjCount: 0, //团购劵
				userIntegral: 0, // 我的积分
				userMoney: 0, // 我的钱包
				yhjCount: 0, //优惠劵
				tearoomId: '',
				SelKeFu: 1,
				isShop: false, //是否是商家
				dsyCount: 0, //待使用
				dzfCount: 0, //待支付
				syzCount: 0, //使用中
				tuiguang: '',
				backgroundImage: '',
			}
		},
		onLoad() {

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
		onShow() {
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
			this.SelKeFu = this.$queue.getData('SelKeFu')
			if (uni.getStorageSync('shangxian')) {
				this.shangxian = uni.getStorageSync('shangxian')
			}
			this.token = uni.getStorageSync('token')
			this.userId = uni.getStorageSync('userId')
			if (this.token) {
				this.isLogin = false
				this.getUserInfo()
				this.getnumlist()
				this.getBannerList()
				this.selectOrdersStatusCountByUserId()
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
					// this.openMsg()
					// #endif
				}
			} else {
				this.isLogin = true
				this.money = 0
				this.coupon_num = 0
				this.score = 0
				this.userName = '匿名'
				this.avatar = '../../static/logo.png'
				// this.team_coupon_num = 0
				this.tearoomId = ''
				this.tgjCount = 0
				this.userIntegral = 0
				this.userMoney = 0
				this.yhjCount = 0
				this.isShop = false
				this.dsyCount = 0 //待使用
				this.dzfCount = 0 //待支付
				this.syzCount = 0 //使用中
				// this.order_pay_num = 0 //待付款数量
				// this.order_receive_num = 0 //待使用数量
				// this.order_evaluate_num = 0 //待评价数量
				// this.order_finish_num = 0 //已完成数量
				// this.order_useing_num = 0 //使用中数量
			}
		},
		methods: {
			goOut() {
				uni.showModal({
					title: '提示',
					content: '确定退出登录吗？',
					success: function(res) {
						if (res.confirm) {
							console.log('用户点击确定');
							uni.removeStorageSync('userName')
							uni.removeStorageSync('avatar')
							uni.removeStorageSync('userId')
							uni.removeStorageSync('token')
							uni.removeStorageSync('phone')
							uni.removeStorageSync('zhiFuBaoName')
							uni.removeStorageSync('zhiFuBao')
							uni.removeStorageSync('invitationCode')
							uni.removeStorageSync('unionId')
							uni.removeStorageSync('openId')
							uni.removeStorageSync('isVIP')
							uni.showToast({
								title: '退出成功！',
								icon: 'none'
							})
							setTimeout(function() {
								uni.navigateBack()
							}, 1000)
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				})
			},
			//获取订单徽标
			selectOrdersStatusCountByUserId() {
				this.$Request.getT('/app/orders/selectOrdersStatusCountByUserId').then(res => {
					if (res.code == 0) {
						this.dsyCount = res.data.dsyCount //待使用
						this.dzfCount = res.data.dzfCount //待支付
						this.syzCount = res.data.syzCount //使用中

					}
				})
			},
			//去商家中心
			goShopCenter() {
				if (uni.getStorageSync('token')) {
					let data = {
						page: 1,
						limit: 10,
						userId: uni.getStorageSync('userId')
					}
					this.$Request.getT('/app/tearoom/selectTearoomList', data).then(res => {
						if (res.code == 0) {
							if (res.data && res.data.list && res.data.list.length > 0) {
								uni.navigateTo({
									url: '/my/shop/shopIndex'
								})
							} else {
								uni.showToast({
									title: '请绑定棋牌室',
									icon: 'none'
								})
							}
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			//获取轮播图
			getBannerList() {
				this.$Request.get("/app/banner/selectBannerList", {
					classify: 8
				}).then(res => {
					if (res.code == 0) {
						this.swiperList = res.data
					}
				});
			},
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
			//联系客服
			selectKeFu() {
				let SelKeFu = this.$queue.getData('SelKeFu');
				if (SelKeFu + '' == '1') { //手机号
					uni.makePhoneCall({
						phoneNumber: uni.getStorageSync('kefuPhone')
					});
				} else if (SelKeFu + '' == '2') { //企业微信
					let that = this
					// #ifdef MP-WEIXIN
					wx.openCustomerServiceChat({
						extInfo: {
							url: that.$queue.getData('kefu')
						},
						corpId: that.$queue.getData('kefuAppId'),
						success(res) {}
					})
					// #endif
					// #ifdef H5
					window.location.href = that.$queue.getData('kefu');
					// #endif
					// #ifdef APP
					let kefu = that.$queue.getData('kefu')
					console.log(kefu)
					plus.runtime.openURL(kefu, function(res) {});
					// #endif
				} else if (SelKeFu + '' == '4') { //客服二维码页面
					uni.navigateTo({
						url: '/my/setting/customer'
					})
				} else if (SelKeFu + '' == '3') { //微信小程序客服  H5跳转二维码客服
					// #ifndef MP-WEIXIN
					uni.navigateTo({
						url: '/my/setting/customer'
					})
					// #endif
				}
			},
			/**
			 * @param {String} url 跳转链接
			 * 轮播图跳转
			 */
			goNavSwiper(url) {
				// 判断是否是应用内链接
				if (url.indexOf('/pages/') !== -1 || url.indexOf('/my/') !== -1 || url.indexOf('/main/') !== -1) {
					if (uni.getStorageSync('token')) {
						uni.navigateTo({
							url
						});
					} else {
						uni.navigateTo({
							url: '/pages/public/login'
						})
					}
				} else if (url.indexOf('http') !== -1) {
					//#ifndef H5
					uni.navigateTo({
						url: '/pages/webView/webView?url=' + url
					})
					//#endif
					//#ifdef H5
					window.location.href = url;
					//#endif
				} else if (url.indexOf('客服') !== -1) {
					this.selectKeFu()
				}
			},
			goOrder(index) {
				let token = uni.getStorageSync('token')
				if (token) {
					// #ifdef MP-WEIXIN
					if (uni.getStorageSync('sendMsg')) {
						uni.requestSubscribeMessage({
							tmplIds: this.arr,
							success(re) {
								// console.log(re,'**********')
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
					// #endif
					uni.setStorageSync('orderIndex', Number(index))
					uni.switchTab({
						url: '/pages/order/index'
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			goNav(url) {
				let token = uni.getStorageSync('token')
				if (token) {
					// #ifdef MP-WEIXIN
					if (uni.getStorageSync('sendMsg')) {
						uni.requestSubscribeMessage({
							tmplIds: this.arr,
							success(re) {
								// console.log(re,'**********')
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
					// #endif
					uni.navigateTo({
						url
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			goLogin(url) {
				uni.navigateTo({
					url
				})
			},
			// 获取优惠劵   团购劵  我的钱包  我的积分
			getnumlist() {
				this.$Request.get("/app/user/selectUserDetailsCount").then(res => {
					if (res.code == 0) {
						this.tgjCount = res.data.tgjCount //团购劵
						this.userIntegral = res.data.userIntegral //我的积分
						this.userMoney = res.data.userMoney //我的钱包
						this.yhjCount = res.data.yhjCount //优惠劵
					}
				});
			},
			// 获取用户信息
			getUserInfo() {
				this.$Request.get("/app/user/selectUserById").then(res => {
					if (res.code == 0) {
						// this.zhiRate = res.data.zhiRate ? res.data.zhiRate : 0;
						// uni.setStorageSync('zhiRate', res.data.zhiRate)
						// this.consortiaId = res.data.consortiaId
						// uni.setStorageSync('consortiaId', res.data.consortiaId)
						// if (this.consortiaId) {
						// 	this.getUserTd()
						// }
						this.tearoomId = res.data.tearoomId
						this.userName = res.data.userName
						this.invitationCode = res.data.invitationCode
						this.avatar = res.data.avatar ? res.data.avatar : '../../static/logo.png'
						if (res.data && res.data.isShop) {
							if (Number(res.data.isShop) == 1) {
								this.isShop = true
							} else {
								this.isShop = false
							}
						} else {
							this.isShop = false
						}

						uni.setStorageSync('avatar', res.data.avatar)
						uni.setStorageSync('invitationCode', res.data.invitationCode)
						uni.setStorageSync('zhiFuBao', res.data.zhiFuBao)
						uni.setStorageSync('zhiFuBaoName', res.data.zhiFuBaoName)
					}
				});
			},
		}
	}
</script>

<style lang="less">
	page {
		background: #F7F5F6;
	}

	button::after {
		border: none;
		background-color: none;
	}

	button {
		display: flex;
		align-items: center;
		position: relative;
		display: block;
		margin-left: auto;
		margin-right: auto;
		padding-left: 0px;
		padding-right: 0px;
		box-sizing: border-box;
		text-decoration: none;
		// line-height: 96rpx;
		overflow: hidden;
		color: #FFFFFF;
		/* background-color: #fff; */
		background-color: rgba(255, 255, 255, 0) !important;
		font-size: 28rpx;
		width: 100%;
		height: 100%;
		color: #333333;
	}

	.top_view {
		width: 686rpx;
		height: 505rpx;
		background: #01993A;
		border-radius: 24rpx;
		margin: 0 auto;
		margin-top: 40rpx;
		padding: 30rpx 20rpx;

		.top_centerView {
			width: 646rpx;
			height: 326rpx;
			background: #FFFAE7;
			border-radius: 24rpx;
			padding: 20rpx 30rpx;
			margin: 0 auto;
			margin-top: 20rpx;

			.order_name {
				font-size: 28rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #1A1A1A;
				margin-top: 30rpx;
			}
		}

		.order_btn {
			width: 180rpx;
			height: 80rpx;
			background: #01993A;
			border-radius: 12rpx;
			text-align: center;
			line-height: 76rpx;
			font-size: 28rpx;
			font-family: PingFang SC;
			font-weight: bold;
			color: #FFFFFF;
		}

		.top_centerxian {
			width: 585rpx;
			height: 1rpx;
			border: 1rpx solid #AAD6BA;
			margin-top: 30rpx;
		}

		.userbox {
			display: flex;
			align-items: center;
			color: #FFFFFF;
			padding: 0rpx 20rpx;
		}

		.bb {
			text-align: center;
			font-size: 24rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #1A1A1A;
			position: relative;

			image {
				width: 46rpx;
				height: 46rpx;
			}
		}

		.heddle {
			display: flex;
			align-items: center;
			justify-content: space-between;
			font-size: 24rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #01993A;
			padding-bottom: 30rpx;
			border-bottom: 1rpx solid #AAD6BA;

			.num {
				font-size: 38rpx;
				font-family: PingFang SC;
				font-weight: 800;

			}
		}
	}


	.swiper {
		width: 100%;
		height: 270rpx;

		.swiper-box {
			width: 686rpx;
			height: 100%;
			border-radius: 10rpx;
		}

		.swiper-box-swiper {
			width: 100%;
			height: 100%;
		}
	}

	.headbot {

		.orderbox {
			background: #FFFFFF;
			border-radius: 24rpx;
			padding: 30rpx 0;
			margin: 30rpx;

			.bb {
				text-align: center;
				font-size: 26rpx;
				position: relative;

				image {
					width: 52rpx;
					height: 52rpx;
				}
			}

			.numbox {
				background: red;
				color: #FFFFFF;
				position: absolute;
				top: -5rpx;
				right: -18rpx;
				z-index: 99;
				border-radius: 50%;
				padding: 2rpx 8rpx;
			}

			.title {
				font-size: 32rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #333333;
				padding: 0 30rpx;
			}
		}
	}
</style>