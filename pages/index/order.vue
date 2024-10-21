<template>
	<view style="padding-bottom: 80px;">
		<view class="info" v-if="storeName">
			<view class="flex align-center justify-center padding">
				<view class="info-box">
					<view class="info-box-label flex align-center justify-between">
						<view class="info-box-label-l flex align-center">
							<image src="../../static/images/index/pf.png">
							</image>
							{{score?score:5}}
							<!-- <view class="info-box-label-l-ite" v-for="(item,index) in labeList" :key="index">
								{{item}}
							</view> -->
							<view class="info-box-title">
								{{storeName}}
							</view>
						</view>
						<view class="info-box-label-r">
							销量{{sale ? sale : 0}}
						</view>
					</view>
					<!-- 营业时间 -->
					<view class="info-box-time flex align-center">
						营业时间：{{storeTime}}
					</view>
					<!-- 所在位置 -->
				</view>
			</view>
			<swiper class="screen-swiper" :circular="true" :autoplay="true" interval="2500" duration="800">
				<swiper-item v-for="(item,index) in swiperList" :key="index" @click="saveImg(swiperList,index)">
					<image :src="item" mode="aspectFill" class=""
						style="border-top-left-radius: 24rpx;border-top-right-radius: 24rpx;"></image>
				</swiper-item>
			</swiper>
			<view class="info-box-address">
				<image src="../../static/images/index/addressBg.png"></image>
				<view class="info-box-address-s flex align-center justify-between">
					{{storeAddress}}
					<view class="flex align-center justify-between">
						<image src="../../static/images/index/add.png" @click="selectMap()"></image>
						<image src="https://qipaishi.xianmxkj.com/upload/dianhua.png" @click="bindPhone(storePhone)">
						</image>
					</view>
				</view>
			</view>

		</view>

		<!-- tabs切换 -->
		<!-- <view class="tabs flex align-center justify-center">
			<view class="tabs-box flex align-center justify-between">
				<view class="tabs-box-item flex align-center justify-center" :class="tabIndex==index?'tabsActive':''"
					v-for="(item,index) in listtab" :key="index" @click="bindTab(index)">
					{{item.name}}
				</view>
			</view>
		</view> -->
		<!-- 套餐 -->
		<view class="list flex align-center justify-center">
			<view class="list-box">
				<packageList @click.native="godetail(item.goodsId)" v-for="(item,index) in goodsList"
					:listData="item" />
				<empty v-if="goodsList.length==0"></empty>
			</view>
		</view>
		<!-- 评论 -->
		<view class="pinglun flex align-center justify-center">
			<view class="pinglun-box">
				<view class="pinglun-box-num">
					评论({{commentCount}})
				</view>
				<block v-if="msgList.length > 0">
					<view class="pinglun-box-list">
						<view class="pinglun-box-list-item" v-for="(item,index) in msgList" :key="index">
							<view class="pinglun-box-list-item-t flex align-center justify-between">
								<view class="pinglun-box-list-item-t-l flex align-center">
									<image :src="item.avatar ? item.avatar : ' ../../static/logo.png'">
										{{item.userName?item.userName:'匿名用户'}}
								</view>
								<view class="pinglun-box-list-item-t-r">
									{{item.createTime}}
								</view>
							</view>
							<view v-if="item.image"
								class="pinglun-box-list-item-img flex align-center justify-between flex-wrap">
								<image @click.stop="lookImgs(ind,item.image.split(','))"
									v-for="(ite,ind) in item.image.split(',')" :key="ind" :src="ite" mode="">
								</image>
								<image src="" style="height: 0;" mode=""></image>
							</view>
							<view class="pinglun-box-list-item-c">
								{{item.content}}
							</view>
						</view>
					</view>
					<view @click="gopl()" class="pinglun-box-all flex align-center justify-center">
						查看全部 <u-icon name="arrow-right" style="margin-left: 10rpx;" color="#333333" size="12"></u-icon>
					</view>
				</block>
				<block v-if="msgList.length == 0">
					<empty />
				</block>

			</view>

		</view>

	</view>
