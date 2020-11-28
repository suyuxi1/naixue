<template>
	<view class="content">
		<view class="input_box">
			<textarea placeholder="请输入您要留言的内容" class="textarea border" v-model="message.content"></textarea>
			<input type="text" class="input_box border" v-model="message.name" placeholder="请输入用户名:" />
			<button @click="addMsg" class="submit t-c">提交</button>
		</view>
		<view class="list">
			<view class="item" v-for="(item, index) in listData" :key="index">
				<view class="word">
					<text class="user_name">{{ item.name }}</text>
					<text class="message">{{ item.content }}</text>
				</view>
				<text class="delete t-c" @click="removeMsg(item._id)">删除</text>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			message: {
				name: '',
				content: ''
			},
			listData: [
				{
					_id: '',
					name: '',
					content: ''
				}
			]
		};
	},
	onLoad() {
		this.getData();
	},
	methods: {
		addMsg() {
			let data = this.message;
			uniCloud
				.callFunction({
					name: 'add',
					data: data
				})
				.then(res => {
					this.getData();
					this.message.name = '';
					this.message.content = '';
				});
		},
		getData() {
			uniCloud
				.callFunction({
					name: 'get'
				})
				.then(res => {
					this.listData = res.result.data;
				});
		},
		removeMsg(id) {
			console.log(id);
			let _id = id;
			uniCloud
				.callFunction({
					name: 'remove',
					data: {
						_id
					}
				})
				.then(res => {
					this.getData();
				});
		}
	}
};
</script>

<style>
.content {
	display: flex;
	flex-direction: column;
	width: 750rpx;
	height: 100vh;
	color: #808080;
	font-size: 12px;
}
input {
	height: 80rpx;
}
.textarea {
	width: 710rpx;
	height: 300rpx;
}
.border {
	margin: 20rpx;
	border-radius: 10rpx;
	border: 0.5px solid #afb0b2;
}
.submit {
	margin: 0 20rpx 0 20rpx;
	background-color: #6fa2ff;
}
.t-c{
	color: #FFFFFF;
}
.list {
	display: flex;
	flex-direction: column;
	margin: 30rpx;
}
.item {
	width: 100%;
	display: flex;
	flex-direction: column;
	justify-content: center;
	margin: 20rpx 0 20rpx 0;
}
.word {
	display: flex;
	align-items: center;
	width: 740rpx;
}
.user_name {
	color: #6fa2ff;
	font-size: 30rpx;
}
.message{
	margin-left: 35rpx;
}
.delete {
	display: flex;
	align-items: center;
	justify-content: center;
	width: 80rpx;
	height: 60rpx;
	border-radius: 10rpx;
	background-color: #ff8082;
	color: #ffffff;
	margin-top: 10rpx;
}

</style>
