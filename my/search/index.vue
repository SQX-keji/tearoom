<template>
	<view class="content">
		<u-sticky>
			<view class="search-box">
				<u-search style="width: 100%;" placeholder="请输入关键字搜索" :focus="true" v-model="keyword"
					:show-action="true" :animation="true" shape="square" bg-color="#F7F7F7" color="#1A1A1A"
					action-text="取消" @custom="goBack()" @search="doSearch(false)"></u-search>
			</view>
			<view v-show="isShow == false">
				<view class="filter flex align-center justify-center">
					<view class="filter-box flex align-center justify-between">
						<view class="filter-box-item flex align-center" @click="selectFilter(index)"
							:class="current==index?'filterActive':''" v-for="(item,index) in filterList" :key="index">
							<view class="flex align-center">
								{{item.name}}
								<view class="" v-if="index !=0">
									<u-icon name="arrow-up-fill" style="scale: 0.7;"
										:color="current==index && item.ishow==true?'#01993A':'#999999'"
										size="10"></u-icon>
									<u-icon name="arrow-down-fill" style="scale: 0.7;margin-top: -4rpx;"
										:color="current==index && item.ishow==false?'#01993A':'#999999'"
										size="10"></u-icon>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</u-sticky>
		<view class="historys" v-if="isShow">
			<view class="keyword-block" v-if="hotKeywordList.length!=0">
				<view class="keyword-list-header">
					<view>热门搜索</view>
					<view  @tap="hotToggle">
						<u-icon v-if="isStart" name="eye-off" color="#999999" size="24"></u-icon>
						<u-icon v-else name="eye-fill" color="#999999" size="24"></u-icon>
					</view>
				</view>
				<view class="keyword" v-if="isStart">
					<view v-for="(keyword,index) in hotKeywordList" @tap="doSearch(keyword)" :key="index">
						{{keyword}}
					</view>
				</view>
				<view class="hide-hot-tis" v-else>
					<view>当前搜热已隐藏</view>
				</view>
			</view>
			<!-- <view class="keyword-block" v-if="oldKeywordList.length>0">
				<view class="keyword-list-header">
					<view>历史记录</view>
					<view>
						<image @tap="oldDelete" src="/static/images/index/delete.png"></image>
					</view>
				</view>
				<view class="keyword">
					<view v-for="(keyword,index) in oldKeywordList" @tap="doSearch(keyword)" :key="index">
						{{keyword}}
					</view>
				</view>
			</view> -->
		</view>
		<view class="list" style="margin-top: 20rpx;" v-else>
			<view class="listbox" v-for="(item,index) in dataList" :key="index" @click="goOrder(item.tearoomId)">
				<image :src="item.tearoomCoverImage"></image>
				<view class="boxright">
					<view class="title">{{item.tearoomName}}</view>
					<view class="flex align-center justify-between  margin-top-xs">
						<view>
							<image src="../../static/images/index/pf.png" style="width: 24rpx;height: 22rpx;">
							</image>
							{{item.score}}
							<text style="color: #666666;margin-left: 15rpx;font-size: 24rpx;">销量{{item.sales ? item.sales : 0}}</text>
						</view>
						<view style="color: #666666;">{{item.distance}}</view>
					</view>
					<view class="flex align-center flex-wrap" v-if="item.tearoomLabel">
						<view class="tb" v-for="(ite,ind) in item.tearoomLabel.split(',')" :key="ind">{{ite}}</view>
					</view>
					<view class="data">营业时间：{{item.startTime}}-{{item.endTime}}</view>
				</view>
			</view>
			<empty style="padding-top: 30vh;" v-if="dataList.length == 0"></empty>
			<!-- 加载更多 -->
			<u-loadmore v-if="dataList.length>0" :status="status" />
		</view>
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
				isStart: true,
				isShow: true,
				defaultKeyword: "",
				keyword: "",
				oldKeywordList: [], //历史记录
				hotKeywordList: [], //热搜
				dataList: [], //搜索列表
				limit: 10,
				page: 1,
				pages: 1,
				latitude: '',
				longitude: '',
				status: 'loadmore',
			}
		},
		onLoad() {
			this.getSearchList()
			this.getAddressInfo()
		},
		methods: {
			//获取位置信息
			getAddressInfo() {
				let that = this
				uni.getLocation({
					type: 'gcj02', //返回可以用于uni.openLocation的经纬度
					success(res) {
						that.latitude = res.latitude
						that.longitude = res.longitude
					}
				});
			},
			// 跳转店铺
			shop(userId) {
				uni.navigateTo({
					url: '/pages/index/game/shop?shopUserId=' + userId
				})
			},
			// 获取搜索历史/热搜
			getSearchList() {
				// 热搜关键字	815
				this.$Request.getT('/app/common/type/815').then(res => {
					if (res.code == 0) {
						if (res.data && res.data.value) {
							this.hotKeywordList = res.data.value.split(',') //热门搜索记录
						}
					}
				});
				// this.$Request.get("api/index/index/show_hot_search").then(res => {
				// 	console.log(res)
				// 	if (res.code == 1) {
				// 		this.hotKeywordList = res.data.hot_search //热门搜索记录
				// 		if (res.data.history_search) {
				// 			this.oldKeywordList = res.data.history_search.split(',') //历史搜索记录
				// 		}

				// 	}
				// });
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
				this.doSearch()
			},
			//执行搜索
			doSearch(keyword) {
				if (keyword) { //从热搜选择的
					this.keyword = keyword
				}
				if (!this.keyword) {
					uni.showToast({
						title: '请输入内容',
						icon: 'none',
						duration: 1000
					})
					return
				}
				this.isShow = false
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
					type:1,
					sort: type,
					longitude: this.longitude,
					latitude: this.latitude,
					limit: this.limit,
					page: this.page,
					search: this.keyword,
					city: uni.getStorageSync('city')
				}
				this.$Request.getT('/app/tearoom/selectTearoomList', data).then(res => {
					if (res.code == 0) {
						this.page = res.data.totalPage
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
			// 点击取消返回首页
			goBack() {
				uni.navigateBack()
			},
			//热门搜索开关
			hotToggle() {
				this.isStart = !this.isStart
			},
			// 跳转订单
			goOrder(tearoomId) {
				if (uni.getStorageSync('token')) {
					uni.navigateTo({
						url: '/pages/index/order?tearoomId=' + tearoomId
					});
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					});
				}
			},

		},
		onReachBottom: function() {
			if (this.page < this.pages) {
				this.page += 1
				this.status = 'loading'
				this.doSearch()
			} else {
				this.status = 'nomore'
			}
		},
		onPullDownRefresh: function() {
			this.page = 1
			this.doSearch()
		},
	}