</template>

<script>
	import empty from '@/components/empty.vue'
	import packageList from '../../components/packageList/packageList.vue'
	export default {
		components: {
			empty,
			packageList
		},
		data() {
			return {
				scrollTop: 0,
				old: {
					scrollTop: 0
				},
				tearoomId: '', //店铺id
				swiperList: [], //轮播图
				storeName: '', //店铺名称
				score: 0, //店铺评分
				sale: 0, //店铺销量
				storeTime: '', //营业时间
				labeList: [], //标签
				storeAddress: '', //地址
				storePhone: '',
				goodsList: [], //茶室
				commentCount: 0, //评论数
				msgList: [], //评论列表
				lat: '', //纬度
				lng: '', //经度

				listtab: [{
					name: '套餐',
				}, {
					name: '评价',
				}],
				tabIndex: 0,
				arr: [], //通知订阅
				showModal1: true,
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
				page: 1,
				limit: 10,
				invitationCode: ''
			}
		},
		onLoad(option) {
			if (option.invitation) {
				uni.setStorageSync('inviterCode', option.inviterCode)
			}
			if (option.tearoomId) {
				this.tearoomId = option.tearoomId
				this.getStoreInfo()
			}

		},
		onShareAppMessage(res) {
			return {
				path: '/pages/index/order?invitation=' + this.invitationCode + '&tearoomId=' + this
					.tearoomId, //这是为了传参   onload(data){let id=data.id;} 
				title: this.storeName,
			}
		},
		onShareTimeline() {
			return {
				path: '/pages/index/order?invitation=' + this.invitationCode + '&tearoomId=' + this
					.tearoomId, //这是为了传参   onload(data){let id=data.id;} 
				title: this.tuiguang,
			}
		},
		onShow() {
			this.invitationCode = uni.getStorageSync('invitationCode')
			if (uni.getStorageSync('token')) {
				//呼叫保洁通知 381
				// this.$Request.get('api/index/common/sys_common', {
				// 	type_id: 381
				// }).then(res => {
				// 	if (res.code == 1 && res.data && res.data.value) {
				// 		if (res.data && res.data.value) {
				// 			this.arr.push(res.data.value)
				// 		}
				// 	}
				// })
				if (this.showModal1) {
					// #ifdef MP-WEIXIN
					this.openMsg()
					// #endif
				}
				if (this.tearoomId) {
					this.getStoreInfo()
				}
			}
		},
		methods: {
			bindPhone(phone) {
				uni.makePhoneCall({
					phoneNumber: phone //仅为示例
				});
			},
			//开启订阅消息
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
			bindTab(index) {
				this.tabIndex = index
			},
			//导航地址
			selectMap() {
				console.log(this.lat, this.lng, '----------')
				let that = this
				uni.openLocation({
					latitude: Number(that.lat),
					longitude: Number(that.lng),
					address: that.storeAddress,
					name: that.storeName
				})
			},
			//预览品论图
			lookImgs(index, imgList) {
				uni.previewImage({
					urls: imgList,
					current: index
				})
			},
			//获取店铺详情
			getStoreInfo() {
				this.$queue.showLoading('加载中...')
				let data = {
					tearoomId: this.tearoomId
				}
				if (this.checkQQ) {
					return;
				}
				this.checkQQ = true;
				this.$Request.getT('/app/tearoom/selectTearoomById', data).then(res => {
					if (res.code == 0) {
						this.checkQQ = false;
						uni.hideLoading();

						this.storeName = res.data.tearoomName //店铺名
						if (res.data.tearoomName) {
							uni.setNavigationBarTitle({
								title: res.data.tearoomName
							})
						}
						this.score = res.data.score //店铺评分
						this.sale = res.data.sales //店铺销量
						this.storeTime = res.data.startTime + '~' + res.data.endTime //营业时间

						this.lat = res.data.tearoomLatitude //纬度
						this.lng = res.data.tearoomLongitude //经度
						this.storePhone = res.data.tearoomPhone
						this.storeAddress = res.data.tearoomAddress //店铺地址
						if (res.data.tearoomLabel) { //店铺标签
							this.labeList = res.data.tearoomLabel.split(',')
						}
						if (res.data.tearoomCoverImage) {
							this.swiperList = res.data.tearoomCoverImage.split(',') //轮播图
						}
						this.getStorelist()
						this.getCommentList()

					} else {
						this.checkQQ = false;
						uni.hideLoading();
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			// 获取店铺商品
			getStorelist() {
				let data = {
					page: this.page,
					limit: this.limit,
					tearoomId: this.tearoomId,
					status: 1
				}
				this.$Request.getT('/app/goods/selectGoodsList', data).then(res => {
					if (res.code == 0) {

						if (res.data.list && res.data.list.length > 0) {
							this.goodsList = [];
							res.data.list.forEach(d => {
								if (d.roomTimeList && d.roomTimeList.length > 0) {
									let timeLists = JSON.parse(JSON.stringify(this.timeList));
									timeLists.forEach(de => {
										if (d.roomTimeList.findIndex(item => item.roomTime === de
												.str_time) !== -1) {
											de.is_busy = 1;
										}
									});
									d.roomTimeList = timeLists;
								} else {
									d.roomTimeList = this.timeList;
								}
							});
						}
						this.goodsList = res.data.list //茶室
						// this.lat = res.data.tearoomLatitude //纬度
						// this.lng = res.data.tearoomLongitude //经度
					} else {
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			// 获取评论接口
			getCommentList() {
				let data = {
					page: 1,
					limit: 1,
					tearoomId: this.tearoomId,
				}
				this.$Request.getT('/app/comment/selectCommentList', data).then(res => {
					if (res.code == 0) {
						this.commentCount = res.data.totalCount //评论数
						this.msgList = res.data.list //评论数组
					} else {
						uni.showToast({
							title: res.msg,
							icon: 'none'
						})
					}
				})
			},
			godetail(goodsId) {
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
				uni.navigateTo({
					url: '/pages/index/packageDetail?goodsId=' + goodsId + '&tearoomId=' + this.tearoomId
				})
			},
			upper: function(e) {
				// console.log(e)
			},
			lower: function(e) {
				// console.log(e)
			},
			scroll: function(e) {
				// console.log(e)
				this.old.scrollTop = e.detail.scrollTop
			},
			gopl() {
				uni.navigateTo({
					url: '/my/setting/pinglun?id=' + this.tearoomId
				})
			}
		},

	}
</script>

<style lang="scss">
	page {
		// background: #000000;
		background: #F7F7F7;
	}

	.pinglun {
		width: 100%;

		.pinglun-box {
			width: 686rpx;
			background-color: #FFFFFF;
			border-radius: 24rpx;
		}

		.pinglun-box-num {
			padding: 30rpx;
			color: #333333;
			font-size: 32rpx;
			font-weight: bold;
		}

		.pinglun-box-list {
			padding: 0 30rpx;

			.pinglun-box-list-item {
				width: 100%;
			}

			.pinglun-box-list-item-t {
				width: 100%;
			}

			.pinglun-box-list-item-img {
				margin-top: 20rpx;

				image {
					width: 200rpx;
					height: 200rpx;
					border-radius: 18rpx;
					margin-bottom: 18rpx;
				}
			}

			.pinglun-box-list-item-c {
				color: #333333;
				font-size: 28rpx;
				margin-top: 20rpx;
			}

			.pinglun-box-list-item-t-l {
				color: #333333;
				font-size: 28rpx;
				font-weight: 500;

				image {
					width: 64rpx;
					height: 64rpx;
					border-radius: 50%;
					margin-right: 10rpx;
				}
			}

			.pinglun-box-list-item-t-r {
				color: #999999;
				font-size: 24rpx;
			}
		}

		.pinglun-box-all {
			width: 100%;
			height: 80rpx;
			background-color: #FFFFFF;
			border-radius: 0 24rpx 24rpx;
			color: #333333;
			font-size: 24rpx;
			border-top: 1px solid #F5F5F2;
			margin-top: 30rpx;
		}
	}

	.list {
		width: 100%;

		.list-box {
			width: 686rpx;
		}
	}

	.tabsActive {
		background: #01993A !important;
		color: #FFFFFF !important;
	}

	.tabs {
		width: 100%;
		margin: 20rpx 0;

		.tabs-box {
			width: 686rpx;
			height: 80rpx;
		}

		.tabs-box-item {
			width: calc((100% - 20rpx) / 2);
			height: 100%;
			background-color: #FFFFFF;
			border-radius: 40rpx;
			color: #442916;
			font-size: 28rpx;
			font-weight: 500;
		}
	}

	.info {
		width: 686rpx;
		margin: 0 auto;
		border-radius: 30rpx;
		margin-top: 20rpx;
		background: #FFFFFF;

		.info-box {
			width: 686rpx;
		}

		.info-box-title {
			color: #333333;
			font-size: 32rpx;
			font-weight: bold;
			margin-left: 20rpx;
		}

		.info-box-label {
			margin-top: 10rpx;
		}

		.info-box-label-l {
			color: #FF1536;
			font-size: 28rpx;
			font-weight: 500;

			image {
				width: 25rpx;
				height: 25rpx;
				margin-right: 5rpx;
			}

			.info-box-label-l-ite:nth-of-type(1) {
				margin-left: 20rpx;
			}

			.info-box-label-l-ite {
				color: #01993A;
				font-size: 20rpx;
				padding: 7rpx 10rpx;
				background-color: #E6FFEF;
				border-radius: 4rpx;
				margin-left: 10rpx;
			}
		}

		.info-box-label-r {
			color: #333333;
			font-size: 22rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #333333;
		}

		.info-box-time {
			width: 100%;
			height: 60rpx;
			// background-color: #FFEED9;
			// border-radius: 24rpx 24rpx 0rpx 0rpx;
			// padding-left: 20rpx;
			color: #333333;
			font-size: 26rpx;
			font-size: 26rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #333333;
		}

		.info-box-address {
			position: relative;
			width: 100%;
			height: 123rpx;
			border-radius: 0 0 24rpx 24rpx;
			margin-bottom: 26rpx;

			image {
				width: 100%;
				height: 100%;
			}

			.info-box-address-s {
				width: calc(100% - 40rpx);
				position: absolute;
				top: 50%;
				left: 20rpx;
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
	}

	.imgs {
		width: 100%;
		margin-top: 20rpx;

		.imgs-item {
			width: 100rpx;
			height: 100rpx;
			border-radius: 8rpx;
			margin-right: 10rpx;
			margin-bottom: 10rpx;

			image {
				width: 100%;
				height: 100%;
				border-radius: 8rpx;
			}
		}
	}

	.bg {
		background: #FFFFFF;
	}

	.screen-swiper {
		height: 350rpx;
		// width: 686rpx;
		// margin: 0 auto;
		// margin-top: 20rpx;
	}

	.scroll-Y {
		max-height: 480rpx;
	}

	.ptit {
		font-size: 32rpx;
		font-family: PingFang SC;
		font-weight: bold;
		color: #333333;
		margin-bottom: 20rpx;
	}

	.taber {
		background: #FFFFFF;
		padding: 20rpx 30rpx;
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		z-index: 99;

		.butt {
			width: 686rpx;
			height: 90rpx;
			background: linear-gradient(-90deg, #74543E 0%, #442916 100%);
			border-radius: 16rpx;
			text-align: center;
			display: flex;
			align-items: center;
			justify-content: center;
			font-size: 32rpx;
			font-family: PingFang SC;
			font-weight: 500;
			color: #FFFFFF;
		}
	}
</style>