<!-- 取餐页面 -->
<template>
	<view class="container">
		<view v-if="!orderCurrent.order_id" class="d-flex w-100 h-100 flex-column just-content-center align-items-center">
			<image src="/static/images/loading.gif" class="drinks-img"></image>
			<view class="tips d-flex flex-column align-items-center font-size-base text-color-assist">
				<view>您还没有点单</view>
				<view>快去犒劳一下自己吧~</view>
			</view>
			<button type="primary" class="drink-btn" size="default" @tap="menu">去点餐</button>
			<view class="font-size-sm text-color-primary">查看历史订单</view>
		</view>
		<template v-else>
			<view class="order-box">
				<view class="bg-white">
					<view class="section">
						<!-- store info begin -->
						<list-cell :hover="false">
							<view class="w-100 d-flex align-items-center">
								<view class="d-flex flex-column w-60">
									<view class="w-100 font-size-lg text-color-base text-truncate">{{ orderCurrent.chooseStore }}</view>
								</view>
								<view class="d-flex justify-content-end align-items-center w-40">
									<image src="/static/images/order/mobile.png" style="width: 60rpx; height: 60rpx; margin-right: 30rpx"></image>
									<image src="/static/images/order/navigation.png" style="width: 60rpx; height: 60rpx"></image>
								</view>
							</view>
						</list-cell>
						<!-- store info end -->
						<list-cell :hover="false" padding="50rpx 30rpx">
							<view class="w-100 d-flex flex-column">
								<view class="d-flex align-items-center just-content-center" v-if="orderCurrent.order_type == 0">
									<view class="sort-num">{{ orderCurrent.sort_num }}</view>
								</view>
								<!-- steps begin -->
								<view class="d-flex just-content-center">
									<view
										class="steps d-flex flex-column"
										:class="{
											'w-80': orderCurrent.order_type == 0,
											'w-100': orderCurrent.order_type == 1
										}"
									>
										<view class="steps__img-column">
											<view class="steps__img-column-item">
												<image src="/static/images/order/ordered_selected.png" v-if="orderCurrent.status >= 2"></image>
												<image src="/static/images/order/ordered_selected.png" v-else></image>
											</view>
											<view class="steps__img-column-item" :class="{ active: orderCurrent.status >= 3 }">
												<image src="/static/images/order/production_selected.png" v-if="orderCurrent.status >= 3"></image>
												<image src="/static/images/order/production.png" v-else></image>
											</view>
											<view class="steps__img-column-item" :class="{ active: orderCurrent.status >= 4 }" v-if="orderCurrent.order_type == 1">
												<image src="/static/images/order/delivery_selected.png" v-if="orderCurrent.status >= 4"></image>
												<image src="/static/images/order/delivered.png" v-else></image>
											</view>
											<view class="steps__img-column-item" :class="{ active: orderCurrent.status >= 5 }">
												<image src="/static/images/order/delivered_selected.png" v-if="orderCurrent.status >= 5"></image>
												<image src="/static/images/order/delivered.png" v-else></image>
											</view>
										</view>
										<view class="steps__text-column">
											<view class="steps__text-column-item" :class="{ active: orderCurrent.status >= 2 }">
												<view class="steps__column-item-line bg-transparent"></view>
												<view class="steps__text-column-item-text">已下单</view>
												<view class="steps__column-item-line"></view>
											</view>
											<view class="steps__text-column-item" :class="{ active: orderCurrent.status >= 3 }">
												<view class="steps__column-item-line"></view>
												<view class="steps__text-column-item-text">制作中</view>
												<view class="steps__column-item-line"></view>
											</view>
											<view class="steps__text-column-item" :class="{ active: orderCurrent.status >= 4 }" v-if="orderCurrent.order_type == 1">
												<view class="steps__column-item-line"></view>
												<view class="steps__text-column-item-text">配送中</view>
												<view class="steps__column-item-line"></view>
											</view>
											<view class="steps__text-column-item" :class="{ active: orderCurrent.status >= 5 }">
												<view class="steps__column-item-line"></view>
												<view class="steps__text-column-item-text">{{ orderCurrent.order_type == 1 ? '已送达' : '请取餐' }}</view>
												<view class="steps__column-item-line bg-transparent"></view>
											</view>
										</view>
									</view>
								</view>
								<!-- steps end -->
								<view v-if="orderCurrent.status <= 3" class="d-flex just-content-center align-items-center font-size-base text-color-assist mb-40">
									您前面还有
									<text class="text-color-primary mr-10 ml-10">{{ peopleNum }}</text>
									单待制作
								</view>
								<!-- goods begin -->
								<view class="w-100 d-flex flex-column position-relative mt-30" style="margin-bottom: -40rpx">
									<view class="w-100 d-flex align-items-center mb-40" v-for="(good, index) in orderCurrent.goodsInOrder" :key="index">
										<view class="d-flex flex-column w-60 overflow-hidden">
											<view class="font-size-lg text-color-base mb-10 text-truncate">{{ good.name }}</view>
											<!-- <view class="font-size-sm text-color-assist text-truncate">{{ good.property }}</view> -->
										</view>
										<view class="d-flex w-40 align-items-center justify-content-between pl-30">
											<view class="font-size-base text-color-base">x{{ good.number }}</view>
											<view class="font-size-base text-color-base font-weight-bold">￥{{ good.price }}</view>
										</view>
									</view>
								</view>
								<!-- goods end -->
							</view>
						</list-cell>
					</view>
					<view class="section">
						<!-- payment and amount begin -->
						<list-cell :hover="false" padding="50rpx 30rpx">
							<view class="w-100 d-flex flex-column">
								<view class="pay-cell">
									<view>支付方式</view>
									<view class="font-weight-bold">{{ payMethods }}</view>
								</view>
								<view class="pay-cell">
									<view>金额总计</view>
									<view class="font-weight-bold">￥{{ orderCurrent.totalFee }}</view>
								</view>
							</view>
						</list-cell>
						<!-- payment and amount end -->
					</view>
					<view class="section">
						<!-- order info begin -->
						<list-cell :hover="false" padding="50rpx 30rpx">
							<view class="w-100 d-flex flex-column">
								<view class="pay-cell">
									<view>下单时间</view>
									<view class="font-weight-bold">{{ orderCurrent.createOrderTime }}</view>
								</view>
								<view class="pay-cell">
									<view>下单门店</view>
									<view class="font-weight-bold">{{ orderCurrent.chooseStore }}</view>
								</view>
								<view class="pay-cell">
									<view>支付方式</view>
									<view class="font-weight-bold">{{ payMethods }}</view>
								</view>
								<view class="pay-cell">
									<view>订单号</view>
									<view class="font-weight-bold">{{ orderCurrent.order_id }}</view>
								</view>
							</view>
						</list-cell>
						<!-- order info end -->
					</view>
					<!-- order other info begin -->
					<list-cell :hover="false" padding="50rpx 30rpx 20rpx" last>
						<view class="w-100 d-flex flex-column">
							<view class="pay-cell">
								<view>取单号</view>
								<view class="font-weight-bold">{{ orderCurrent.sort_num }}</view>
							</view>
							<view class="pay-cell">
								<view>享用方式</view>
								<view class="font-weight-bold">自取</view>
							</view>
							<view class="pay-cell">
								<view>取餐时间</view>
								<view class="font-weight-bold">立即取餐</view>
							</view>
							<view class="pay-cell">
								<view>完成制作时间</view>
								<view class="font-weight-bold">{{ orderCurrent.takeTime }}</view>
							</view>
							<view class="pay-cell">
								<view>备注</view>
								<view class="font-weight-bold">{{ orderCurrent.remark }}</view>
							</view>
						</view>
					</list-cell>
					<!-- order other info end -->
				</view>
			</view>
		</template>
	</view>
