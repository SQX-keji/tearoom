<template>
	<view style="padding-bottom: 80px;">
		<view class="flex align-center justify-center" style="margin-top: 20rpx;">
			<view class="bg-box">
				<view class="flex" style="width: 100%;">
					<image class="screen-swiper" :src="order.goodsCoverImage" mode="aspectFill"
						style="border-radius: 24rpx;"></image>
					<view class="" style="border-radius: 24rpx;padding:10rpx 30rpx;width: 430rpx;">
						<view class="tit">{{order.goodsName?order.goodsName:''}}</view>
						<view class="label flex align-center flex-wrap">
							<view v-if="order.goodsType" class="label-item"
								v-for="(item,index) in order.goodsType?order.goodsType.split(','):[]" :key="index">
								{{item}}
							</view>
							<view class="label-item">
								已售{{order.sales?order.sales:0}}
							</view>
						</view>
						<view class="priceStatus flex align-center justify-between">
							<view class="priceStatus-l">
								<text>¥</text>{{order.price?order.price:0}}/<text>小时</text>
							</view>
							<view class="priceStatus-r flex align-center justify-between">
								<!-- <view class="priceStatus-r-txt">
									{{isKongxian==true?'空闲中':'已满'}}
								</view>
								<text v-if="isKongxian">订</text> -->
								<view class="priceStatus-r-txt" v-if="order.status==1">空闲中</view>
								<view class="priceStatus-r-txt" v-else>已满</view>
								<text v-if="isKongxian">订</text>
							</view>
						</view>
					</view>
				</view>
				<!-- 时间段状态 -->
				<view class="timeStatus flex justify-between" v-if="timeArr.length>0">
					<view class="timeStatus-item" v-for="(item,index) in timeArr" :key="index">
						<view class="timeStatus-item-k" v-if="item.is_busy==0">

						</view>
						<view class="timeStatus-item-k ding" v-else>

						</view>
						<view class="timeStatus-item-t">
							{{item.name?item.name:''}}
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

		<view class="headbox flex align-center justify-center" style="margin-top: 20rpx;">
			<view class="headbox-box">
				<view class="tabbox">
					<view class="tab" :class="tabIndex==1?'tabact':''" @click="bindTab(1)">套餐模式</view>
					<view class="tab" :class="tabIndex==2?'tabact-r':''" @click="bindTab(2)">小时模式</view>
				</view>

				<view class="" style="margin-top: 30rpx;margin-bottom: 10rpx;padding: 0 20rpx;"
					v-if="order.roomTimeList">
					<its-calendar ref="itsCalendar" :goodsId="goodsId" @submitData="submitData">
					</its-calendar>
				</view>
				<view class="tcbox" v-if="tabIndex==1">
					<view class="tcname">套餐选择</view>
					<view class="listbox justify-between">
						<scroll-view class="scroll-view_H" scroll-x="true" @scroll="scroll" scroll-left="0">
							<view class=" scroll-view-item_H" v-for="(item,index) in productList" :key="index"
								:class="infoIndex==index?'scroll-view-item_Act':''" @click="bindInfo(index,item)">
								<view>
									<view>{{item.productName}}</view>
									<view class="tcprice">
										<text>￥</text>
										{{item.productPrice}}
									</view>
								</view>
							</view>
						</scroll-view>

					</view>
				</view>
				<view class="tcbox" v-if="tabIndex==2">
					<view class="tcname">时长选择</view>
					<view class="listbox">
						<scroll-view class="scroll-view_H" scroll-x="true" @scroll="scroll" scroll-left="0">
							<view class=" scroll-view-item_sc" v-for="(item,index) in hourlist" :key="index"
								:class="hourIndex==index?'scroll-view-item_scAct':''" @click="bindHour(index,item)">
								<view>{{item.name}}</view>
							</view>
						</scroll-view>

					</view>
				</view>
				<view class="tcbox margin-top justify-between" style="margin-bottom: 30rpx;">
					<view class="tcname" style="font-weight: 400;">时间选择</view>
					<view class="listbox ">
						<view class="databox">
							<view class="startr flex align-center justify-center" @click="selsctStartTime()">
								{{startTime?startTime:'开始时间'}}
							</view>
							<view class="padding-lr-xs">到</view>
							<view class="end flex align-center justify-center">
								{{endTime?endTime:'结束时间'}}
							</view>
						</view>
					</view>
				</view>
			</view>


		</view>

		<view class="headbox   flex align-center justify-center">
			<view class="headbox-box padding">
				<view class=" flex align-center justify-between margin-bottom">
					<view style="width:200rpx">预约号码</view>
					<view class="text-sm">
						<u--input type="number" maxlength="11" placeholder="请填写" border="none" v-model="order_phone"
							inputAlign="right"></u--input>
					</view>
				</view>
				<view class=" flex align-center justify-between margin-bottom">
					<view style="width:200rpx">姓名</view>
					<view class="text-sm" style="width: 80%;">
						<u--input placeholder="请填写" border="none" v-model="order_name" inputAlign="right"></u--input>
					</view>
				</view>
				<view class="flex align-center justify-between margin-bottom">
					<view>预约时长</view>
					<view>{{used_time}}小时</view>
				</view>


				<view class="flex align-center justify-between  margin-bottom" v-if="tabIndex==1" @click="openteam">
					<view>团购劵</view>
					<view>
						<text v-if="tglist.length==0">暂无可用团购劵</text>
						<!-- <text v-else>{{teamName?'-￥'+teamName:'请选择团购劵'}}</text> -->
						<text v-else>{{teamName?teamName:'请选择团购劵'}}</text>
						<image src="../../static/images/my/right.png"
							style="width: 13rpx;height: 22rpx;margin-left: 15rpx;"></image>
					</view>
				</view>
				<view class="flex align-center justify-between" :class="tabIndex==2?'margin-tb':''" @click="openyouhui">
					<view>优惠劵</view>
					<view>
						<text v-if="couponlist.length==0">暂无可用优惠劵</text>
						<text v-else>{{couponName?'-￥'+couponName:'请选择优惠劵'}}</text>
						<image src="../../static/images/my/right.png"
							style="width: 13rpx;height: 22rpx;margin-left: 15rpx;"></image>
					</view>
				</view>
				<view class="margin-top-xl">
					<view>备注</view>
					<view class="margin-top-sm" style="border-radius:10rpx;">
						<u--textarea :confirmType="null" v-model="remarks" placeholder="选填，可以告诉我你的需求" border="none"
							height="120" maxlength="100" style="background: #f5f5f5;" :count="true" />
					</view>
				</view>
			</view>

		</view>
		<u-picker @cancel="startshow = false" :show="startshow" :columns="columns" @confirm="confirm"></u-picker>

		<u-picker @cancel="hourshow = false" :show="hourshow" :columns="columnsHour" keyName="label"
			@confirm="confirmhour"></u-picker>

		<view class="taber">
			<view class="price">
				<view class="pp"><text>￥</text>{{totalprice}}</view>
			</view>
			<view class="butt" @click="gopay">立即抢购</view>
		</view>

		<u-popup :show="show" @close="show = false" @open="open">
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
				<view class="btnpay" @click="getOrderNo">立即下单</view>
			</view>
		</u-popup>

		<!-- 优惠劵弹框 -->
		<u-popup :show="youhuiShow" mode="bottom" round="14" :closeable="true" @close="youhuiShow = false">
			<view style="padding: 30rpx 30rpx 50rpx 30rpx;background: #F5F8FC;">
				<view class="text-lg text-bold text-center">优惠劵</view>
				<view class="flex align-center justify-end">
					<view class="deteJuan" @click="deteYouhuj">不使用优惠劵</view>
				</view>
				<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper"
					@scrolltolower="lower" @scroll="scroll">
					<view class="money_box" v-for="(item,index) in couponlist" :key="index">
						<view class="box_tit">
							<view class="money_name">{{item.couponName}}</view>
							<view class="money_price"><text>￥</text>{{item.lessMoney}}</view>
						</view>
						<view class="money_data" v-if="item.failureTime">有效期至{{item.failureTime}}
						</view>
						<view class="money_data" v-else>永久有效</view>
						<view class="money_line">
							<u-line direction="row" color="#E6E6E6" border-style="dashed" />
						</view>
						<view class="box_bottom">
							<view class="money_use">满{{item.minMoney}}元可使用</view>
							<view class="money_btn">
								<view class="lj_use" @click="useryouhui(item,1)">
									立即使用
								</view>
							</view>
						</view>
					</view>
				</scroll-view>

			</view>
		</u-popup>

		<!-- 团购劵弹框 -->
		<u-popup :show="tuangouShow" mode="bottom" round="14" :closeable="true" @close="tuangouShow = false">
			<view style="padding: 30rpx 30rpx 50rpx 30rpx;background: #F5F8FC;">
				<view class="text-lg text-bold text-center">团购劵</view>
				<view class="flex align-center justify-end">
					<view class="deteJuan" @click="deteTeam">不使用团购劵</view>
				</view>
				<scroll-view :scroll-top="scrollTop" scroll-y="true" class="scroll-Y" @scrolltoupper="upper"
					@scrolltolower="lower" @scroll="scroll">
					<view class="money_box" v-for="(item,index) in tglist" :key="index">
						<view class="box_tit">
							<view class="money_name">{{item.couponName}}</view>
							<view class="money_price" style="font-size: 26rpx;">
								<!-- <text>￥</text>{{item.new_money}} -->
								限制{{item.hour}}小时使用
							</view>
						</view>
						<view class="money_data" v-if="item.failureTime">有效期至{{item.failureTime}}
						</view>
						<view class="money_data" v-else>永久有效</view>
						<view class="money_line">
							<u-line direction="row" color="#E6E6E6" border-style="dashed" />
						</view>
						<view class="box_bottom flex align-center justify-between">
							<!-- <view class="money_use">满{{item.old_money}}元可使用</view> -->
							<view class="">

							</view>
							<view class="money_btn">
								<view class="lj_use" @click="useryouhui(item,2)">
									立即使用
								</view>
							</view>
						</view>
					</view>
				</scroll-view>

			</view>
		</u-popup>
	</view>
