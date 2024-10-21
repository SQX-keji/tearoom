<template>
	<view>
		<view class="calendar">
			<view class="calendar_day">
				<scroll-view class="scroll-view_H" scroll-x="true" @scroll="scroll" scroll-left="120">
					<view class="day_x" :class="day_index == index?'act':''" v-for="(item, index) in dayArr"
						:key="index" @click.stop="dayList(item,index)">
						<view class="day_x_a">{{item.weeks}}</view>
						<view class="day_x_b">{{item.days}}</view>
						<!-- <view class="day_x_c">预约</view> -->
					</view>
				</scroll-view>

			</view>

			<!-- <view class="" style="width: 100%;height: 70vh;margin-top: 20rpx;">
				<scroll-view scroll-y="true" style="width: 100%;height: 100%;">
					<view class="calendar_time flex flex-wrap justify-between">
						<view class="time_x flex align-center justify-center flex-wrap"
							:class="item.isSelect ? 'time_x_sty' : ''" :style="item.is_busy!=0?'background-color:#5a351d;color:#fff':''" v-for="(item, index) in hostArr" :key="index"
							@click.stop="hosts(item,index)">
							<view class="">
								{{item.str_time}}~{{item.end_time}}
							</view>
							<view class="">
								{{item.is_busy==0?'空闲':'占用'}}
							</view>
						</view>
						<view class="time_x" style="height: 0;"></view>
						<view class="time_x" style="height: 0;"></view>
					</view>
				</scroll-view>
			</view> -->
		</view>
		<!-- <view class="btn" @click="submit()">确认</view> -->
	</view>
</template>