</template>

<script>
import {mapState, mapMutations} from 'vuex'
import listCell from '../../components/list-cell/list-cell.vue'
export default {
  data() {
    return {
      peopleNum: ''
    }
  },
  components: {
    listCell
  },
  computed: {
    ...mapState(['orderCurrent'])
  },
  onLoad(option) {
    this.init(option.order_id)
  },
  methods: {
    ...mapMutations(['SET_ORDERCURRENT', 'CHEAR_CURRENT', 'SET_ORDERCURRENTSTATUS']),
    menu() {
      uni.switchTab({
        url: '../menu/menu'
      })
    },
    // 数据初始化
    init(id) {
      if (Object.keys(this.orderCurrent).length == 0) {
        return uniCloud
        .callFunction({
          name: 'order',
          data: {
            action: 'orderCurrent',
            order_id: id
          }
        })
        .then(res => {
          if (res.result.status === 0) {
            let result = res.result
            this.SET_ORDERCURRENT(result.data)
            this.peopleNum = result.data.peopleNum
          } else if (res.result.status === -1) {
            uni.showModal({
              content: res.result.msg
            })
          } else if (res.result.status == 1) {
            let result = res.result
            this.SET_ORDERCURRENT(result.data)
            this.update(this.orderCurrent.order_id)
          }
        })
      }
    },
    update(id) {
      return uniCloud
      .callFunction({
        name: 'order',
        data: {
          action: 'orderUpdate',
          order_id: id
        }
      }).then(res => {
        if (res.result === 6) {
          this.CHEAR_CURRENT()
          uni.redirectTo({
            url: '../menu/menu'
          })
        } else {
          this.SET_ORDERCURRENTSTATUS(res.result)
        }
      })
    }
  }
}
</script>

