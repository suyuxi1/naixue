<template>
	<view class="container">
		<view class="form-box">
			<view class="form">
				<list-cell :hover="false">
					<view class="form-input">
						<view class="label">收货人</view>
						<input class="input" placeholder="请输入收货人" v-model="form.accept_name" placeholder-class="text-color-assist" />
					</view>
				</list-cell>
				<list-cell :hover="false">
					<view class="form-input">
						<view class="label">性别</view>
						<view class="radio-group">
							<view class="radio" :class="{ checked: !form.gender }" style="margin-right: 10rpx;" @tap="form.gender = 0">先生</view>
							<view class="radio" :class="{ checked: form.gender }" @tap="form.gender = 1">女士</view>
						</view>
					</view>
				</list-cell>
				<list-cell :hover="false">
					<view class="form-input">
						<view class="label">联系方式</view>
						<input class="input" placeholder="请输入收货人联系方式" v-model="form.mobile" placeholder-class="text-color-assist" />
					</view>
				</list-cell>
				<list-cell :hover="false">
					<view class="form-input">
						<view class="label">所在区域</view>
						<input class="input" placeholder="请选择收货地址" v-model="form.area" placeholder-class="text-color-assist" />
					</view>
				</list-cell>
				<list-cell :hover="false">
					<view class="form-input">
						<view class="label">收货地址</view>
						<input class="input" placeholder="请输入收货人详细地址" v-model="form.street" placeholder-class="text-color-assist" />
					</view>
				</list-cell>
			</view>
			<view class="btn-section"><button type="primary" size="default" @click="save">保存</button></view>
		</view>
	</view>
</template>

<script>
import listCell from '@/components/list-cell/list-cell.vue';
export default {
	comments: {
		listCell
	},
	data() {
		return {
			form: {
				accept_name: '',
				gender: 0,
				mobile: '',
				area: '',
				street: '',
				openId: ''
			}
		};
	},
	methods: {
		save() {
			let data = this.form;
			return uniCloud
				.callFunction({
					name: 'validateToken',
					data: {
						token: uni.getStorageSync('token')
					}
				})
				.then(res => {
					if (res.result.status === 0) {
						this.form.openId = res.result.openId;
						return uniCloud
							.callFunction({
								name: 'address',
								data: {
									data: data,
									action: 'addAddress'
								}
							})
							.then(res => {
								if (res.result.status === 0) {
									uni.showToast({
										title: '添加成功'
									});
									uni.navigateBack({});
								} else {
									uni.showModal({
										content: res.result.msg,
										showCancel: false
									});
								}
							});
					}
				});
		}
	}
};
</script>

<style lang="scss" scoped>
.form-box {
	width: 100%;
	height: 100%;
	padding: 30rpx;
	display: flex;
	flex-direction: column;
	color: $text-color-base;
	.form-input {
		display: flex;
		align-items: center;
		width: 100%;
	}
	.label {
		width: 200rpx;
		font-size: $font-size-lg;
		color: $text-color-base;
		font-weight: 500;
	}
	.input {
		flex: 1;
		display: flex;
		align-items: center;
	}
	.radio-group {
		display: flex;
		justify-content: flex-start;
		.radio {
			padding: 10rpx 30rpx;
			border-radius: 6rpx;
			border: 2rpx solid $text-color-assist;
			color: $text-color-assist;
			font-size: $font-size-base;
			&.checked {
				background-color: $color-primary;
				color: $text-color-white;
				border: 2rpx solid $color-primary;
			}
		}
	}
	.btn-section {
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		button {
			font-size: $font-size-base;
			height: 90rpx;
			border-radius: 50rpx !important;
			width: 85%;
			display: flex;
			align-items: center;
			justify-content: center;
		}
	}
}
</style>
