<template>
	<view class="container">
		<view class="wrapper">
			<view class="input-content">
				<view class="cu-form-group"
					style="border: 2upx solid whitesmoke;margin-bottom: 20px;border-radius: 30px">
					<view class="title text-black">手机号</view>
					<input type="number" :value="phone" placeholder="请输入手机号" maxlength="11" data-key="phone"
						@input="inputChange" />
				</view>
				<view class="cu-form-group"
					style="border: 2upx solid whitesmoke;margin-bottom: 20px;border-radius: 30px">
					<text class="title text-black">验证码</text>
					<input type="number" :value="code" placeholder="请输入验证码" maxlength="6" data-key="code"
						@input="inputChange" @confirm="toLogin" />
					<button class="send-msg" @click="sendMsg" :disabled="sending">{{sendTime}}</button>
				</view>
				<view class="cu-form-group"
					style="border: 2upx solid whitesmoke;margin-bottom: 20px;border-radius: 30px">
					<text class="title text-black">设置密码</text>
					<input type="password" :value="password" placeholder="请设置新密码" placeholder-class="input-empty"
						maxlength="20" minlength="6" data-key="password" @input="inputChange" @confirm="toLogin" />
				</view>
			</view>
			<button class="confirm-btn" @click="toLogin">立即找回</button>
		</view>
		<!-- modal弹窗 -->
		<u-modal v-model="meShowModel" :content="meContent" :title="meTitle" :show-cancel-button='meShowCancel'
			@cancel="meHandleClose" @confirm="meHandleBtn" :confirm-text='meConfirmText'
			:cancel-text='meCancelText'></u-modal>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				//弹窗
				meShowModel: false, //是否显示弹框
				meShowCancel: true, //是否显示取消按钮
				meTitle: '提示', //弹框标题
				meContent: '', //弹框内容
				meConfirmText: '确认', //确认按钮的文字
				meCancelText: '取消', //关闭按钮的文字
				meIndex: '', //弹窗的key
				code: '',
				phone: '',
				password: '',
				sending: false,
				sendTime: '获取验证码',
				count: 60,
				logining: false
			}
		},

		methods: {

			sendMsg() {
				const {
					phone
				} = this;
				if (!phone) {
					this.$queue.showToast("请输入手机号");
				} else if (phone.length !== 11) {
					this.$queue.showToast("请输入正确的手机号");
				} else {
					this.$queue.showLoading("正在发送验证码...");
					this.$Request.getT("/app/Login/sendMsg/" + phone + "/forget").then(res => {
						if (res.code === 0) {
							this.sending = true;
							this.$queue.showToast('验证码发送成功请注意查收');
							this.countDown();
							uni.hideLoading();
						} else {
							uni.hideLoading();
							uni.showToast({
								title: res.msg ? res.msg : '请一分钟后再获取验证码',
								icon: 'none',
								duration: 2000
							})
						}
					});
				}
			},
			countDown() {
				const {
					count
				} = this;
				if (count === 1) {
					this.count = 60;
					this.sending = false;
					this.sendTime = '获取验证码'
				} else {
					this.count = count - 1;
					this.sending = true;
					this.sendTime = count - 1 + '秒后重新获取';
					setTimeout(this.countDown.bind(this), 1000);
				}
			},
			inputChange(e) {
				const key = e.currentTarget.dataset.key;
				this[key] = e.detail.value;
			},
			navBack() {
				uni.navigateBack();
			},


			navTo(url) {
				uni.navigateTo({
					url
				})
			},
			toLogin() {
				const {
					phone,
					password,
					code
				} = this;
				if (!phone) {
					this.$queue.showToast("请输入手机号");
				} else if (!password) {
					this.$queue.showToast("请设置密码");
				} else if (password.length < 6) {
					this.$queue.showToast("密码位数必须大于六位");
				} else {
					this.logining = true;
					this.$queue.showLoading("正在修改密码中...");
					this.$Request.post("/app/Login/forgetPwd", {
						pwd: password,
						phone: phone,
						msg: code
					}).then(res => {
						uni.hideLoading();
						if (res.code === 0) {
							uni.navigateTo({
								url: '/pages/public/loginphone'
							});
						} else {
							uni.showToast({
								title: res.msg,
								icon: 'none',
								duration: 2000
							})
							// uni.showModal({
							// 	title: '密码找回失败',
							// 	content: res.msg ,
							// 	success: function (res) {
							// 		if (res.confirm) {
							// 			console.log('用户点击确定');
							// 		} else if (res.cancel) {
							// 			console.log('用户点击取消');
							// 		}
							// 	}
							// });
							// this.meShowModel = true
							// this.meTitle = '密码找回失败'
							// this.meContent = res.msg
							// this.meIndex = 'm0'
							// this.meShowCancel = false
						}
					});
				}
			},
		},

	}
</script>

<style lang='scss'>
	page {
		background: #FFFFFF !important;
	}

	.send-msg {
		border-radius: 30px;
		color: white;
		height: 30px;
		font-size: 14px;
		line-height: 30px;
		background: #01993A;
	}

	.container {
		padding-top: 32upx;
		position: relative;
		width: 100%;
		height: 100%;
		overflow: hidden;
		background: #FFFFFF;
	}

	.wrapper {
		position: relative;
		z-index: 90;
		background: #FFFFFF;
		padding-bottom: 20px;
	}


	.input-content {
		padding: 32upx 80upx;
	}


	.confirm-btn {
		width: 600upx;
		height: 80upx;
		line-height: 80upx;
		border-radius: 60upx;
		margin-top: 32upx;
		background: #01993A;
		color: #fff;
		font-size: 32upx;

		&:after {
			border-radius: 60px;
		}
	}
</style>