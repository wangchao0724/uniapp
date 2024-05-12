<template>
	<view class="tabs">
		<view class="tabs-header">
			<template
				v-for="(item, index) in list"
			>
				<view 
					:class="['tabs-header--item', current === index && 'active-title']" 
					:key="item.name"
					@click="clickTab(item, index)"
				>
					<text class="tab-title">{{item.title}}</text>
					<text class="tab-subtitle">{{item.subTitle}}</text>
				</view>
			</template>
		</view>
		<my-swiper :current="current" @change="changeSwiper">
			<my-swiper-item
				v-for="(item, index) in list"
				:key="item.name"
			>
				<view class="swiper-item-content">
					<slot :name="item.name"></slot>
				</view>
			</my-swiper-item>
		</my-swiper>
	</view>
</template>

<script>
	export default {
		name:"tabs",
		props: {
			list: {
				type: Array,
				default: []
			},
			current: {
				type: Number,
			}
		},
		methods: {
			clickTab(tab, index) {
				this.$emit('click', tab, index);
				if (this.current !== index) {
					this.$emit('change', tab, index)
				}
			},
			changeSwiper(index) {
				const tab = this.list[index];
				this.$emit('change', tab, index)
			}
		}
	}
</script>

<style lang="scss" scoped>
.tabs {
	--borderRadius: 10rpx;
	
	display: flex;
	flex-direction: column;
	padding: 16rpx;
	margin: 20rpx;
	
	
	&-header {
		--borderColor: #fcfcfc;
		
		position: relative;
		display: flex;
		padding: 20rpx 20rpx 30rpx;
		background-color: #fff;
		border-top-left-radius: var(--borderRadius);
		border-top-right-radius: var(--borderRadius);
		
		&::after {
			content: '';
			position: absolute;
			bottom: 0;
			width: calc(100% - 40rpx);
			height: 10rpx;
			background-color: var(--borderColor);
			border-radius: 40rpx;
		}
		
		&--item {
			flex: 1;
			display: flex;
			flex-direction: column;
			justify-content: center;
			text-align: center;
			font-size: 24rpx;
			border-right: 5rpx solid var(--borderColor);
			
			&:last-child {
				border-right: none;
			}
			
			.tab-title {
				font-weight: bold;
				white-space: nowrap;
			}
			
			.tab-subtitle {
				font-size: 20rpx;
			}
		}
		
		.active-title {
			--activeColor: #109855;
			
			position: relative;
			color: var(--activeColor);
			
			&::after {
				content: '';
				position: absolute;
				bottom: -20rpx;
				left: 0;
				width: 50%;
				height: 10rpx;
				transform: translateX(50%);
				background-color: var(--activeColor);
				border-radius: 40rpx;
			}
		}
	}
	
	.swiper-item-content {
		background-color: #fff;
		border-bottom-left-radius: var(--borderRadius);
		border-bottom-right-radius: var(--borderRadius);
	}
}
</style>