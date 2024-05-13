<template>
	<view class="page-wrapper">
		<uni-nav-bar class="page-header" left-icon="left" backgroundColor="transparent" fixed :border="false"
			@clickLeft="clickLeft" @clickRight="clickRight">
			<view class="title">
				<image src="/static/icon/icon_Assets_Details.png" class="title-icon"></image>
				<text class="title-text">Assets Details</text>
			</view>

			<template v-slot:right>
				<image src="/static/icon/icon_Tutorial.png" class="right"></image>
			</template>
		</uni-nav-bar>
		<view class="page-content">
			<view class="tabs-header" :style="{'--left': activeRect.left + 'px', '--width': activeRect.width + 'px'}">
				<view
					ref="tabRef"
					class="tab-item"
					v-for="(item, index) in tabs"
					:key="item.name"
					@click="current = index"
				>
					<text class="tab-title">{{item.title}}</text>
					<text class="tab-subtitle">{{item.subTitle}}</text>
				</view>
			</view>
			<scroll-view 
				class="tabs-content" 
				scroll-y 
				scroll-with-animation 
				refresher-enabled
				refresher-background="#efefef"
				:refresher-triggered="triggered"
				@refresherrefresh="refresherrefresh"
			>
				<view 
					class="list"
					v-for="(row, i) in rewardData"
					:key="i"
				>
					<view class="list-left">
						<text>{{row.msg}}</text>
						<text class="list-left--time">{{row.ctime}}</text>
					</view>
					<view class="list-right">
						<text :style="{color: row.amount >= 0 ? 'green' : 'red'}">
							{{row.amount > 0 ? '+' : ''}}{{row.amount}} Rs
						</text>
					</view>
				</view>
				<uni-load-more ref="loadMoreRef" :status="loadStatus"></uni-load-more>

			</scroll-view>
		</view>
	</view>
</template>

<script>
	// 生成随机日期的函数
	function getRandomDate() {
	  const year = Math.floor(Math.random() * (2025 - 1970) + 1970); // 1970 到 2024 年之间的随机年份
	  const month = Math.floor(Math.random() * 12) + 1; // 1 到 12 之间的随机月份
	  const daysInMonth = new Date(year, month, 0).getDate(); // 获取指定年份和月份的天数
	  const day = Math.floor(Math.random() * daysInMonth) + 1; // 1 到该月份天数之间的随机日期
	
	  return `${year}.${month}.${day}`;
	}
	
	export default {
		data() {
			return {
				tabs: [{
						title: 'Withdraw Limit\nRecord',
						name: 'limit'
					},
					{
						title: 'Rewards Record',
						subTitle: '(To be converted):',
						name: 'rewards',
					},
					{
						title: 'Conversion\nRecord',
						name: 'conversion'
					}
				],
				current: 1,
				activeRect: {
					left: 0,
					width: 0
				},
				triggered: false,
				loadStatus: 'loading',
				intersectionObserver: null,
				page: 1,
				size: 10,
				rewardData: []
			}
		},
		methods: {
			clickLeft() {
				uni.navigateBack()
			},
			clickRight() {
				console.log('点击标题右侧');
			},
			async getData() {
				return new Promise((resolve) => {
					this.loadStatus = 'loading';
					// 使用示例
					setTimeout(() => {
						const arr = [];
						for(let i = 0; i < this.page * this.size; i++) {
							arr.push({
								amount: Math.floor(Math.random() * 201 - 100),
								ctime: getRandomDate(),
								event_id: i,
								msg: 'test' + i
							})
						}
						this.rewardData = arr;
						if (this.page * this.size >= 30) {
							this.loadStatus = 'noMore'
						}
						resolve()
					}, 2000)
				})
			},
			refresherrefresh() {
				this.triggered = true;
				this.page = 1;
				this.getData().finally(() => {
					this.triggered = false;
				});
			}
		},
		watch: {
			current: {
				handler() {
					this.$nextTick(() => {
						const currentTab = this.$refs.tabRef[this.current];
						const rect = currentTab.$el.getBoundingClientRect()
						this.activeRect = {
							left: rect.left,
							width: rect.width
						};
						this.rewardData = [];
						this.page = 1;
						this.getData();
					})
				},
				immediate: true
			}
		},
		mounted() {
			this.intersectionObserver = new IntersectionObserver(([entrie]) => {
				if (this.loadStatus === 'noMore') return;
				if (entrie.isIntersecting) {
					this.page++;
					this.getData();
				}
			});
			this.intersectionObserver.observe(this.$refs.loadMoreRef.$el);
		},
		onUnload() {
			this.intersectionObserver.disconnect();
			this.intersectionObserver = null;
		}
	}
</script>

<style lang="scss" scoped>
	page {
		height: 100%;
		background-color: #efefef;
	}

	.page-wrapper {
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;

		.page-header {

			.title {
				flex: 1;
				display: flex;
				align-items: center;
				justify-content: center;

				&-icon {
					width: 36rpx;
					height: 36rpx;
				}

				&-text {
					margin-left: 16rpx;
					font-weight: bold;
				}
			}

			.right {
				width: 36rpx;
				height: 36rpx;
			}

		}
		
		.page-content {
			flex: 1;
			overflow: hidden;
			display: flex;
			flex-direction: column;
			
			.tabs-header {
				--ratio: 0.5;
				
				display: flex;
				align-items: center;
				font-size: 24rpx;
				position: relative;
				padding-bottom: 20rpx;
				
				&::before {
					content: '';
					position: absolute;
					left: calc(var(--left) + var(--width) * (1 - var(--ratio)) / 2);
					bottom: 0;
					width: calc(var(--width) * var(--ratio));
					height: 10rpx;
					background-color: green;
					border-radius: 10rpx;
					transition: all 0.5s;
				}
				
				&::after {
					content: '';
					position: absolute;
					left: 0;
					bottom: 0;
					width: 100%;
					height: 10rpx;
					background-color: red;
					border-radius: 10rpx;
					transform: translateY(100%);
				}
				
				.tab-item {
					flex: 1;
					text-align: center;
					padding: 20rpx 10rpx;
					position: relative;
					
					.tab-title,
					.tab-subtitle {
						display: block;
					}
					
					&:not(:last-child)::after {
						content: '';
						position: absolute;
						right: 0;
						top: 0;
						width: 10rpx;
						height: 100%;
						background-color: red;
						border-radius: 10rpx;
						transform: translateX(50%);
					}
					
				}
				
			}
			
			.tabs-content {
				flex: 1;
				overflow: hidden;
				padding: 20rpx;
				box-sizing: border-box;
				
				
				.list {
					display: flex;
					align-items: center;
					justify-content: space-between;
					border-bottom: 1rpx solid #ccc;
					padding: 20rpx;
					
					&-left {
						display: flex;
						flex-direction: column;
						
						&--time {
							font-size: 24rpx;
							color: #bbb;
						}
					}
				}
			}
		}
	}
</style>