</template>

<script>
	import moment from 'moment'
	import itsCalendar from '@/components/its-calendar/its-calendar.vue';
	import numberBox from 'uview-ui/libs/config/props/numberBox';
	import {
		showToast
	} from '../../common/queue';
	export default {
		components: {
			itsCalendar
		},
		data() {
			return {
				order: '',
				goodsId: '', //茶室id
				swiperList: [], //轮播图
				// multipleChoice:[],//选择
				productList: [], //套餐列表
				goodsPrice: 0,
				oldPrice: 0, //原价
				time: [],
				totalprice: 0,
				tabIndex: 1,
				infoIndex: 0,
				scrollTop: 0,
				old: {
					scrollTop: 0
				},
				startshow: false,
				startTime: '',
				endTime: '',
				columns: [],
				yearTime: '',
				used_time: '', //套餐小时
				hourlist: [{
					id: 1,
					name: '2小时',
					hour: 2
				}, {
					id: 2,
					name: '4小时',
					hour: 4
				}, {
					id: 3,
					name: '6小时',
					hour: 6
				}, {
					id: 4,
					name: '其他',
					hour: ''
				}],
				hourIndex: 0,
				hourshow: false,
				columnsHour: [],
				order_phone: '',
				order_name: '',
				startdata: '',
				enddata: '',
				remarks: '',
				show: false,
				openWay: 0,
				openLists: [],
				couponlist: [], //优惠劵
				youhuiShow: false,
				couponId: '',
				couponName: '',
				totalprices: 0,
				tglist: [], //团购劵
				teamName: '',
				tuangouShow: false,
				productId: 0, //套餐id
				isKongxian: true,
				timeArr: [],
				isLook: 1,
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
				ordersNo: '',
				tearoomId: '',
				isPlayIng: false, //是否正在支付中
				invitationCode: '',
			}
		},
		onShareAppMessage(res) {
			return {
				path: '/pages/index/packageDetail?invitation=' + this.invitationCode + '&tearoomId=' + this
					.tearoomId + '&goodsId=' + this.goodsId, //这是为了传参   onload(data){let id=data.id;} 
				title: this.order.goodsName,
			}
		},
		onShareTimeline() {
			return {
				path: '/pages/index/packageDetail?invitation=' + this.invitationCode + '&tearoomId=' + this
					.tearoomId + '&goodsId=' + this.goodsId, //这是为了传参   onload(data){let id=data.id;} 
				title: this.order.goodsName,
			}
		},
		onLoad(option) {
			this.invitationCode = uni.getStorageSync('invitationCode')
			if (option.invitation) {
				uni.setStorageSync('inviterCode', option.inviterCode)
			}
			if (option.tearoomId) {
				this.tearoomId = option.tearoomId
			}
			if (option.goodsId) {
				this.goodsId = option.goodsId
				this.getGoodsInfo()
			}
			// #ifdef MP-WEIXIN
			if (option.scene) {
				const scene = decodeURIComponent(option.scene);
				let sceneArr = scene.split(',')
				this.tearoomId = sceneArr[0]
				this.goodsId = sceneArr[1]
				this.getGoodsInfo()
			}
			// #endif

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
		onShow() {
			if (this.tearoomId && this.goodsId) {
				this.getGoodsInfo()
				this.getcoupon()
			}
			this.$Request.getT('/app/common/type/810').then(res => { //小时配置	810
				if (res.code == 0 && res.data && res.data.value) {
					let arr = []
					res.data.value.split(',').map((item, index) => {
						let time = {
							label: item + '小时',
							id: item
						}
						arr.push(time)

					})
					this.columnsHour = [arr]
				}
			});

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
			selsctStartTime() {
				//当没有可选时间，或者当前套餐时间比可选时间多时都不可预约
				if (this.columns.length == 0 || this.columns[0].length == 0) {
					uni.showToast({
						title: '暂无可预约时间',
						icon: 'none'
					})
				} else {
					this.startshow = true
				}
			},
			//重置优惠券/团购券
			setQuan() {
				this.couponId = ''
				this.teamName = ''
				this.couponName = ''
				// this.totalprice = Number(this.totalprice) - Number(this.teamName) - Number(this.couponName)
			},
			// 查看图片
			saveImg(imgs, index) {
				// console.log(imgs)
				let that = this;
				let imgArr = imgs
				// imgArr.push(imgs);
				// //预览图片
				uni.previewImage({
					urls: imgArr,
					current: imgArr[index]
				});
			},
			openteam() { //打开团购劵
				if (this.tglist.length != 0) {
					if (!this.startTime) {
						uni.showToast({
							title: '请选择开始时间',
							icon: 'none'
						})
						return
					}
					if (this.couponName) {
						uni.showToast({
							title: '优惠劵不能与团购劵同时使用',
							icon: 'none'
						})
						return
					}
					this.tuangouShow = true
				} else {
					uni.showToast({
						title: '暂无可使用团购劵',
						icon: 'none'
					})
				}
			},
			openyouhui() { //打开优惠劵
				if (this.couponlist.length != 0) {
					if (!this.startTime) {
						uni.showToast({
							title: '请选择开始时间',
							icon: 'none'
						})
						return
					}
					if (this.teamName) {
						uni.showToast({
							title: '团购劵不能与优惠劵同时使用',
							icon: 'none'
						})
						return
					}

					this.youhuiShow = true
				} else {
					uni.showToast({
						title: '暂无可使用优惠劵',
						icon: 'none'
					})
				}
			},
			//清空优惠劵
			deteYouhuj() {
				this.couponId = ''
				this.couponName = ''
				// if (this.tabIndex == 1) {

				// } else {
				// 	this.totalprices = parseFloat(this.order.price * this.used_time).toFixed(2)
				// }

				this.totalprice = this.totalprices
				this.youhuiShow = false
			},
			//使用优惠劵
			useryouhui(item, index) {
				this.totalprices = this.totalprice
				let totalprice = this.totalprices
				if (index == 1) {
					console.log(this.couponId, '11', item.couponUserId, this.totalprices)
					if (this.couponId == item.couponUserId) {
						this.youhuiShow = false
						return
					} else {
						if (!this.couponId) {
							if (Number(item.minMoney) <= Number(totalprice)) {

								this.couponId = item.couponUserId
								this.teamName = ''
								this.couponName = item.lessMoney
								// console.log(totalprice,'898989')
								// totalprice =  parseFloat(totalprice - item.lessMoney).toFixed(2)
								if (totalprice > 0) {
									this.totalprice = parseFloat(totalprice - item.lessMoney).toFixed(2)
								} else {
									this.totalprice = 0
								}

								this.youhuiShow = false
							} else {
								uni.showToast({
									title: '支付金额不能小于' + item.minMoney + '元',
									icon: 'none'
								})
								return
							}
						} else {
							uni.showToast({
								title: '优惠劵不能重复使用',
								icon: 'none'
							})
						}

					}

				} else {
					if (Number(this.used_time == Number(item.hour))) {
						this.couponId = item.couponUserId
						this.couponName = ''
						// this.teamName = item.new_money
						this.teamName = item.couponName
						// this.totalprice = parseFloat(totalprice - item.new_money).toFixed(2)
						this.totalprice = 0
						this.tuangouShow = false
					} else {
						uni.showToast({
							title: '团购劵不支持当前套餐使用',
							icon: 'none'
						})
						return
					}
				}
			},
			deteTeam() { //清空团购劵
				if (this.teamName) {
					this.couponId = ''
					this.teamName = ''
					this.totalprice = this.totalprices
					this.tuangouShow = false
				} else {
					this.tuangouShow = false
				}
			},
			getcoupon() { //2.优惠券
				let data = {
					type: 2,
					page: 0,
					size: 100,
					goodsId: this.goodsId,
					money: this.totalprice,
					userId: uni.getStorageSync('userId'),
					tearoomId: this.tearoomId,
					goodsId: this.goodsId
				}
				this.$Request.getT('/app/selfCouponUser/userList', data).then(res => { //优惠券
					if (res.status == 0 && res.data) {
						this.couponlist = res.data.content
					}
				});
			},
			getTuangou() { //1团购劵
				let data = {
					type: 1,
					page: 0,
					size: 100,
					goodsId: this.goodsId,
					userId: uni.getStorageSync('userId'),
					tearoomId: this.tearoomId,
					goodsId: this.goodsId
				}
				this.$Request.getT('/app/selfCouponUser/userList', data).then(res => { //团购劵
					if (res.status == 0 && res.data) {
						this.tglist = res.data.content
					}
				});
			},
			selectWay: function(id) {
				this.openWay = id;
			},
			open() { //打开支付弹框
				this.show = true
			},
			/**
			 * @param {String} startTime
			 * @param {String} endTime
			 * 计算两个时间中的时间差
			 */
			formattedTimeRange(startTime, endTime) {
				const start = moment(startTime);
				const end = moment(endTime);
				const hoursDiff = end.diff(start, 'hours');
				const timeRange = [];

				for (let i = 0; i <= hoursDiff; i++) {
					const currentHour = start.clone().add(i, 'hours');
					const formattedHourStart = currentHour.format('HH:mm');
					const formattedHourEnd = currentHour.clone().add(1, 'hours').format('HH:mm');
					const id = i + 1; // Generate ID for each time range

					timeRange.push(`${formattedHourStart}`);
				}

				return timeRange;
			},
			confirm(e) { //选择开始时间
				let nowDate = moment().format("yy-MM-DD")
				let nowHours = moment().format('HH:mm');
				console.log(nowDate, this.yearTime, this.yearTime > nowDate, '11111111')
				if (this.yearTime <= nowDate) {
					if (e.value[0] < nowHours) {
						uni.showToast({
							title: '请选择大于当前时间',
							icon: 'none',
							duration: 2000
						})
						return
					}
				}

				let yearTime = this.yearTime + ' ' + e.value[0]
				let end = moment(yearTime).add(Number(this.used_time), "hours").format("YYYY-MM-DD HH:mm")
				// console.log(yearTime,end,'=========')
				//获取选中的时间并把小时放入数组
				let arrs = this.formattedTimeRange(yearTime, end)
				//判断选择的小时数组是否在可选的时间数组中
				let isTrue = this.checkElementsExist(arrs.slice(0, arrs.length - 1), this.columns[0])
				// console.log(e.value[0],end, '333333')
				//如果在，那么则选中
				if (isTrue) {
					this.startTime = e.value[0]
					this.startdata = yearTime
					this.endTime = moment(end).format('HH:mm');
					this.enddata = end
					this.startshow = false
				} else { //否则提示时间不足
					uni.showToast({
						title: '当前选中的时间不足，请重新选择',
						icon: 'none'
					})
				}
			},
			/**
			 * @param {Array} arr1
			 * @param {Array} arr2
			 * 判断数组arr1中的元素是否全部存在arr2中，如果是则返回true，否则返回false
			 */
			checkElementsExist(arr1, arr2) {
				return arr1.every(function(element) {
					return arr2.includes(element);
				});
			},
			bindInfo(index, item) { //套餐切换选择
				this.infoIndex = index
				this.used_time = item.productNum
				this.totalprice = item.productPrice
				this.goodsPrice = item.productPrice
				this.productId = item.productId
				//重置选择的时间
				this.startTime = ''
				this.endTime = ''
				this.setQuan()
			},
			confirmhour(e) { //小时模式 选择小时
				// console.log(e,'小时选择：'+e.value[0].id)
				this.used_time = e.value[0].id
				this.goodsPrice = this.order.price
				this.totalprice = parseFloat(this.order.price * this.used_time).toFixed(2)
				this.hourshow = false

			},
			bindHour(index, item) { //时长切换选择
				this.hourIndex = index
				if (item.name != '其他') {
					this.used_time = item.hour
					this.goodsPrice = this.order.price
					this.totalprice = parseFloat(this.order.price * this.used_time).toFixed(2)

				} else {

					this.hourshow = true
				}
				//重置选择的时间
				this.startTime = ''
				this.endTime = ''
				this.setQuan()
			},
			scroll: function(e) {
				// console.log(e)
				// this.old.scrollTop = e.detail.scrollTop
			},
			//组件确认的数据
			submitData(e) {
				if (e.index != 0 && e.index != -1) {
					this.setTimeLook(e.hostArr)
				} else {
					this.setTimeLook(this.order.roomTimeList)
				}



				if (e.yearTime) {
					this.yearTime = e.yearTime
				} else {
					this.yearTime = e.dayArr[0].data
				}

				let arr = []
				let nowDate = moment().format("yy-MM-DD");
				let nowHours = moment().format('HH:mm');
				console.log(this.yearTime, nowHours, '-------')
				e.hostArr.map(item => {
					let nowday = nowDate + ' ' + nowHours
					let arrday = this.yearTime + ' ' + item.str_time
					// console.log(nowday,arrday,'-------')
					// console.log(item.is_busy == 0,arrday> nowday, '888888888')
					if (item.is_busy == 0 && arrday > nowday) {
						arr.push(item.str_time)
					}
				})
				// arr.splice(0, 1)
				// console.log(arr, '===========')
				this.columns = [arr]
				// 重置套餐状态
				this.infoIndex = 0
				this.hourIndex = 0
				//重置选择的时间
				this.startTime = ''
				this.endTime = ''


				if (this.hourlist[0].name != '其他') {
					if (this.tabIndex == 2) {
						this.used_time = this.hourlist[0].hour
					}

					if (this.tabIndex == 1) {
						this.totalprice = this.order.productList[this.infoIndex].productPrice
						this.goodsPrice = this.order.productList[this.infoIndex].productPrice
						this.totalprices = this.order.productList[this.infoIndex].productPrice
					} else {
						this.totalprices = parseFloat(this.order.price * this.used_time).toFixed(2)
						this.totalprice = parseFloat(this.order.price * this.used_time).toFixed(2)
						this.goodsPrice = this.order.price
						this.productId = 0
					}
					this.oldPrice = this.order.old_price //原价
				} else {

					this.hourshow = true
				}

			},
			bindTab(index) { //1套餐模式  2小时模式
				this.tabIndex = index
				if (this.tabIndex == 1) {
					this.used_time = this.productList[this.infoIndex].productNum
					this.totalprice = this.order.productList[this.infoIndex].productPrice
					this.totalprices = this.order.productList[this.infoIndex].productPrice
					this.goodsPrice = this.order.productList[this.infoIndex].productPrice
					this.productId = this.order.productList[this.infoIndex].productId
				} else {
					this.productId = 0
					this.used_time = this.hourlist[this.hourIndex].hour
					this.goodsPrice = this.order.price
					this.totalprice = parseFloat(this.order.price * this.used_time).toFixed(2)
					this.totalprices = parseFloat(this.order.price * this.used_time).toFixed(2)
					// console.log(this.totalprice)
				}
				//通过refs调用子组件的方法重置组件状态
				this.$refs.itsCalendar.getDataTime('-1')
				// 重置套餐状态
				this.infoIndex = 0
				this.hourIndex = 0
				//重置选择的时间
				this.startTime = ''
				this.endTime = ''
				this.couponId = ''
				this.setQuan()
			},
			setTimeLook(arrs) {
				let arr = []
				arrs.map((item, index) => {
					let obj = {
						is_busy: item.is_busy
					}
					if (index == 0) {
						obj.name = '次日'
					} else {
						obj.name = index
					}
					arr.push(obj)
				})
				this.timeArr = arr
			},
			//获取茶室详情
			getGoodsInfo() {
				this.$queue.showLoading('加载中...')
				let data = {
					goodsId: this.goodsId
				}
				this.$Request.getT('/app/goods/selectGoodsById', data).then(res => {
					if (res.code == 0) {
						this.order = res.data

						if (this.order.roomTimeList && this.order.roomTimeList.length > 0) {
							let timeLists = JSON.parse(JSON.stringify(this.timeList));
							timeLists.forEach(de => {
								if (this.order.roomTimeList.findIndex(item => item.roomTime === de
										.str_time) !== -1) {
									de.is_busy = 1;

								}
							});
							this.order.roomTimeList = timeLists;
							this.setTimeLook(this.order.roomTimeList)
						} else {
							this.order.roomTimeList = this.timeList;
							this.setTimeLook(this.order.roomTimeList)
						}
						this.isKongxian = this.isIdle(this.order.roomTimeList)
						if (this.order.roomTimeList) {
							this.setTimeLook(this.order.roomTimeList)
						}

						if (res.data.goodsTitleImage) {
							this.swiperList = res.data.goodsTitleImage.split(',')
						}
						this.productList = res.data.productList //套餐列表
						if (this.productList) {
							if (this.tabIndex == 1) {
								this.used_time = this.productList[0].productNum
							}
						}
						if (this.tabIndex == 1) {
							this.productId = this.order.productList[this.infoIndex].productId
							this.totalprice = this.order.productList[this.infoIndex].productPrice
							this.goodsPrice = this.order.productList[this.infoIndex].productPrice
						} else {
							this.goodsPrice = this.order.price
							this.totalprice = parseFloat(this.order.price * this.used_time).toFixed(2)
							this.productId = 0
						}
						this.oldPrice = this.order.price //原价
						this.getcoupon() //优惠劵 2  其他的是团购券
						this.getTuangou() //团购券
						uni.hideLoading();

					} else {
						uni.hideLoading();
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			isValidPhoneNumber(phoneNumber) {
				var pattern = /^1[3456789]\d{9}$/;
				return pattern.test(phoneNumber);
			},
			gopay() {
				if (!uni.getStorageSync('token')) {
					uni.navigateTo({
						url: '/pages/public/login'
					})
					return
				}
				if (this.startTime == 0) {
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
				if (this.isValidPhoneNumber(this.order_phone) == false) {
					uni.showToast({
						title: '请填写正确的手机号',
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
				if (this.totalprice == 0) {
					let data = {
						goodsId: this.goodsId,
						startTime: this.startdata,
						endTime: this.enddata,
						userName: this.order_name,
						phone: this.order_phone,
						payMoney: this.goodsPrice,
						remark: this.remarks,
						couponId: this.couponId,
						// is_meal: this.tabIndex,
						productId: this.productId,
						tearoomId: this.tearoomId
					}
					this.$Request.postJson('/app/orders/insertOrders', data).then(res => {
						if (res.code == 0) {
							let data = {
								ordersNo: res.data.ordersNo
							}
							this.$Request.postT('/app/orders/payOrders', data).then(red => {
								uni.hideLoading();
								if (red.code == 0) {
									uni.showToast({
										title: '购买成功',
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
										title: red.msg,
										icon: 'none'
									})
								}
							})
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 2000
							})
						}
					})
				} else {
					this.show = true
				}

				return




			},
			//生成订单
			getOrderNo() {
				if (this.isPlayIng) {
					return
				}
				this.$queue.showLoading('支付中...')
				this.show = false
				this.isPlayIng = true
				let data = {
					goodsId: this.goodsId,
					startTime: this.startdata,
					endTime: this.enddata,
					userName: this.order_name,
					phone: this.order_phone,
					payMoney: this.goodsPrice,
					remark: this.remarks,
					couponId: this.couponId,
					// is_meal: this.tabIndex,
					productId: this.productId,
					tearoomId: this.tearoomId
				}
				this.$Request.postJson('/app/orders/insertOrders', data).then(res => {
					if (res.code == 0) {
						this.ordersNo = res.data.ordersNo
						this.orderpay()
					} else {
						this.isPlayIng = false
						uni.showToast({
							title: res.msg,
							icon: 'none',
							duration: 2000
						})
					}
				})
			},
			orderpay() {
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
							that.isPlayIng = false
						} else {
							that.isPlayIng = false
							that.getcoupon()
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
							that.isPlayIng = false
							that.getcoupon()
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
									that.isPlayIng = false
									uni.hideLoading();
									uni.showToast({
										title: '支付成功',
										icon: 'none',
										duration: 2000
									})
									setTimeout(function() {
										uni.redirectTo({
											url: '/my/order/index'
										})
									}, 1000)
								},
								fail: function(err) {
									that.isPlayIng = false
									uni.hideLoading();
									that.getcoupon()
									that.$queue.showToast(
										'支付失败');
								}
							});
						} else {
							that.isPlayIng = false
							that.getcoupon()
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
							that.isPlayIng = false
							that.getcoupon()
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
							that.isPlayIng = false
							that.getcoupon()
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
							that.isPlayIng = false
							uni.hideLoading();
							setTimeout(function() {
								uni.setStorageSync('orderIndex', Number(0))
								uni.switchTab({
									url: '/pages/order/index'
								})
							}, 1000)

						} else {
							that.isPlayIng = false
							that.getcoupon()
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
							that.isPlayIng = false
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
							that.isPlayIng = false
							that.getcoupon()
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
						that.isPlayIng = false
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
						that.isPlayIng = false
						that.getcoupon()
						uni.hideLoading();
						// console.log(12)
					}
				});
			}
		}
	}
</script>

<style lang="less">
	/deep/.u-textarea__count {
		background-color: #f5f5f5 !important;
	}

	page {
		background: #F7F7F7;
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
		width: 410rpx;

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

	.bg {
		width: 100%;

	}

	.bg-box {
		width: 686rpx;
		background: #FFFAE7;
		border-radius: 24rpx;
		padding: 24rpx 30rpx;
	}

	.screen-swiper {
		width: 195rpx;
		height: 177rpx;
		// padding: 0 20rpx;
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

	.line {
		width: 100%;
		height: 1rpx;
		background: #F7F7F7;
		margin: 16rpx 0;
	}


	.tites {
		font-size: 32rpx;
		font-weight: 800;
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
		align-items: flex-end;
		justify-content: space-between;

		.price {
			display: flex;
			align-items: flex-end;

			.pp {
				color: #FF1536;
				font-size: 58rpx;
				font-weight: bold;

				text {
					font-size: 38rpx;
				}
			}

			.tt {
				font-size: 26rpx;
				font-weight: 400;
				text-decoration: line-through;
				color: #999999;
				margin-bottom: 10rpx;
				margin-left: 15rpx;
			}
		}

		.butt {
			width: 218rpx;
			height: 90rpx;
			background: #01993A;
			border-radius: 16rpx;
			text-align: center;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 32rpx;
			font-weight: 500;
			color: #FFFFFF;
		}
	}

	.headbox {
		width: 100%;

		padding-bottom: 20rpx;
	}

	.headbox-box {
		width: 686rpx;
		background: #FFFFFF;
		border-radius: 24rpx;
	}

	.tabbox {
		display: flex;
		align-items: center;

	}

	.tab {
		flex: 1;
		text-align: center;
		height: 80rpx;
		line-height: 80rpx;
	}

	.tabact {
		background: #01993A;
		font-weight: bold;
		border-radius: 24rpx 0 24rpx 0;
		color: #FFFFFF;
	}

	.tabact-r {
		background: #01993A;
		font-weight: bold;
		border-radius: 0 24rpx 0 24rpx;
		color: #FFFFFF;
	}


	.tcbox {
		width: 100%;
		display: flex;
		align-items: center;
		padding: 0 20rpx;
		justify-content: space-between;
	}

	.tcname {
		width: 140rpx;
		text-align: center;

	}

	.listbox {
		width: calc(100% - 140rpx - 20rpx);
		display: flex;

	}

	.taocan {
		text-align: center;

	}

	//套餐选择
	.scroll-view_H {
		white-space: nowrap;

	}

	.scroll-view-item_H {
		// display: inline-block;
		display: inline-flex;
		align-items: center;
		justify-content: center;
		// width: 157rpx;
		// height: 110rpx;
		padding: 20rpx 20rpx;
		background: #F2F2F2;
		border-radius: 16rpx;
		text-align: center;
		background: #F2F2F2;
		margin-right: 20rpx;
		font-size: 26rpx;
		color: #333333;
	}

	.scroll-view-item_Act {
		background: #01993A;
		font-weight: 500;
		color: #FFFFFF;
	}

	.tcprice {
		margin-top: 5rpx;
		font-size: 30rpx;
		font-weight: bold;
		// color: #FFFFFF;

	}

	.tcprice text {
		font-size: 24rpx;
		font-weight: bold;
		// color: #FFFFFF;
	}

	// 时间选择
	.databox {
		display: inline-flex;
		align-items: center;
	}

	.startr {
		width: 220rpx;
		height: 80rpx;
		background: #FFFFFF;
		border: 2px solid #01993A;
		border-radius: 16rpx;
		text-align: center;
	}

	.end {
		width: 230rpx;
		height: 80rpx;
		background: #FFFFFF;
		border: 2px solid #01993A;
		border-radius: 16rpx;
		text-align: center;
	}

	//时长选择
	.scroll-view-item_sc {
		display: inline-flex;
		align-items: center;
		justify-content: center;
		padding: 15rpx 30rpx;
		background: #FFFFFF;
		border: 1rpx solid #F2F2F2;
		border-radius: 16rpx;
		text-align: center;
		margin-right: 20rpx;
		font-size: 26rpx;
		color: #333333;
	}

	.scroll-view-item_scAct {
		// border: 1px solid #F3D6AE;
		background: #E6FFEF;
		color: #01993A;
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

	// 优惠劵
	.scroll-Y {
		height: 580rpx;
	}

	.money_box {
		// height: 300rpx;
		// line-height: 300rpx;
		// text-align: center;
		// font-size: 36rpx;
		width: 100%;
		margin: 0 auto;
		background: #ffffff;
		border-radius: 14upx;
		height: 220rpx;
		margin-bottom: 20upx;
		margin-top: 20rpx;
	}

	.box_tit {
		width: 90%;
		margin: 0 auto;
		height: 80upx;
		display: flex;
	}

	.money_name {
		flex: 1;
		display: flex;
		justify-content: left;
		align-items: center;
		font-size: 27rpx;
		font-weight: bold;
		letter-spacing: 2upx;
	}

	.money_price {
		flex: 1;
		display: flex;
		justify-content: flex-end;
		align-items: center;
		font-size: 48upx;
		font-weight: bold;
		color: red;

		text {
			font-size: 30rpx;
		}
	}

	.money_data {
		color: #999999;
		font-size: 24rpx;
		width: 90%;
		margin: 0 auto;
		margin-top: -8upx;
	}

	.u-line {
		width: 90% !important;
		border-bottom-width: 6upx !important;
		margin: 0 auto !important;
		margin-top: 22upx !important;
		margin-bottom: 22upx !important;
	}

	.box_bottom {
		width: 90%;
		margin: 0 auto;
		display: flex;
		height: 40upx;
	}

	.money_use {
		flex: 1;
		color: #999999;
		font-size: 24rpx;
		display: flex;
		justify-content: left;
		align-items: center;
	}

	.lj_use {
		width: 150rpx;
		border: 2rpx solid #01993A;
		color: #01993A;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
	}

	.deteJuan {
		width: 190rpx;
		border: 2rpx solid #01993A;
		color: #01993A;
		text-align: center;
		line-height: 48rpx;
		border-radius: 40rpx;
		font-size: 23rpx;
		margin: 20rpx 0;
	}
</style>