<script>
	import moment from 'moment'
	export default {
		props: {
			//茶室id
			goodsId: {
				type: Number | String,
				default: ''
			}
		},
		data() {
			return {
				dayArr: [],
				hostArr: [],
				day_index: 0,
				nowTimes: '',
				scrollTop: 0,
				old: {
					scrollTop: 0
				},
				yearTime: '',
				selectList: [], //已经选中的
				minTime: 1, //最小选中时间
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
				}]

			}
		},
		mounted() {
			this.minTime = uni.getStorageSync('make_min_time')
			let dateArr = []
			let today = new Date();
			let nowTime = today.getTime() // 当前时间戳
			this.nowTimes = parseInt(nowTime / 1000)
			for (let i = 0; i < 7; i++) {
				let newDate = new Date(today.getTime() + i * 1000 * 60 * 60 * 24)
				let month = (parseInt(newDate.getMonth()) + 1) > 9 ? (parseInt(newDate.getMonth()) + 1) : "0" + (parseInt(
					newDate.getMonth()) + 1) // 当前月份
				let day = (newDate.getDate()) > 9 ? newDate.getDate() : "0" + newDate.getDate() //当前日期
				let backTime = newDate.getTime() // 几天后时间戳
				let backDays = newDate.getDay() // 几天后周几
				let remTime = (backTime - nowTime) / 1000 // 距离今天几天
				let data = moment(newDate).format("yy-MM-DD")
				// console.log(moment(newDate).format("yy-MM-DD"), '============')
				let week = ''
				if (remTime == 0) {
					week = "今天"
				} else if (remTime == 86400) {
					week = "明天"
				} else if (remTime == 172800) {
					week = "后天"
				} else {
					if (backDays == 0) {
						week = "周日"
					} else if (backDays == 1) {
						week = "周一"
					} else if (backDays == 2) {
						week = "周二"
					} else if (backDays == 3) {
						week = "周三"
					} else if (backDays == 4) {
						week = "周四"
					} else if (backDays == 5) {
						week = "周五"
					} else if (backDays == 6) {
						week = "周六"
					}
				}
				// let fullDate = `${month}-${day}`
				let fullDate = month + '月' + day + '日'

				let ass = {
					weeks: week,
					days: fullDate,
					data: data
				}
				dateArr.push(ass)
			}
			this.dayArr = dateArr
			// console.log(this.dayArr,'========')
			this.yearTime = this.dayArr[0].data
			this.getDataTime('-1')
		},
		methods: {
			//确认选中
			submit() {
				let data = this.dayArr[this.day_index].data
				// console.log(data, '======')
				// console.log(moment().format("yy-MM-DD HH:mm:ss"))
				let arr = []
				this.hostArr.map(item => {
					if (item.isSelect) {
						arr.push(item)
					}
				})
				if (arr.length == 0) {
					uni.showToast({
						title: '请选择空闲时间',
						icon: 'none'
					})
					return
				}
				let start = arr[0]
				let endtime = arr[arr.length - 1]
				let startdata = data + ' ' + start.str_time
				let enddata = data + ' ' + endtime.end_time
				console.log(startdata, '==', enddata)
				const Time = moment(enddata).diff(moment(startdata), 'hours')
				// console.log(Time, Time <= this.minTime,'data1data1')
				if (Time < this.minTime) {
					uni.showToast({
						title: '选中时间不能小于' + this.minTime + '小时',
						icon: 'none'
					})
					return
				}
				let arrdata = this.dayArr[this.day_index].days + ' ' + start.str_time + '~' + endtime.end_time
				let arrlist = {
					arrdata: arrdata,
					startdata: startdata,
					enddata: enddata
				}
				// console.log(arrdata)
				// this.$emit('submitData', arrlist)
			},
			// 根据日期获取时间
			getDataTime(index) {
				this.$queue.showLoading('加载中...')
				// if (index == '-1') {
				// 	this.yearTime = ''
				// }
				let data = {
					goodsId: this.goodsId,
					date: this.yearTime,
				}
				this.$Request.getT('/app/roomTime/selectRoomTimeByDate', data).then(res => {
					if (res.code == 0) {
						// res.data.map(item => {
						// 	item.isSelect = false
						// })
						if (res.data && res.data.length > 0) {
							let timeLists = JSON.parse(JSON.stringify(this.timeList));
							let timesList = []
							var myDate = new Date();
							let nowDate = moment().format("yy-MM-DD")
							timeLists.forEach(de => {
								// console.log(parseInt(de.str_time))
								// console.log(parseInt(myDate.getHours()))
								if(nowDate==this.yearTime){
									if (Number(parseInt(de.str_time)) > Number(parseInt(myDate.getHours()))) {
										if (res.data.findIndex(item => item.roomTime === de.str_time) !== -
											1) {
											de.is_busy = 1;
										}
										timesList.push(de)
									}
								}else{
									if (res.data.findIndex(item => item.roomTime === de.str_time) !== -
										1) {
										de.is_busy = 1;
									}
									timesList.push(de)
								}
								
							});
							res.data = timesList;
							this.hostArr = res.data
						} else {
							this.hostArr = this.timeList
						}
						if (index != '-1') {
							this.day_index = index
						} else {
							this.day_index = 0
						}
						let time = {
							hostArr: this.hostArr,
							dayArr: this.dayArr,
							yearTime: this.yearTime,
							index: index
						}
						this.$emit('submitData', time)
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
			scroll: function(e) {
				this.old.scrollTop = e.detail.scrollTop
			},
			// 点击日期
			dayList(e, index) {
				if (index != this.day_index) { //切换
					//获取切换的日期
					let year = new Date().getFullYear()
					let month = e.days.substring(0, e.days.indexOf('月'))
					let day = e.days.substring(e.days.indexOf('月') + 1, e.days.length - 1)
					//获取当前系统的日期
					let lowMonth = ''
					if (new Date().getMonth() + 1 > 9) {
						lowMonth = new Date().getMonth() + 1
					} else {
						lowMonth = '0' + (new Date().getMonth() + 1)
					}
					let lowDay = ''
					if (new Date().getDate() > 9) {
						lowDay = new Date().getDate()
					} else {
						lowDay = '0' + new Date().getDate()
					}
					if ((month + '-' + day) == (lowMonth + '-' + lowDay)) { //如果是今天
						this.yearTime = ''
					} else {
						this.yearTime = year + '-' + month + '-' + day
					}
				}
				this.getDataTime(index)
				this.dayTime = this.yearTime
			},
			// 点击时间
			hosts(item, index) {
				if (item.is_busy == 1) {
					uni.showToast({
						title: '该时间段已经被预约,请选择其他时间段',
						icon: 'none'
					})
					return
				}
				// 如果点击的isSelect为false，则修改为true
				if (!item.isSelect) {
					item.isSelect = true;
				} else {
					// 如果点击的isSelect为true，则修改为false
					item.isSelect = false;
				}

				// 判断是否有多个选中项
				let selectedCount = 0; // 记录选中的数量
				let startIndex = -1; // 记录第一个选中项的索引
				let endIndex = -1; // 记录最后一个选中项的索引
				for (let i = 0; i < this.hostArr.length; i++) {
					if (this.hostArr[i].isSelect) {
						selectedCount++;
						if (startIndex === -1) {
							startIndex = i;
						}
						endIndex = i;
					}
				}
				// 如果只有一个选中项，则不需要做其他处理
				if (selectedCount === 1) {
					return;
				}
				// 如果有多个选中项，则根据规则修改其他选中项的isSelect
				if (startIndex <= endIndex) {
					for (let i = startIndex + 1; i < endIndex; i++) {
						this.hostArr[i].isSelect = true;
					}
				}
				//如果已经选择了多个 再次选中的是之前选中的中间值，则取消所有选中，把当前选中设置为true
				if (index < endIndex && index > startIndex) {
					this.hostArr.map(item => {
						item.isSelect = false
					})
					this.hostArr[index].isSelect = true

				}


			},
		}
	}
</script>

<style lang="scss">
	page {
		background-color: #F4F4F4;
	}

	.btn {
		position: fixed;
		bottom: 50rpx;
		left: 0;
		right: 0;
		z-index: 99;
		background: #442916;
		border-radius: 16rpx;
		height: 90rpx;
		line-height: 90rpx;
		color: #FFFFFF;
		text-align: center;
		margin: 0 30rpx;
	}

	.act {
		color: #01993A !important;
	}

	.calendar {
		width: 100%;
		height: min-content;
		background-color: #FFFFFF;
		border-radius: 8rpx;
		padding-bottom: 20rpx;
		margin: 0 auto;
	}

	.calendar_day {
		display: flex;
		width: 100%;
		height: 120rpx;

		.scroll-view_H {
			white-space: nowrap;
			width: 100%;

			.day_x {
				display: inline-block;
				width: 130rpx;
				text-align: center;
				font-size: 24rpx;
				color: #333333;
				border-radius: 15rpx;
				// padding-top: 10rpx;
				// padding-bottom: 20rpx;
				padding: 20rpx 0;

				.day_x_b {
					margin: 5rpx 0;
				}

				.day_x_c {
					color: #999999;
					height: 40rpx;
					line-height: 40rpx;
				}
			}

			.act {
				background-color: #E6FFEF;

				.day_x_c {
					color: #01993A;
					// background-color: #F3D6AE;
					border-radius: 10rpx 10rpx 15rpx 15rpx;
					height: 40rpx;
					line-height: 40rpx;
				}

			}
		}
	}

	.calendar_time {
		width: 100%;
		margin: 20rpx 0;

		.time_x {
			align-content: center;
			width: calc((100% - 42rpx) / 4);
			height: 90rpx;
			font-size: 24rpx;
			color: #333333;
			background: #F2F2F2;
			border-radius: 16rpx;
			margin-bottom: 14rpx;
		}

		.time_x_sty {
			background-color: #F3D6AE;
			color: #333333 !important;
		}
	}

	.sub {
		display: flex;
		justify-content: center;
		align-items: center;
		width: 710rpx;
		height: 100rpx;
		border-radius: 50rpx;
		margin: 30rpx auto;
		color: #FFFFFF;
		font-size: 36rpx;
		background-color: #2FB57A;
	}
</style>