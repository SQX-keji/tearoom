<template>
	<view class="content">
		<view class="fw-box" v-if="swiperList.length!=0">
			<swiper class="screen-swiper" :circular="true" :autoplay="true" interval="2500" duration="800">
				<swiper-item @click="goNavSwiper(item.url)" v-for="(item,index) in swiperList" :key="index">
					<image :src="item.imageUrl" style="width: 750rpx;height: 444rpx;"></image>
				</swiper-item>
			</swiper>
		</view>
		<!-- <image src="https://qipaishi.xianmxkj.com/upload/topindeximage.png" style="width: 750rpx;height: 484rpx;"></image> -->
		<view class="top">
			<view class="headseach">
				<view class="searchleft" @click="selectCity()">
					<view>{{city}}</view>
					<image src="../../static/images/index/up.png"></image>
				</view>
				<view class="searchright" @click="goSearch">
					<u-search placeholder="请输入关键字搜索" v-model="keyword" :showAction="false" bgColor="#FEF9E2"
						searchIconColor="#01993A" color="#01993A" @click="goSearch" placeholderColor="#01993A"
						style="width: 100%;" height="68rpx" plachholderStyle="font-size:20rpx"
						:readonly="true"></u-search>
				</view>
			</view>

			<view class="flex padding" v-if="gridData.length > 0 && shangxian != '否'">
				<image :src="gridData[0].imageUrl" @click="goNav(gridData[0].url)"
					style="width: 333rpx;height: 400rpx;"></image>
				<view class="margin-left">
					<image :src="gridData[1].imageUrl" @click="goNav(gridData[1].url)"
						style="width: 341rpx;height: 191rpx;"></image>
					<view class="flex justify-between margin-top-xs">
						<image :src="gridData[2].imageUrl" @click="goNav(gridData[2].url)"
							style="width: 160rpx;height: 192rpx;"></image>
						<image :src="gridData[3].imageUrl" @click="goNav(gridData[3].url)"
							style="width: 160rpx;height: 192rpx;"></image>
					</view>
				</view>
			</view>


			<!-- <view class="fw-box" v-if="swiperList.length!=0">
				<swiper class="screen-swiper" :circular="true" :autoplay="true" interval="2500" duration="800">
					<swiper-item @click="goNavSwiper(item.banner_url)" v-for="(item,index) in swiperList" :key="index">
						<image :src="item.banner" mode="aspectFill" class="radius"></image>
					</swiper-item>
				</swiper>
			</view>
			<view class="margin-top padding-lr-xs" style="color: #333333;margin-bottom: 15rpx;"
				v-if="gridData.length!=0 && shangxian=='否'">
				<u-grid :col="5" :border="false">
					<u-grid-item v-for="(item,index) in gridData" :key='index' @click="goNav(item.banner_url)">
						<image :src="item.banner" style="width: 83rpx;height: 83rpx;border-radius: 92rpx;">
						</image>
						<view class="grid-text margin-top-xs">{{item.banner_name}}</view>
					</u-grid-item>
				</u-grid>
			</view> -->
			<!-- <view class="gonggao" v-if="messageList.length > 0">
				<view style="margin-top: 8upx;" class="margin-right">
					<image src="../../static/images/index/gonggao.png" style="width: 110upx;height: 30upx;">
					</image>
				</view>
				<swiper :autoplay="true" :vertical="true" :interval="4000" :circular="true" :indicator-dots="false"
					class="index_gonggao">
					<block v-for="(item, index) in messageList">
						<swiper-item @tap='toGoodsInfo(item.url)'>
							<view class="">{{item.title}}</view>
						</swiper-item>
					</block>
				</swiper>
			</view> -->
		</view>
		<image src="https://qipaishi.xianmxkj.com/upload/mdtj.png"
			style="width: 153rpx;height: 34rpx;margin: 20rpx 30rpx 0rpx;"></image>
		<view class="filter flex align-center justify-center">
			<view class="filter-box flex align-center justify-between">
				<view class="filter-box-item flex align-center" @click="selectFilter(index)"
					:class="current==index?'filterActive':''" v-for="(item,index) in filterList" :key="index">
					<view class="flex align-center">
						{{item.name}}
						<view class="" v-if="index !=0">
							<u-icon name="arrow-up-fill" :color="current==index && item.ishow==true?'#01993A':'#999999'"
								size="10"></u-icon>
							<u-icon name="arrow-down-fill" style="margin-top: -4rpx;"
								:color="current==index && item.ishow==false?'#01993A':'#999999'" size="10"></u-icon>
						</view>
					</view>
				</view>
			</view>
		</view>
		<view class="head">
			<view class="margin-lr">
				<view class="listbox" v-for="(item,index) in dataList" :key="index"
					@click="goOrder(item.tearoomId,item.status)">
					<view class="listbox-img">
						<image :src="item.tearoomTitleImage"></image>
						<!-- <view class="listbox-img-label"
							:class="item.ban_status == 2?'dayang':(item.ban_status==1?'':'yiman')">
							{{item.ban_status==1?'营业中':(item.ban_status==2?'已打烊':'今日已满')}}
						</view> -->
						<view v-if="item.status==1" class="listbox-img-label ">营业中</view>
						<view v-if="item.status==2" class="listbox-img-label dayang">已打烊</view>
						<view v-if="item.status!=1&&item.status!=2" class="listbox-img-label yiman">今日已满</view>
					</view>
					<view class="boxright">
						<view class="title">{{item.tearoomName}}</view>
						<view class="flex align-center justify-between  margin-top-xs">
							<view>
								<image src="../../static/images/index/pf.png"
									style="width: 24rpx;height: 22rpx;margin-right: 10rpx;">
								</image>
								{{item.score?item.score:5}}
								<text
									style="color: #666666;margin-left: 15rpx;font-size: 24rpx;">销量{{item.sales ? item.sales : 0}}</text>
							</view>
							<view style="color: #666666;">{{item.distance}}</view>
						</view>
						<view class="flex align-center flex-wrap margin-tb-xs" v-if="item.tearoomLabel">
							<view class="tb" v-for="(ite,ind) in item.tearoomLabel.split(',')" :key="ind">{{ite}}</view>
						</view>
						<view class="data">营业时间：{{item.startTime}}-{{item.endTime}}</view>
					</view>
				</view>
			</view>
		</view>

		<view class="headleft">
			<view @click="opendoor()">
				<image src="../../static/images/index/kdm.png" mode="" style="width: 100rpx;height: 100rpx;"></image>
			</view>
			<view class="margin-top-sm" @click="openbx()">
				<image src="../../static/images/index/kbx.png" mode="" style="width: 100rpx;height: 100rpx;"></image>
			</view>
			<view class="margin-top-sm" @click="goDet()">
				<image src="../../static/images/index/xd.png" mode="" style="width: 100rpx;height: 100rpx;"></image>
			</view>
		</view>
		<!-- 暂无数据 -->
		<empty v-if="dataList.length == 0" />
		<!-- 加载更多 -->
		<u-loadmore v-if="dataList.length>0" :status="status" />

		<!-- 新人红包 -->
		<u-popup :show="popushongbao" @close="popushongbao = false" mode="center" bgColor="transparent">
			<view>
				<image @tap="GetQuan"
					src="https://songshui.xianmaxiong.com/file/uploadPath/2022/11/28/737f1212e0e0c39fbaccc5b9091a2d73.png"
					style="width: 564upx;height:618upx "></image>
			</view>
		</u-popup>

	</view>
