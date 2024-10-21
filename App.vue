<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
			//#ifdef APP-PLUS
			// APP检测更新 具体打包流程可以参考：https://ask.dcloud.net.cn/article/35667
			plus.screen.lockOrientation('portrait-primary'); //竖屏正方向锁定
			//获取是否热更新过
			const updated = uni.getStorageSync('updated'); // 尝试读取storage
			let that = this;
			if (updated.completed === true) {
				// 如果上次刚更新过
				// 删除安装包及安装记录
				console.log('安装记录被删除，更新成功');
				uni.removeSavedFile({
					filePath: updated.packgePath,
					success: res => {
						uni.removeStorageSync('updated');
					}
				});
			} else if (updated.completed === false) {
				uni.removeStorageSync('updated');
				plus.runtime.install(updated.packgePath, {
					force: true
				});
				uni.setStorage({
					key: 'updated',
					data: {
						completed: true,
						packgePath: updated.packgePath
					},
					success: res => {
						console.log('成功安装上次的更新，应用需要重启才能继续完成');
					}
				});
				uni.showModal({
					title: '温馨提示',
					content: '应用将重启以完成更新',
					showCancel: false,
					complete: () => {
						plus.runtime.restart();
					}
				});
			} else {
				//获取当前系统版本信息
				plus.runtime.getProperty(plus.runtime.appid, widgetInfo => {
					//请求后台接口 解析数据 对比版本
					that.$Request.getT('/app/user/selectNewApp').then(res => {
						res = res.data[0];
						if (res.wgtUrl && widgetInfo.version < res.version) {
							let downloadLink = '';
							let androidLink = res.androidWgtUrl;
							let iosLink = res.iosWgtUrl;
							let ready = false;
							//校验是是不是热更新
							if (res.wgtUrl.match(RegExp(/.wgt/))) {
								// 判断系统类型
								if (plus.os.name.toLowerCase() === 'android') {
									console.log('安卓系统');
									if (androidLink && androidLink !== '#') {
										// 我这里默认#也是没有地址，请根据业务自行修改
										console.log('发现下载地址');
										// 安卓：创建下载任务
										if (androidLink.match(RegExp(/.wgt/))) {
											console.log('确认wgt热更新包');
											downloadLink = androidLink;
											ready = true;
										} else {
											console.log('安卓推荐.wgt强制更新，.apk的强制更新请您自行修改程序');
										}
									} else {
										console.log('下载地址是空的，无法继续');
									}
								} else {
									console.log('苹果系统');
									if (iosLink && iosLink !== '#') {
										// 我这里默认#也是没有地址，请根据业务自行修改
										console.log('发现下载地址');
										// 苹果(A)：进行热更新（如果iosLink是wgt更新包的下载地址）判断文件名中是否含有.wgt
										if (iosLink.match(RegExp(/.wgt/))) {
											console.log('确认wgt热更新包');
											downloadLink = iosLink;
											ready = true;
										} else {
											console.log('苹果只支持.wgt强制更新');
										}
									} else {
										console.log('下载地址是空的，无法继续');
									}
								}
								if (ready) {
									console.log('任务开始');
									let downloadTask = uni.downloadFile({
										url: downloadLink,
										success: res => {
											if (res.statusCode === 200) {
												// 保存下载的安装包
												console.log('保存安装包');
												uni.saveFile({
													tempFilePath: res.tempFilePath,
													success: res => {
														const packgePath = res
															.savedFilePath;
														// 保存更新记录到stroage，下次启动app时安装更新
														uni.setStorage({
															key: 'updated',
															data: {
																completed: false,
																packgePath: packgePath
															},
															success: () => {
																console
																	.log(
																		'成功保存记录'
																	);
															}
														});
														// 任务完成，关闭下载任务
														console.log(
															'任务完成，关闭下载任务，下一次启动应用时将安装更新'
														);
														downloadTask.abort();
														downloadTask = null;
													}
												});
											}
										}
									});
								} else {
									console.log('下载地址未准备，无法开启下载任务');
								}
							} else {
								//不是热更新是在线更新 校验是否强制升级
								if (res.method == 'true') {
									uni.showModal({
										showCancel: false,
										confirmText: '立即更新',
										title: '发现新版本',
										content: res.des,
										success: res => {
											if (res.confirm) {
												that.$queue.showLoading('下载中...');
												if (uni.getSystemInfoSync().platform ==
													'android') {
													uni.downloadFile({
														url: androidLink,
														success: downloadResult => {
															if (downloadResult
																.statusCode ===
																200) {
																plus.runtime
																	.install(
																		downloadResult
																		.tempFilePath, {
																			force: false
																		},
																		d => {
																			console
																				.log(
																					'install success...'
																				);
																			plus.runtime
																				.restart();
																		},
																		e => {
																			console
																				.error(
																					'install fail...'
																				);
																		}
																	);
															}
														}
													});
												}
												if (uni.getSystemInfoSync().platform ==
													'ios') {
													plus.runtime.openURL(iosLink, function(
														res) {});
												}
											} else if (res.cancel) {
												console.log('取消');
											}
										}
									});
								} else {
									uni.showModal({
										title: '发现新版本',
										confirmText: '立即更新',
										cancelText: '下次更新',
										content: res.des,
										success: res => {
											if (res.confirm) {
												that.$queue.showLoading('下载中...');
												if (uni.getSystemInfoSync().platform ==
													'android') {
													uni.downloadFile({
														url: androidLink,
														success: downloadResult => {
															if (downloadResult
																.statusCode ===
																200) {
																plus.runtime
																	.install(
																		downloadResult
																		.tempFilePath, {
																			force: false
																		},
																		d => {
																			console
																				.log(
																					'install success...'
																				);
																			plus.runtime
																				.restart();
																		},
																		e => {
																			console
																				.error(
																					'install fail...'
																				);
																		}
																	);
															}
														}
													});
												}
												if (uni.getSystemInfoSync().platform ==
													'ios') {
													plus.runtime.openURL(iosLink, function(
														res) {});
												}
											} else if (res.cancel) {
												console.log('取消');
											}
										}
									});
								}
							}
						}
					});
				});
			}

			//#endif
		},
		onShow: function() {
			console.log('App Show')
			let that = this;

			// 获取最少预约时间配置
			// this.$Request.get('api/index/common/sys_common', {
			// 	type_id: 355
			// }).then(res => {
			// 	if (res.code == 1 && res.data && res.data.value) {
			// 		uni.setStorageSync('make_min_time', res.data.value)
			// 	}
			// })
			// 小程序是否上线审核
			// that.$Request.getT('/app/common/type/302').then(res => {
			// 	if (res.code == 0) {
			// 		if (res.data && res.data.value) {
			// 			// #ifdef MP-WEIXIN
			// 			that.$queue.setData('shangxian', res.data.value)
			// 			// #endif
			// 			// #ifndef MP-WEIXIN
			// 			that.$queue.setData('shangxian', '是')
			// 			// #endif
			// 		}
			// 	}
			// });
			//小程序是否上线审核312
			that.$Request.getT('/app/common/type/312').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						// #ifdef MP-WEIXIN
						that.$queue.setData('shangxian', res.data.value)
						// #endif
						// #ifndef MP-WEIXIN
						that.$queue.setData('shangxian', '是')
						// #endif
					}
				}
			});
			//是否开启我的余额
			that.$Request.getT('/app/common/type/311').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						// #ifdef MP-WEIXIN
						that.$queue.setData('yueSelect', res.data.value)
						// #endif
						// #ifndef MP-WEIXIN
						that.$queue.setData('yueSelect', '是')
						// #endif
					}
				}
			});
			//是否开启我的余额
			that.$Request.getT('/app/common/type/301').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						// #ifdef MP-WEIXIN
						that.$queue.setData('yueSelect', res.data.value)
						// #endif
						// #ifndef MP-WEIXIN
						that.$queue.setData('yueSelect', '是')
						// #endif
					}
				}
			});
			//客服方式  1手机号 2企业客服 3微信客服  	618
			that.$Request.getT('/app/common/type/618').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.$queue.setData('SelKeFu', res.data.value)
					}
				}
			});
			//客服AppId 617
			that.$Request.getT('/app/common/type/617').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.$queue.setData('SelKeFuAppId', res.data.value)
					}
				}
			});
			//客服链接 616
			that.$Request.getT('/app/common/type/616').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.$queue.setData('SelKeFuLink', res.data.value)
					}
				}
			});
			//客服手机号	619
			that.$Request.getT('/app/common/type/619').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.$queue.setData('kefuPhone', res.data.value)
					}
				}
			});

			//订单提前多长时间展示茶室信息（分钟）	816 
			that.$Request.getT('/app/common/type/816').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						that.$queue.setData('hour', res.data.value)
					}
				}
			});
			//#ifdef H5
			let isopen = false
			// this.$Request.get('/app/common/type/237').then(res => {
			// 	if (res.code == 0) {
			// 		if (res.data.value == '是') {
			// 			isopen = true
			// 		}
			// 	}
			// });
			// if (isopen) {
			// 	let ua = navigator.userAgent.toLowerCase();
			// 	if (ua.indexOf('micromessenger') !== -1) {
			// 		let openid = uni.getStorageSync('openid');
			// 		let userId = uni.getStorageSync('userId');
			// 		let that = this;
			// 		if (!openid) {
			// 			if (window.location.href.indexOf('?code=') !== -1 || window.location.href.indexOf('&code=') !==
			// 				-1) {
			// 				let code;
			// 				if (window.location.href.indexOf('?code=') !== -1) {
			// 					code = window.location.href.split('?code=')[1].split('&')[0];
			// 				} else {
			// 					code = window.location.href.split('&code=')[1].split('&')[0];
			// 				}
			// 				this.$Request.get('/app/Login/getOpenId?code=' + code).then(ret => {
			// 					uni.setStorageSync('openId', ret.data)

			// 					this.$Request.get('/app/Login/openid/login?openId=' + ret.data).then(res => {

			// 						this.$queue.setData("userId", res.user.userId);
			// 						this.$queue.setData("token", res.token);
			// 						this.$queue.setData("phone", res.user.phone);
			// 						this.$queue.setData("userName", res.user.userName);
			// 						this.$queue.setData("avatar", res.user.avatar);
			// 						this.$queue.setData("invitationCode", res.user.invitationCode);
			// 						this.$queue.setData("inviterCode", res.user.inviterCode);
			// 					});
			// 				});
			// 			} else {
			// 				this.$Request.get('/app/common/type/108').then(res => {
			// 					if (res.data.value == '是') {
			// 						window.location.href =
			// 							'https://open.weixin.qq.com/connect/oauth2/authorize?appid=' +
			// 							that.$queue.getWxAppid() +
			// 							'&redirect_uri=' +
			// 							window.location.href.split('#')[0] +
			// 							'&response_type=code&scope=snsapi_userinfo#wechat_redirect';
			// 					}
			// 				});
			// 			}
			// 		}
			// 		if (userId && openid) {
			// 			this.$Request.get('/app/Login/bindOpenId?userId=' + userId + '&openId=' + openid).then(res => {
			// 				// 省钱兄陪玩 https://qipaishi.xianmaxiong.com
			// 			});
			// 		}
			// 	}
			// }
			//#endif
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/* 注意要写在第一行，同时给style标签加入lang="scss"属性 */
	@import "uview-ui/index.scss";
	@import 'components/colorui/main.css';
	@import 'components/colorui/icon.css';

	page {
		background-color: #F7F5F6;
	}

	.bg {
		background: #FFFFFF;
	}

	.fles {
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.fles_cen {
		display: flex;
		align-items: center;
		justify-content: center;
	}
</style>