<style lang="scss" scoped>
/* #ifdef H5 */
page {
	min-height: 100%;
	background-color: $bg-color;
}
/* #endif */
.order-box {
	padding: 20rpx;
	/* #ifdef H5 */
	margin-bottom: 100rpx;
	/* #endif */
}

.drinks-img {
	width: 260rpx;
	height: 260rpx;
}

.tips {
	margin: 60rpx 0 80rpx;
	line-height: 48rpx;
}

.drink-btn {
	width: 320rpx;
	border-radius: 50rem !important;
	margin-bottom: 40rpx;
	font-size: $font-size-base;
	line-height: 3;
}

@mixin arch {
	content: '';
	position: absolute;
	background-color: $bg-color;
	width: 30rpx;
	height: 30rpx;
	bottom: -15rpx;
	z-index: 10;
	border-radius: 100%;
}

.section {
	position: relative;
	&::before {
		@include arch;
		left: -15rpx;
	}
	&::after {
		@include arch;
		right: -15rpx;
	}
}
.pay-cell {
	width: 100%;
	display: flex;
	align-items: center;
	justify-content: space-between;
	font-size: $font-size-base;
	color: $text-color-base;
	margin-bottom: 40rpx;
	&:nth-last-child(1) {
		margin-bottom: 0;
	}
}
.sort-num {
	font-size: 64rpx;
	font-weight: bold;
	color: $text-color-base;
	line-height: 2;
}
.steps__img-column {
	display: flex;
	margin: 30rpx 0;
	.steps__img-column-item {
		flex: 1;
		display: flex;
		align-items: center;
		justify-content: center;
		image {
			width: 80rpx;
			height: 80rpx;
		}
	}
}

.steps__text-column {
	display: flex;
	margin-bottom: 40rpx;
	.steps__text-column-item {
		flex: 1;
		display: inline-flex;
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: $font-size-base;
		color: $text-color-assist;
		&.active {
			color: $text-color-base;
			font-weight: bold;
			.steps__column-item-line {
				background-color: $text-color-base;
			}
		}
		.steps__column-item-line {
			flex: 1;
			height: 2rpx;
			background-color: #919293;
			transform: scaleY(0.5);
		}
		.steps__text-column-item-text {
			margin: 0 8px;
		}
	}
}
</style>