</script>
<style lang="scss">
	page {
		background-color: #F5F5F5;
	}

	.filterActive {
		color: #333333 !important;
		font-weight: bold;
	}

	.filter {
		width: 100%;
		height: 100rpx;
		background-color: #FFFFFF;

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

	.bg {
		background-color: #FFFFFF;
	}

	.search-box {
		width: 100%;
		/* background-color: rgb(242, 242, 242); */
		padding: 15upx 2.5%;
		display: flex;
		justify-content: space-between;
		background-color: #FFFFFF;
	}

	.search-box .mSearch-input-box {
		width: 100%;
	}

	.search-box .input-box {
		width: 85%;
		flex-shrink: 1;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.search-box .search-btn {
		width: 15%;
		margin: 0 0 0 2%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-shrink: 0;
		font-size: 28upx;
		color: #fff;
		background: linear-gradient(to right, #ff9801, #ff570a);
		border-radius: 60upx;
	}

	.search-box .input-box>input {
		width: 100%;
		height: 60upx;
		font-size: 32upx;
		border: 0;
		border-radius: 60upx;
		-webkit-appearance: none;
		-moz-appearance: none;
		appearance: none;
		padding: 0 3%;
		margin: 0;
		background-color: #ffffff;
	}

	.placeholder-class {
		color: #9e9e9e;
	}

	.search-keyword {
		width: 100%;

	}

	.keyword-list-box {
		height: calc(100vh - 110upx);
		padding-top: 10upx;
		/* border-radius: 20upx 20upx 0 0; */
		/* background-color: #fff; */
	}

	.keyword-entry-tap {
		background-color: #eee;
	}

	.keyword-entry {
		width: 94%;
		height: 80upx;
		margin: 0 3%;
		font-size: 30upx;
		color: #333;
		display: flex;
		justify-content: space-between;
		align-items: center;
		border-bottom: solid 1upx #e7e7e7;
	}

	.keyword-entry image {
		width: 60upx;
		height: 60upx;
	}

	.keyword-entry .keyword-text,
	.keyword-entry .keyword-img {
		height: 80upx;
		display: flex;
		align-items: center;
	}

	.keyword-entry .keyword-text {
		width: 90%;
	}

	.keyword-entry .keyword-img {
		width: 10%;
		justify-content: center;
	}

	.keyword-block {
		padding: 10upx 0;
	}

	.keyword-block .keyword-list-header {
		width: 94%;
		padding: 10upx 3%;
		font-size: 27upx;
		font-weight: 700;
		/* color: #FFFFFF; */
		display: flex;
		justify-content: space-between;
	}

	.keyword-block .keyword-list-header image {
		width: 40upx;
		height: 40upx;
	}

	.keyword-block .keyword {
		width: 94%;
		padding: 3px 3%;
		display: flex;
		flex-flow: wrap;
		justify-content: flex-start;
	}

	.keyword-block .hide-hot-tis {
		display: flex;
		justify-content: center;
		font-size: 28upx;
	}

	.keyword-block .keyword>view {
		display: flex;
		justify-content: center;
		align-items: center;
		border-radius: 10upx;
		padding: 0 20upx;
		margin: 10upx 20upx 10upx 0;
		height: 60upx;
		font-size: 28upx;
		background-color: #ffffff;
		color: #343546;
	}

	.listbox {
		width: 686rpx;
		margin: 0 auto;
		background: #FFFFFF;
		border-radius: 24rpx;
		padding: 30rpx 30rpx;
		display: flex;
		margin-bottom: 20rpx;
		position: relative;
		z-index: 9;

		image {
			width: 159rpx;
			height: 160rpx;
			border-radius: 15rpx;
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
</style>