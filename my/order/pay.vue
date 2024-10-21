<template>
	<view class="" style="padding-bottom: 80px;">
		<!-- <uv-skeleton title :loading="loading" rows="6" rowsHeight="120" rowsWidth="100%"> -->
		<view
			style="width: 750rpx;background: linear-gradient(-48deg, #01993A 0%, #2FBB63 100%);height: 610rpx;padding: 30rpx;">
			<view class="statusview_Name" v-if="order.status==1">待支付...</view>
			<view class="statusview_Name" v-if="order.status==2">待使用...</view>
			<view class="statusview_Name" v-if="order.status==3">使用中...</view>
			<view class="statusview_Name" v-if="order.status==4">已完成...</view>
			<view class="statusview_Name" v-if="order.status==6">待评价...</view>
			<view class="statusview_Name" v-if="order.status==5">已取消...</view>
			<view class="statusview_Name" v-if="order.status==7">已退款...</view>
			<view class="text-sm margin-top-xs" style="color: #FFFFFF;" v-if="order.status==3">订单预订成功，祝你使用愉快</view>

			<view
				style="width: 686rpx;height: 368rpx;background: #FFFFFF;border-radius: 24rpx;margin: 0 auto;margin-top: 30rpx;">
				<view class="towbox" style="padding: 30rpx 30rpx 0rpx;">
					<image :src="order.goodsCoverImage"></image>
					<view class="margin-left-sm">
						<view class="tit">{{order.goodsName}}</view>
						<view class="time">
							<view style="flex: 1;text-align: center;">
								<view class="time_date">{{strTime}}</view>
								<view class="time_riqi">{{startDate}}</view>
							</view>
							<view style="flex: 1;text-align: center;">
								<view class="times">共{{timeCha}}小时</view>
							</view>
							<view style="flex: 1;text-align: center;">
								<view class="time_date">{{endTime}}</view>
								<view class="time_riqi">{{endDate}}</view>
							</view>
						</view>
						<view class="price">
							{{order.status==1 || order.status == 5 ?'应付':'实付'}}:￥<text>{{order.payMoney}}</text>
						</view>
					</view>
				</view>
				<view class="info-box-address" v-if="order.tearoom">
					<image src="../../static/images/index/addressBg.png"></image>
					<view class="info-box-address-s flex align-center justify-between">
						{{order.tearoom.tearoomAddress}}
						<view class="flex align-center justify-between">
							<image src="../../static/images/index/add.png"
								@click="openMap(order.tearoom.tearoomLatitude, order.tearoom.tearoomLongitude, order.tearoom.tearoomAddress)">
							</image>
							<image src="https://qipaishi.xianmxkj.com/upload/dianhua.png"
								@click="bindPhone(order.tearoom.tearoomPhone)">
							</image>
						</view>
					</view>
				</view>
			</view>
		</view>

		<!-- <view class="box ">
			<view class="flex align-center justify-between">
				<view class="towbox" style="width: 80%;">
					<image :src="order.image"></image>
					<view class="margin-left-sm" style="width:75%;">
						<view class="tit">{{order.title}}</view>
						<view class="price">
							{{order.status==1 || order.status == 6 ?'应付':'实付'}}:￥<text>{{order.payment}}</text>
						</view>
					</view>
				</view>
			</view>
			<view class=" flex align-center justify-between margin-tb">
				<view>开始时间</view>
				<view style="color: #666666;">{{order.str_time}}</view>
			</view>
			<view class=" flex align-center justify-between ">
				<view>结束时间</view>
				<view style="color: #666666;">{{order.end_time}}</view>
			</view>
		</view> -->
		<!-- <view class="box " v-if="order.store_info">
			<view class="flex align-center justify-between"
				@click="openMap(order.store_info.store_lat, order.store_info.store_lng, order.store_info.store_address)">
				<view>
					<view class="titl">{{order.store_name}}</view>
					<view class="text-sm margin-top-xs" style="color: #999999;">{{order.store_info.store_address}}
					</view>
				</view>
				<image src="../../static/images/my/right.png" style="width: 16rpx;height: 28rpx;"></image>
			</view>

			<view class="line"></view>
			<view class="text-center text-24 text-bold" @click="bindPhone(order.store_info.store_phone)">联系商家</view>
		</view> -->
		<view :class="order.status != 3 ? 'center_view' :'centers_view'">
			<view class="box  flex align-center justify-between" @click="goswitch()" v-if="order.openDoor==1">
				<view class="flex align-center justify-between">
					<image src="../static/kg.png" style="width:55rpx;height:61rpx;"></image>
					<view class="margin-left-xs">
						<view class="titl">开关操作</view>
						<view class="text-sm" style="color: #999999;">点击进入开关启动</view>
					</view>
				</view>
				<image src="../../static/images/my/right.png" style="width: 16rpx;height: 28rpx;"></image>
			</view>

			<view class="cx_view" v-if="order.goods &&order.goods.roomPwd">
				<view
					style="background: #FFFFFF;padding: 20rpx;border-top-left-radius: 24rpx;border-top-right-radius: 24rpx;">
					<view class="titl">茶室信息<text
							style="color:red;font-size:20rpx;margin-left: 10rpx;">提前{{hour}}分钟展示</text></view>
				</view>
				<view class="flex justify-between">
					<view class="cx_item">
						<view class="cx_name">开门密码</view>
						<view style="width:100%;height:auto;color: #029A3B;word-break:break-all;padding:20rpx;">
							{{order.goods.roomPwd?order.goods.roomPwd:''}}
						</view>
					</view>

					<view class="cx_item">
						<view class="cx_name">WIFI密码</view>
						<view v-if="order.goods.roomWifi" @click="copyWifi(order.goods.roomWifi)"
							class="flex align-center justify-center"
							style="width:100%;height:auto;color: #999999;word-break:break-all;padding:20rpx;">
							{{order.goods.roomWifi}}
							<u-icon name="order" color="#999999" size="20"></u-icon>
						</view>
					</view>

					<view class="cx_item">
						<view class="cx_name">房间号码</view>
						<view
							style="width:100%;height:auto;color: #029A3B;margin-top: 10rpx;word-break:break-all;padding:20rpx;">
							{{order.goods.roomNo?order.goods.roomNo:''}}
						</view>
					</view>
				</view>
			</view>
			<!-- <view class="box flex" v-if="order.tearoom_info">
				<view class="titl margin-bottom">茶室信息<text
						style="color:red;font-size:2px;margin-left: 10rpx;">提前{{hour}}分钟展示</text></view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">开门密码</view>
					<view class="" style="color: #999999;">{{order.door_pwd?order.door_pwd:''}}</view>
				</view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">WIFI密码</view>
					<view v-if="order.wifi_pwd" @click="copyWifi(order.wifi_pwd)" class="flex align-center"
						style="color: #999999;">
						{{order.wifi_pwd}}
						<u-icon name="order" color="#999999" size="20"></u-icon>
					</view>
				</view>
				<view class="flex align-center justify-between ">
					<view class="">房间号码</view>
					<view class="" style="color: #999999;">{{order.room_num?order.room_num:''}}</view>
				</view>
			</view> -->
			<view class="box" style="padding-bottom: 50rpx;">
				<view class="titl margin-bottom">个人信息</view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">姓名</view>
					<view class="" style="color: #999999;">{{order.userName}}</view>
				</view>
				<view class="flex align-center justify-between ">
					<view class="">联系方式</view>
					<view class="" style="color: #999999;">{{order.phone}}</view>
				</view>
			</view>
			<!-- <view class="box ">
				<view class="titl margin-bottom">温馨提示</view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">有效期</view>
					<view class="" style="color: #999999;">{{order.expired_time}}</view>
				</view>
				<view class="flex align-center justify-between margin-bottom" v-if="order.store_info">
					<view class="">使用时间</view>
					<view class="" style="color: #999999;">{{order.store_info.store_time}}</view>
				</view>
				<view class="flex align-center justify-between ">
					<view class="">使用规则</view>
					<view class="" style="color: #999999;">仅限购买一次 多次无效</view>
				</view>
			</view> -->
			<view class="boxs ">
				<view class="flex justify-between">
					<image src="https://qipaishi.xianmxkj.com/upload/qianlian.png"
						style="width: 16rpx;height: 76rpx;margin-top: -100rpx;"></image>
					<image src="https://qipaishi.xianmxkj.com/upload/qianlian.png"
						style="width: 16rpx;height: 76rpx;margin-top: -100rpx;"></image>
				</view>
				<view class="titl margin-bottom">订单信息</view>

				<view class="flex align-center justify-between margin-bottom"
					v-if="order.add_hours && order.add_hours != 0">
					<view class="">加时</view>
					<view class="" style="color: #999999;">{{order.add_hours}}小时</view>
				</view>
				<view class="flex align-center justify-between margin-bottom"
					v-if="order.add_money && order.add_money != 0">
					<view class="">加时金额</view>
					<view class="" style="color: #999999;">￥{{order.add_money}}</view>
				</view>
				<view class="flex align-center justify-between margin-bottom"
					v-if="order.couponMoney && order.couponMoney != 0&&order.couponUser&&order.couponUser.type==2">
					<view class="">优惠劵</view>
					<view class="" style="color: #999999;">-￥{{order.couponMoney}}</view>
				</view>
				<view class="flex align-center justify-between margin-bottom"
					v-if="order.couponUser&&order.couponUser.type==1">
					<view class="">团购劵</view>
					<view class="" style="color: #999999;">-￥{{order.couponMoney}}</view>
				</view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">商品总价</view>
					<view class="" style="color: #999999;">￥{{order.payMoney}}</view>
				</view>
				<!-- 支付方式 1微信 2支付宝 3零钱 -->
				<view class="flex align-center justify-between margin-bottom">
					<view class="">支付方式</view>
					<view class="" style="color: #999999;" v-if="order.payWay==3">会员卡支付</view>
					<view class="" style="color: #999999;" v-if="order.payWay==2">支付宝支付</view>
					<view class="" style="color: #999999;" v-if="order.payWay==1">微信支付</view>
				</view>
				<view class="flex align-center justify-between margin-bottom">
					<view class="">订单编号</view>
					<view class="flex align-center" style="color: #999999;">
						{{order.ordersNo}}
						<u-icon name="file-text" @click="copyOrder(order.ordersNo)" color="#999999" size="24"></u-icon>
					</view>
				</view>
				<view class="flex align-center justify-between ">
					<view class="">下单时间</view>
					<view class="" style="color: #999999;">{{order.createTime}}</view>
				</view>
				<view class=" margin-top" v-if="order.remark">
					<view class="" style="letter-spacing: 30rpx;">备注</view>
					<view class="" style="color: #999999;">{{order.remark}}</view>
				</view>

			</view>
		</view>
		<view class="taber " v-if="order.status==1||order.status==2||order.status==5||order.status==3">
			<view class="btn" v-if="order.status==1||order.status==2" @click="bindorderOff">取消订单</view>
			<view class="btn" v-if="order.status==5" @click="bindorderdete(order.ordersId)">删除订单</view>
			<view class="btnn" @click="showpay = true" v-if="order.status==1">立即支付</view>
			<view class="btn" v-if="order.status==3" @click="hujiao" @>呼叫保洁</view>
			<view class="btnn" @click="showHour = true" v-if="order.status==3">加时</view>
			<view class="btn" @tap.stop="bindorderOffs(item)" v-if="order.status==3">提前结束
			</view>

		</view>
		<!-- </uv-skeleton> -->
		<!-- 支付弹框 -->
		<u-popup :show="showpay" @close="showpay = false">
			<view
				style="width: 100%;text-align: center;font-size:38rpx;font-weight: bold;margin-top:15rpx;margin-bottom: 50rpx;">
				选择支付方式
			</view>
			<view style="display: flex;height: 100upx;align-items: center;padding: 20upx 30rpx;"
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
			<view
				style="width: 690rpx;height: 80rpx;background: #01993A;color:#FFFFFF;text-align: center;line-height: 80rpx;border-radius: 50rpx;margin: 30rpx;"
				@tap="orderpay()">确认支付</view>
		</u-popup>

		<!-- 加时弹框 -->
		<u-popup :show="showHour" @close="closeHour" :closeable="true">
			<view
				style="width: 100%;text-align: center;font-size:38rpx;font-weight: bold;margin-top:30rpx;margin-bottom: 50rpx;">
				加时
			</view>
			<view class="padding-lr flex align-center justify-between padding-bottom" @click="godata()">
				<view>结束时间</view>
				<view class="text-26">
					<text style="color: #333333;" v-if="enddata">{{enddata?enddata:'选择结束时间'}}</text>
					<text style="color: #999999;" v-else>选择结束时间</text>
					<image src="../../static/images/my/right.png"
						style="width: 13rpx;height: 22rpx;margin-left: 15rpx;"></image>
				</view>
			</view>
			<view class="flex align-center justify-end padding-lr">
				<view class="text-26 flex align-center">
					加时费：
					<view class="" style="color: red;">
						<text>￥</text>
						<text style="font-size: 34rpx;">{{timePrice}}</text>
					</view>
				</view>
			</view>
			<view style="display: flex;height: 100upx;align-items: center;padding: 20upx 30rpx;"
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
			<view
				style="width: 690rpx;height: 80rpx;background: #01993A;color:#FFFFFF;text-align: center;line-height: 80rpx;border-radius: 50rpx;margin: 30rpx;margin-bottom: 60rpx;"
				@tap="pay()">确认支付</view>
		</u-popup>

		<!-- #ifndef MP-WEIXIN -->
		<view class="tabkef" @click="selectKeFu()">
			<image src="../../static/images/my/kefu.png" style="width: 90upx;height: 90upx;">
			</image>
		</view>
		<!-- #endif -->

		<!-- #ifdef MP-WEIXIN -->
		<view v-if="SelKeFu==3" class="tabkef">
			<button type="default" open-type="contact">
				<image src="../../static/images/my/kefu.png" style="width: 90upx;height: 90upx;">
				</image>
			</button>
		</view>

		<view class="tabkef" v-else @click="selectKeFu()">
			<image src="../../static/images/my/kefu.png" style="width: 90upx;height: 90upx;">
			</image>
		</view>
		<!-- #endif -->
		<u-picker :show="timeShow" @cancel="closePicker()" @close="closePicker()" :columns="columns"
			@confirm="confirm"></u-picker>
	</view>