</template>

<script>
	import empty from '../../components/empty.vue'
	export default {
		components: {
			empty
		},
		data() {
			return {

				status: 'loadmore',
				current: 0,
				filterList: [{
						name: '综合排序',
					},
					{
						name: '销量',
						ishow: true,
					},
					{
						name: '距离',
						ishow: true,
					}
				],
				keyword: '',
				swiperList: [{
					id: 1,
					banner: 'https://qipaishi.xianmaxiong.com/uploads/20231026/f77abd3d51f534bb753b947b752d9343.png'
				}],
				gridData: [],
				messageList: [],
				dataList: [],
				latitude: '',
				longitude: '',
				limit: 10,
				page: 1,
				city: '',
				pages: 0,
				userId: '',
				popushongbao: false,
				token: '',
				arr: [], //通知订阅
				showModal1: true,
				shangxian: '否',
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
		onLoad(e) {
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
			this.getGrid()
			let that = this
			// #ifdef MP-WEIXIN
			if (e.scene) {
				const scene = decodeURIComponent(e.scene);
				that.$queue.setData('inviterCode', scene.split(',')[0]);
			}
			// #endif

			// 获取邀请码保存到本地
			if (e.invitation) {
				that.$queue.setData('inviterCode', e.invitation);
			}

			uni.getLocation({
				type: 'wgs84', //返回可以用于uni.openLocation的经纬度 gcj02
				success(res) {
					that.latitude = res.latitude
					that.longitude = res.longitude
					that.$Request.get('/app/Login/selectCity?lat=' + res.latitude + '&lng=' + res.longitude)
						.then(
							res => {
								if (res.code == 0) {
									that.city = res.data ? res.data : '未知'
									uni.setStorageSync('city', res.data)
									that.page = 1
									that.getDataList()
								}
							});
					//如果已经有定位城市，则直接请求数据，不在进行定位获取
					// if (uni.getStorageSync('city')) {
					// 	that.city = uni.getStorageSync('city')
					// 	that.getDataList()
					// } else {
					// 	that.$Request.get('/app/Login/selectCity?lat=' + res.latitude + '&lng=' + res.longitude)
					// 		.then(
					// 			res => {
					// 				if (res.code == 0) {
					// 					that.city = res.data ? res.data : '未知'
					// 					uni.setStorageSync('city', res.data)
					// 					that.page = 1
					// 					that.getDataList()
					// 				}
					// 			});

					// }
				}
			});

		},
		onShow() {

			this.getBannerList()
			this.page = 1
			this.getDataList()
			if (uni.getStorageSync('shangxian')) {
				this.shangxian = uni.getStorageSync('shangxian')
			}
			this.token = uni.getStorageSync('token')
			let that = this
			uni.$on('city', data => {
				that.city = data
				uni.setStorageSync('city', data)
				that.getDataList()
			})
			// if (!this.city || this.city == '') { //没有城市则先通过经纬度获取城市
			// 	this.getCity()
			// } else {
			// 	this.getAddressInfo()
			// }
			if (this.token) {
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
				// 用户呼叫通知	822
				this.$Request.get('/app/common/type/822').then(res => {
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
				// //是否开启新人优惠券
				this.$Request.getT('/app/common/type/814').then(red => {
					if (red.code == 0 && red.data && red.data.value == '是') {
						this.$Request.get("/app/user/selectUserById").then(res => {
							if (res.code == 0) {
								if (res.data.isNewCoupon != 1) {
									this.popushongbao = true
								}
							}
						});
					}
				});
			}
		},
		//刷新
		onPullDownRefresh() {
			this.page = 1
			this.getDataList()
			// if (!this.city || this.city == '') { //没有城市则先通过经纬度获取城市
			// 	this.getCity()
			// } else {
			// 	this.getAddressInfo()
			// }

		},
		//加载更多
		onReachBottom() {
			if (this.page < this.pages) {
				this.page += 1
				this.status = 'loading'
				this.getDataList()
				// if (!this.city || this.city == '') { //没有城市则先通过经纬度获取城市
				// 	this.getCity()
				// } else {
				// 	this.getAddressInfo()
				// }
			} else {
				this.status = 'nomore'
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
			GetQuan() { //领取新人优惠劵
				let userId = uni.getStorageSync('userId')
				this.$Request.post('/app/selfCouponUser/userCoupon').then(res => {
					if (res.status == 0) {
						// this.first = false
						this.popushongbao = false
						uni.showToast({
							title: '领取成功',
							icon: 'none',
						})
						setTimeout(d => {
							uni.navigateTo({
								url: '/my/youhuijuan/index'
							});
						}, 1000);
					}
				});
			},
			goDet() { //续单
				if (this.token) {
					this.$Request.getT("/app/orders/selectAddOrders").then(res => {
						if (res.code == 0) {
							if (res.data && res.data.ordersNo) {
								uni.navigateTo({
									url: '/my/order/pay?ordersNo=' + res.data.ordersNo
								})
							} else {
								uni.showToast({
									title: '您当前没有可续单的订单',
									icon: 'none',
									duration: 2000
								})
							}

						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 1500
							})
						}
					});
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			opendoor() { //开大门
				if (this.token) {
					this.$Request.postT("/app/orders/openTearoomGate").then(res => {
						if (res.code == 0) {
							uni.showToast({
								title: '大门已打开',
								icon: 'none',
								duration: 1500
							})
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 1500
							})
						}
					});
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			openbx() { //开包厢
				if (this.token) {
					this.$Request.postT("/app/orders/openGoodsGate").then(res => {
						if (res.code == 0) {
							uni.showToast({
								title: '包厢已打开',
								icon: 'none',
								duration: 1500
							})
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 1500
							})
						}
					});
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
			//选择城市
			selectCity() {
				uni.navigateTo({
					url: '/my/city/city'
				})
			},
			//根据经纬度获取城市
			getCity() {
				let that = this
				uni.getLocation({
					type: 'gcj02', //返回可以用于uni.openLocation的经纬度
					success(res) {
						that.latitude = res.latitude
						that.longitude = res.longitude
						let data = {
							lng: res.longitude,
							lat: res.latitude
						}
						that.$Request.post('app/Login/selectCity', data).then(res => {
							if (res.code == 0) {
								that.city = res.data
								uni.setStorageSync('city', res.data)
								that.getDataList()
							} else {
								uni.showToast({
									title: res.msg,
									icon: 'none'
								})
							}
						})
					}
				});
			},
			//获取位置信息
			getAddressInfo() {
				let that = this
				uni.getLocation({
					type: 'gcj02', //返回可以用于uni.openLocation的经纬度
					success(res) {
						that.latitude = res.latitude
						that.longitude = res.longitude
						that.getDataList()
					}
				});
			},
			//筛选
			selectFilter(index) {
				if (this.current === index) {
					this.filterList[index].ishow = !this.filterList[index].ishow;
				} else {
					this.filterList[index].ishow = true;
					if (this.current !== -1) {
						this.filterList[this.current].ishow = false;
					}
					this.current = index;
				}
				this.getDataList()
			},
			getDataList() {
				let type = ''
				if (this.current == 0) { //全部
					type = '1'
				} else if (this.current == 1) { //销量
					if (this.filterList[this.current].ishow == true) { //从少到多
						type = '2'
					} else { //从多到少
						type = '3'
					}
				} else if (this.current == 2) { //距离
					if (this.filterList[this.current].ishow == true) { //从近到远
						type = '4'
					} else { //从远到近
						type = '5'
					}
				}

				let data = {
					page: this.page,
					limit: this.limit,
					latitude: this.latitude,
					longitude: this.longitude,
					city: this.city,
					sort: type,
					status: 1

				}
				this.$Request.getT('/app/tearoom/selectTearoomList', data).then(res => {
					uni.stopPullDownRefresh()
					uni.hideLoading()
					if (res.code == 0) {
						this.pages = res.data.totalPage
						if (this.page < this.pages) {
							this.status = 'loadmore'
						} else {
							this.status = 'nomore'
						}
						res.data.list.map(item => {
							if ((parseFloat(item.distance)).toFixed(1) > 1000) { //距离大于1000米则单位换算成km
								item.distance = (parseFloat(item.distance) / 1000).toFixed(1) + 'km'
							} else {
								item.distance = (parseFloat(item.distance)).toFixed(1) + 'm'
							}
						})
						if (this.page == 1) {
							this.dataList = res.data.list
						} else {
							this.dataList = [...this.dataList, ...res.data.list]
						}
					}
				})
			},
			//轮播图跳转
			goNavSwiper(url) {
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
				} else { //客服二维码页面
					uni.navigateTo({
						url: '/my/setting/customer'
					})
				}
			},
			//获取轮播图
			getBannerList() {
				this.$Request.get("/app/banner/selectBannerList", {
					classify: 1
				}).then(res => {
					if (res.code == 0) {
						this.swiperList = res.data
					}
				});
			},
			// 获取金刚区分类
			getGrid() {
				this.$Request.get("/app/banner/selectBannerList", {
					classify: 2
				}).then(res => {
					if (res.code == 0) {
						this.gridData = res.data
					}
				});
			},
			// //获取banner轮播图跟公告
			// getBannerListOrGonggao() {
			// 	this.$Request.post('api/index/index/new_index').then(res => {
			// 		if (res.code == 1) {
			// 			this.messageList = res.data.gonggao //公告
			// 			this.swiperList = res.data.banner //banner轮播图
			// 			this.gridData = res.data.king_list
			// 		}
			// 	})
			// },
			//去搜索页
			goSearch() {
				uni.navigateTo({
					url: '/my/search/index'
				})
			},
			//跳转详情
			goOrder(tearoomId, status) {
				if (uni.getStorageSync('token')) {
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
					if (status == 2) {
						uni.showToast({
							title: '茶馆已打烊，请重新选择',
							icon: 'none'
						})
						return
					}
					if (status != 1 && status != 2) {
						uni.showToast({
							title: '茶馆预定已满，请重新选择',
							icon: 'none'
						})
						return
					}
					uni.navigateTo({
						url: '/pages/index/order?tearoomId=' + tearoomId
					})
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}

			},
			// 跳转游戏列表
			goNav(url) {
				if (this.token) {
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
					if (uni.getStorageSync('sendMsg')) {
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
					if (url.indexOf('/pages/') !== -1 || url.indexOf('/my/') !== -1) {
						uni.navigateTo({
							url
						});
					} else {
						//#ifndef H5
						uni.navigateTo({
							url: '/pages/index/webView?url=' + url
						});
						//#endif
						//#ifdef H5
						window.location.href = url;
						//#endif
					}
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					})
				}
			},
		}
	}
</script>

<style lang="less">
	page {
		background: #FFFFFF;
	}

	.filterActive {
		color: #333333 !important;
		font-weight: bold;
	}

	.filter {
		width: 100%;
		height: 100rpx;

		.filter-box {
			width: 686rpx;
			height: 80rpx;
		}

		.filter-box-item {
			width: 200rpx;
			color: #999999;
		}

		.filter-box-item:nth-of-type(2) {
			justify-content: center;
		}

		.filter-box-item:nth-of-type(3) {
			justify-content: flex-end;
		}
	}

	// /deep/.u-search__content {
	// 	background: rgba(0, 0, 0, 0.25) !important;
	// }

	/deep/.u-search__content__input {
		background: rgba(0, 0, 0, 0) !important;
	}

	.content {
		position: relative;
	}

	.top {
		width: 100%;
		padding-top: 30rpx;
		background: #FFFFFF;
		border-top-left-radius: 30rpx;
		border-top-right-radius: 30rpx;
		margin-top: -30rpx;
		position: relative;
		z-index: 99;

	}

	.headseach {
		width: 100%;
		display: flex;
		align-items: center;
		justify-content: space-between;
		padding: 0rpx 30rpx;
		// position: fixed;
		// top: 0;
		// left: 0;
		// right: 0;
		// z-index: 999;
		background: #FFFFFF;
		// background: linear-gradient(180deg, #FAD8A8 0%, #fadcab 100%);

		.searchleft {
			width: 25%;
			font-size: 30rpx;
			font-weight: bold;
			display: flex;
			align-items: center;
			justify-content: center;

			image {
				width: 19rpx;
				height: 12rpx;
				margin-left: 10rpx;
			}
		}

		.searchright {
			width: 75%;
			background: #FEF9E2;
			border-radius: 34rpx;
		}
	}



	.fw-box {
		// margin-top: 100rpx;
		// margin-top: 20rpx;
		// padding: 0 30rpx;

		.screen-swiper {
			height: 444rpx;
		}
	}

	.gonggao {
		display: flex;
		align-items: center;
		border-radius: 12rpx;
		background: #FFFFFF;
		// margin: 30rpx 25rpx;
		margin: 0 25rpx;
		margin-top: 30rpx;
		padding: 0 30rpx;
		position: relative;
		z-index: 9;

		.index_gonggao {
			color: #666666;
			width: 550rpx;
			height: 68rpx;
			// background: #EAF4FE;
			border-radius: 50rpx;
			align-items: center;
			line-height: 50rpx;
			padding: 10rpx 15rpx;

		}
	}

	.yiman {
		background-color: #eb3940 !important;
	}

	.dayang {
		background-color: #bfbfbf !important;
	}

	.listbox {
		background: #FFFAE7;
		border-radius: 24rpx;
		padding: 30rpx 30rpx;
		display: flex;
		margin-bottom: 20rpx;
		position: relative;
		z-index: 9;

		.listbox-img {
			position: relative;

			image {
				width: 160rpx;
				height: 160rpx;
				border-radius: 15rpx;
			}

			.listbox-img-label {
				position: absolute;
				top: 0;
				left: 0;
				color: #FFFFFF;
				font-size: 24rpx;
				padding: 5rpx 10rpx;
				border-radius: 15rpx 0 15rpx 0;
				background-color: #5cbe74;
			}

		}


		.boxright {
			margin-left: 15rpx;
			width: 75%;
			font-size: 24rpx;

			.title {
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

			.tb {
				background: #E6FFEF;
				border-radius: 4rpx;
				font-size: 24rpx;
				font-family: PingFang SC;
				font-weight: 500;
				color: #01993A;
				padding: 6rpx 15rpx;
				margin-right: 10rpx;
				margin-top: 8rpx;
			}

			.data {
				color: #666666;
				font-size: 24rpx;
				margin-top: 8rpx;
			}
		}
	}

	.headleft {
		position: fixed;
		right: 40rpx;
		bottom: 120rpx;
		z-index: 999;
	}
</style>