<template>
	<view class="content">
		<text class="sentence">{{sentence}}</text>
		<view class="flex">
			<input class="input" v-model="videoUrl"/>
			<button @click="pastUrl">paste</button>
		</view>
		<button @click="parse">parse</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				sentence: '我很忙，时间比我更忙，不吃不睡，不舍昼夜',
				videoUrl: ''
			}
		},
		onLoad() {
			this.getRandomSentence()
		},
		methods: {
			getRandomSentence() {
				uni.request({
					url: 'https://api.oick.cn/yiyan/api.php',
					success: ({data}) => {
						console.log('sentence: ', data)
						this.sentence = data
					}
				})
			},
			pastUrl() {
				// 获取剪切板内容
				uni.getClipboardData({
					success: ({data}) => {
						console.log('clipboard', data)
						if (data.match('http://') || data.match('https://')) {
							this.videoUrl = data
							uni.hideToast()
						} else {
							uni.showToast({
								title: '请先复制视频链接',
								icon: 'none'
							})
						}
					},
					fail: () => {
						uni.showToast({
							title: '请先复制视频链接',
							icon: 'none'
						})
					}
				})
			},
			parse() {
				console.log('the videoUrl: ', this.videoUrl)
				uni.request({
					url: 'https://api.oick.cn/video/api.php',
					data: {
						url: this.videoUrl
					},
					success: ({title, nickname, music, play}) => {
						console.log('analyse success', title, nickname, music, play)
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		padding: 0 24rpx;
	}
	.sentence {
		padding: 24rpx;
	}
	.input {
		border: solid 1px #7c7c7c;
		border-radius: 10rpx;
		height: 46px;
		width: 500rpx;
	}
</style>