</template>

<script>
	import moment from 'moment'
	export default {
		data() {
			return {
				timeCha: '',
				startDate: '',
				endDate: '',
				strTime: '',
				endTime: '',
				loading: true,
				openWay: 0,
				openLists: [],
				showpay: false,
				ordersNo: '',
				order: [],
				hour: 0,
				showHour: false,
				enddata: '',
				timePrice: 0,
				timeShow: false,
				columns: [],
				timeList: [{
					"id": 1,
					"str_time": "00:00",
					"end_time": "01:00",
					"is_busy": 0
				}, {
					"id": 3,
					"str_time": "01:00",
					"end_time": "02:00",
					"is_busy": 0
				}, {
					"id": 5,
					"str_time": "02:00",
					"end_time": "03:00",
					"is_busy": 0
				}, {
					"id": 7,
					"str_time": "03:00",
					"end_time": "04:00",
					"is_busy": 0
				}, {
					"id": 9,
					"str_time": "04:00",
					"end_time": "05:00",
					"is_busy": 0
				}, {
					"id": 11,
					"str_time": "05:00",
					"end_time": "06:00",
					"is_busy": 0
				}, {
					"id": 13,
					"str_time": "06:00",
					"end_time": "07:00",
					"is_busy": 0
				}, {
					"id": 15,
					"str_time": "07:00",
					"end_time": "08:00",
					"is_busy": 0
				}, {
					"id": 17,
					"str_time": "08:00",
					"end_time": "09:00",
					"is_busy": 0
				}, {
					"id": 19,
					"str_time": "09:00",
					"end_time": "10:00",
					"is_busy": 0
				}, {
					"id": 21,
					"str_time": "10:00",
					"end_time": "11:00",
					"is_busy": 0
				}, {
					"id": 23,
					"str_time": "11:00",
					"end_time": "12:00",
					"is_busy": 0
				}, {
					"id": 25,
					"str_time": "12:00",
					"end_time": "13:00",
					"is_busy": 0
				}, {
					"id": 27,
					"str_time": "13:00",
					"end_time": "14:00",
					"is_busy": 0
				}, {
					"id": 29,
					"str_time": "14:00",
					"end_time": "15:00",
					"is_busy": 0
				}, {
					"id": 31,
					"str_time": "15:00",
					"end_time": "16:00",
					"is_busy": 0
				}, {
					"id": 33,
					"str_time": "16:00",
					"end_time": "17:00",
					"is_busy": 0
				}, {
					"id": 35,
					"str_time": "17:00",
					"end_time": "18:00",
					"is_busy": 0
				}, {
					"id": 37,
					"str_time": "18:00",
					"end_time": "19:00",
					"is_busy": 0
				}, {
					"id": 39,
					"str_time": "19:00",
					"end_time": "20:00",
					"is_busy": 0
				}, {
					"id": 41,
					"str_time": "20:00",
					"end_time": "21:00",
					"is_busy": 0
				}, {
					"id": 43,
					"str_time": "21:00",
					"end_time": "22:00",
					"is_busy": 0
				}, {
					"id": 45,
					"str_time": "22:00",
					"end_time": "23:00",
					"is_busy": 0
				}, {
					"id": 47,
					"str_time": "23:00",
					"end_time": "23:59",
					"is_busy": 0
				}],
				date: '',
				price: 0,
				SelKeFu: 1,
				timer: '',
				isTre: true
			}
		},
		onLoad(option) {
			this.SelKeFu = this.$queue.getData('SelKeFu')
			if (option.ordersNo) {
				this.ordersNo = option.ordersNo
				this.getDetail()
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
			let that = this
			that.timer = setInterval(() => {
				that.getDetail()
			}, 10000)
		},
		onUnload() {
			clearInterval(this.timer)
		},
		onHide() {
			clearInterval(this.timer)
		},
		onShow() {
			this.hour = uni.getStorageSync('hour');
			// uni.$on('time', data => {
			// 	// console.log(data, '时间')
			// 	this.time = data
			// 	const Time = moment(this.time.enddata).diff(moment(this.order.end_time), 'hours')
			// 	this.timePrice = parseFloat(this.order.goods_info.goods_price) * parseFloat(Time).toFixed(2)
			// 	// console.log(Time,'=======',price)
			// })

		},
		methods: {
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
			//获取茶室详情
			getGoodsInfo() {
				this.$queue.showLoading('加载中...')
				let data = {
					goodsId: this.order.goodsId,
				}
				this.$Request.getT('/app/goods/selectGoodsById', data).then(res => {
					if (res.code == 0) {
						this.price = res.data.price

					} else {
						uni.hideLoading();
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			//复制wifi密码
			copyWifi(val) {
				uni.setClipboardData({
					data: val,
					success() {
						uni.showToast({
							title: 'WIFI密码已复制'
						})
					}
				})
			},

			//呼叫保洁
			hujiao() {
				let that = this
				uni.showModal({
					title: '提示',
					content: '确认呼叫保洁吗？',
					confirmText: '确认',
					complete(ret) {
						if (ret.confirm) {
							let data = {
								ordersId: that.order.ordersId
							}
							that.$Request.postT('/app/orders/callCleaKeeping', data).then(res => {
								if (res.code == 0) {
									uni.showToast({
										title: '已呼叫'
									})
								} else {
									uni.showToast({
										title: res.msg,
										icon: 'none'
									})
								}
							})
						}
					}
				})

			},
			closePicker() {
				this.timeShow = false
			},
			//复制订单号
			copyOrder(order) {
				uni.setClipboardData({
					data: order,
					success() {
						uni.showToast({
							title: '已复制订单号'
						})
					},
					fail() {
						uni.showToast({
							title: '复制失败，请重试！',
							icon: 'none'
						})
					}
				})
			},
			confirm(e) {
				console.log(e)
				let endTime = e.value[0]
				this.enddata = moment().format('YYYY-MM-DD') + ' ' + e.value[0]
				// console.log(this.enddata, moment().format('YYYY-MM-DD'))
				// this.time = data
				const Time = moment(this.enddata).diff(moment(this.order.endTime), 'hours')
				this.timePrice = parseFloat(this.price) * parseFloat(Time).toFixed(2)
				// console.log(Time,'8888',this.timePrice)
				this.timeShow = false
			},
			// 根据日期获取时间
			getDataTime(index) {
				this.$queue.showLoading('加载中...')
				let data = {
					goodsId: this.order.goodsId,
					date: this.date,
				}
				this.$Request.getT('/app/roomTime/selectRoomTimeByDate', data).then(res => {
					if (res.code == 0) {
						let arr = []
						let arrs = []
						if (res.data && res.data.length > 0) {
							let timeLists = JSON.parse(JSON.stringify(this.timeList));
							let timesList = []
							var myDate = new Date();
							timeLists.forEach(de => {
								// console.log(parseInt(de.str_time))
								// console.log(parseInt(myDate.getHours()))
								if (Number(parseInt(de.str_time)) > Number(parseInt(myDate.getHours()))) {
									if (res.data.findIndex(item => item.roomTime === de.str_time) !== -
										1) {
										de.is_busy = 1;
									}
									timesList.push(de)
								}
							});
							res.data = timesList;
							arrs = res.data

						} else {
							arrs = this.timeList
						}
						res.data.some((item, index) => {
							// item.isSelect = false
							if (item.is_busy == 0) {
								//判断下一条是否是可选的
								if ((index + 1) < res.data.length && res.data[index + 1].is_busy ==
									1) { //否
									arr.push(item.end_time)
									return true
								} else { //不是
									arr.push(item.end_time)
								}

							}
						})

						// arr.splice(0, 1)
						this.columns.push(arr)
						console.log(this.columns, '11111')
						uni.hideLoading();
						// this.hostArr = res.data
						// if (index != '-1') this.day_index = index
						// let time = {
						// 	hostArr:this.hostArr,
						// 	dayArr:this.dayArr,
						// 	yearTime:this.yearTime
						// }
						// this.$emit('submitData', time)
					} else {
						uni.hideLoading();
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			closeHour() {
				this.showHour = false
				this.enddata = ''
				this.timePrice = 0
			},
			//预约时间
			godata() {
				if (this.columns.length == 0 || this.columns[0].length == 0) {
					uni.showToast({
						title: '暂无可选择的时间',
						icon: 'none'
					})
				} else {
					this.timeShow = true
				}

				// uni.navigateTo({
				// 	url: '/pages/index/data?goodsId=' + this.order.goods_id
				// })
			},
			selectWay: function(id) {
				this.openWay = id;
			},
			goswitch() {
				uni.navigateTo({
					url: '/my/order/switching?goodsId=' + this.order.goodsId
				})
				// uni.navigateTo({
				// 	url: '/my/setting/switching?goods_id='+this.order.goods_id
				// })
			},
			// 一键导航
			openMap(latitude, longitude, name) {
				uni.openLocation({
					latitude: latitude - 0, //要去的纬度-地址       
					longitude: longitude - 0, //要去的经度-地址
					name: name, //地址名称
					address: name, //详细地址名称
					success: function() {
						console.log('导航成功');
					},
					fail: function(error) {
						console.log(error)
					}
				});
			},
			bindPhone(phone) {
				uni.makePhoneCall({
					phoneNumber: phone //仅为示例
				});
			},
			getDateDiff(data, data1) {
				// 传进来的data必须是日期格式，不能是时间戳
				//var str = data;
				//将字符串转换成时间格式
				var timePublish = new Date(data1);
				var timeNow = new Date(data);
				var minute = 1000 * 60;
				var hour = minute * 60;
				var day = hour * 24;
				var month = day * 30;
				var result = "2";
				var diffValue = timeNow - timePublish;
				var diffMonth = diffValue / month;
				var diffWeek = diffValue / (7 * day);
				var diffDay = diffValue / day;
				var diffHour = diffValue / hour;
				var diffMinute = diffValue / minute;
				return diffHour;
			},
			getDetail() {
				let data = {
					ordersNo: this.ordersNo
				}
				this.$Request.getT('/app/orders/selectOrdersDetails', data).then(res => {
					if (res.code === 0) {
						this.order = res.data
						// if (this.order.moneyJson) {
						// 	let moneyJson = JSON.parse(this.order.moneyJson)
						// 	this.order.moneyJson = moneyJson
						// }

						// if (this.order.riderPhone) {
						// 	let mobile = this.order.riderPhone
						// 	this.order.riderPhones = mobile.substring(0, 3) + "****" + mobile.substring(7)
						// }
						if (this.order.startTime && this.order.endTime) {
							this.timeCha = this.getDateDiff(this.order.endTime, this.order.startTime).toFixed(2)

							// console.log(this.timeCha)
							this.startDate = this.order.startTime.substring(5, 10)
							this.endDate = this.order.endTime.substring(5, 10)
							this.strTime = this.order.startTime.substring(10, 17)
							this.endTime = this.order.endTime.substring(10, 16)
							this.date = this.order.startTime.substring(0, 10)
							// console.log(year, '-----', strTime, endTime)
							// console.log(this.date,'==========')
							// this.order.data = year
							// this.order.time = strTime + '~' + endTime
						}
						if (this.isTre) {
							this.isTre = false
							this.getDataTime()
							this.getGoodsInfo()
						}


					}
					uni.hideLoading()
					this.loading = false
				});
			},
			// 取消订单
			bindorderOff() {
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定取消订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							that.$Request.postT('/app/orders/cancelOrders?ordersNo=' + that.ordersNo)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单取消成功'
										});
										that.getDetail()
										setTimeout(function() {
											uni.navigateBack()
										}, 100)
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
			bindorderOffs() {
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
							that.$Request.postT('/app/orders/closeOrders?ordersId=' + that.order.ordersId)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单已完成'
										});
										that.getDetail()
										setTimeout(function() {
											uni.navigateBack()
										}, 100)
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
			// 删除订单
			bindorderdete(ordersId) {
				let that = this
				uni.showModal({
					title: '温馨提示',
					content: '确定删除订单?',
					showCancel: true,
					cancelText: '取消',
					confirmText: '确认',
					success: res => {
						if (res.confirm) {
							that.$Request.postT('/app/orders/removeOrders?ordersId=' + ordersId)
								.then(res => {
									if (res.code == 0) {
										uni.showToast({
											title: '订单删除成功'
										});
										// that.getDetail()
										setTimeout(function() {
											uni.navigateBack()
										}, 1000)
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
			// 待支付订单支付
			orderpay() {
				let that = this
				that.$queue.showLoading('加载中...')
				that.showpay = false
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
									uni.showLoading({
										title: '支付成功'
									});
									uni.hideLoading();
									that.getDetail()
									setTimeout(function() {
										uni.navigateBack()
									}, 100)
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
								title: '支付成功',
								icon: 'none',
								duration: 1200
							})
							uni.hideLoading();
							that.getDetail()
							setTimeout(function() {
								uni.navigateBack()
							}, 100)

						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none'
							})
						}
					})
				}

			},
			// 加时支付
			pay() {
				let that = this
				if (!that.enddata || that.enddata == '') {
					uni.showToast({
						title: '请选择结束时间',
						icon: 'none'
					})
					return
				}
				if (that.openWay == 1) { //支付宝

					// #ifdef H5
					let data = {
						ordersNo: that.ordersNo,
						classify: 5, //支付宝 classify 4app 5h5
						endTime: that.enddata
					}
					that.$Request.postT('/app/aliPay/payAddOrders', data).then(red => {
						if (red.code == 0) {
							const div = document.createElement('div')
							div.innerHTML = red.data //此处form就是后台返回接收到的数据
							document.body.appendChild(div)
							document.forms[0].submit()
							that.showHour = false

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
						classify: 4, //支付宝 classify 4app 5h5
						endTime: that.enddata
					}
					that.$Request.postT('/app/aliPay/payAddOrders', data).then(red => {
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
						classify: 3, //微信 classify 1app 2公众号 3小程序
						endTime: that.enddata
					}
					that.$Request.postT('/app/wxPay/payAddOrders', data).then(red => {
						if (red.code == 0) {
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
									that.getDetail()
									setTimeout(function() {
										uni.navigateBack()
									}, 100)
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
						classify: 2, //微信 classify 1app 2公众号 3小程序
						endTime: that.enddata
					}
					that.$Request.postT('/app/wxPay/payAddOrders', data).then(red => {
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
						classify: 1, //微信 classify 1app 2公众号 3小程序
						endTime: that.enddata
					}
					that.$Request.postT('/app/wxPay/payAddOrders', data).then(red => {
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
						ordersNo: that.ordersNo,
						endTime: that.enddata
					}
					that.$Request.postT('/app/orders/payAddOrders', data).then(res => {
						if (res.code == 0) {
							uni.showToast({
								title: '支付成功',
								icon: 'none',
								duration: 1200
							})
							uni.hideLoading();
							that.showHour = false
							that.getDetail()
							setTimeout(function() {
								uni.navigateBack()
							}, 100)

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
							that.showHour = false
							that.getDetail()
							setTimeout(function() {
								uni.navigateBack()
							}, 100)
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
						that.showHour = false
						setTimeout(function() {
							uni.navigateBack()
						}, 100)
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
	page {
		background: #F5F5F5;
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

	.centers_view {
		background: #F5F5F5;
		width: 750rpx;
		border-top-left-radius: 30rpx;
		border-top-right-radius: 30rpx;
		margin-top: -30rpx;
		padding-top: 20rpx;
	}

	.center_view {
		background: #F5F5F5;
		width: 750rpx;
		border-top-left-radius: 30rpx;
		border-top-right-radius: 30rpx;
		margin-top: -70rpx;
		padding-top: 20rpx;
	}

	.statusview_Name {
		font-size: 40rpx;
		font-family: PingFang SC;
		font-weight: 800;
		color: #FFFFFF;
		margin-top: 30rpx;
	}

	.cx_view {
		width: 686rpx;
		margin: 0 auto;
		margin-top: 20rpx;


		.cx_item {
			width: 222rpx;
			// height: 130rpx;
			min-height: 130rpx;
			background: #FFFFFF;
			border-radius: 0rpx 0rpx 24rpx 24rpx;
			text-align: center;

			.cx_name {
				margin-top: 24rpx;
			}
		}
	}

	.box {
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 30rpx;
		margin: 20rpx 30rpx;
	}

	.boxs {
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 50rpx 30rpx;
		margin: 20rpx 30rpx;
	}


	.titl {
		font-size: 28rpx;
		font-family: PingFang SC;
		font-weight: 600;
	}

	.info-box-address {
		position: relative;
		width: 100%;
		height: 123rpx;
		border-radius: 0 0 24rpx 24rpx;
		margin-bottom: 26rpx;
		margin-top: 20rpx;

		image {
			width: 100%;
			height: 100%;
		}

		.info-box-address-s {
			width: calc(100% - 40rpx);
			position: absolute;
			top: 50%;
			left: 30rpx;
			transform: translate(0, -50%);
			overflow: hidden;
			white-space: nowrap;
			text-overflow: ellipsis;

			image {
				width: 128rpx;
				height: 57rpx;
				margin-right: 20rpx;
			}
		}
	}

	.towbox {
		display: flex;
		// align-items: center;

		image {
			width: 195rpx;
			height: 195rpx;
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

		.time {
			width: 412rpx;
			height: 88rpx;
			background: #FFFFFF;
			border: 1rpx solid #01993A;
			border-radius: 16rpx;
			margin-top: 14rpx;
			display: flex;
			align-items: center;

			.times {
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: bold;
				color: #333333;
			}

			.time_date {
				font-size: 30rpx;
				font-family: DIN;
				font-weight: 500;
				color: #01993A;
			}

			.time_riqi {
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #8AC39F;
				// margin-top: 10rpx;
			}
		}

		.price {
			color: #333333;
			font-size: 24rpx;
			margin-top: 10rpx;
			font-weight: bold;

			text {
				font-size: 32rpx;
			}
		}
	}

	.line {
		width: 100%;
		background: #EEEEEE;
		height: 1rpx;
		margin: 30rpx 0;
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
		justify-content: flex-end;

		.btn {
			width: 190rpx;
			height: 70rpx;
			border: 2rpx solid #666666;
			border-radius: 40rpx;
			text-align: center;
			display: flex;
			align-items: center;
			justify-content: center;
			color: #333333;
			margin-left: 20rpx;
		}

		.btnn {
			width: 190rpx;
			height: 70rpx;
			background: #01993A;
			border-radius: 40rpx;
			text-align: center;
			display: flex;
			align-items: center;
			justify-content: center;
			color: #FFFFFF;
			margin-left: 20rpx;
		}
	}

	.btnn {
		width: 170rpx;
		height: 65rpx;
		background: #01993A;
		border-radius: 40rpx;
		text-align: center;
		display: flex;
		align-items: center;
		justify-content: center;
		color: #FFFFFF;
		margin-left: 20rpx;
	}

	.tabkef {
		position: fixed;
		bottom: 200rpx;
		right: 40rpx;
		z-index: 999;
	}
</style>