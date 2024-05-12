<template>
	<view class="page-wrapper">
		<uni-nav-bar class="page-header" left-icon="left" :color="color" :backgroundColor="backgroundColor" :fixed="fixed" :border="border"
			@clickLeft="clickLeft" @clickRight="clickRight">
			<view class="title">
				<slot name="title">{{title}}</slot>
			</view>

			<template v-slot:right>
				<image src="/static/icon/icon_Tutorial.png" class="right"></image>
			</template>
		</uni-nav-bar>
		<view class="page-content">
			<slot></slot>
		</view>
	</view>
</template>

<script>
	export default {
		name: "page",
		props: {
			title: {
				type: String
			},
			color: {
				type: String,
				default: '#000000',
			},
			backgroundColor: {
				type: String,
				default: 'transparent'
			},
			fixed: {
				type: Boolean,
				default: true,
			},
			border: {
				type: Boolean,
				default: false
			}
		},
		methods: {
			clickLeft() {
				uni.navigateBack()
			},
			clickRight() {
				this.$emit('clickRight')
			}
		}
	}
</script>

<style lang="scss" scoped>
	.page-wrapper {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
	}
	
	.page-header {
		.title {
			flex: 1;
			display: flex;
			align-items: center;
			justify-content: center;
		}
		
		.right {
			width: 36rpx;
			height: 36rpx;
		}
	}

	
	.page-content {
		flex: 1;
	}
</style>