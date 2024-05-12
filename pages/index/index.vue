<template>
	<page @clickRight="clickRight">
		<template v-slot:title>
			<image src="/static/icon/icon_Assets_Details.png" class="title-icon"></image>
			<text class="title-text">Assets Details</text>
		</template>
		<tabs :list="list" :current="current" @change="tabChange">
			<template v-slot:limit>
				<view class="empty">
					<image src="/static/icon/img_no data.png" class="empty-img"></image>
					<text class="empty-text">No Data</text>
				</view>
			</template>
			<template v-slot:rewards>
				<view class="list">
					<view 
						class="list-item"
						v-for="item in rewardData"
					>
						<view class="list-item--row">
							<view class="list-item--row-left">
								<text class="list-item--row-left--title">{{item.msg}}</text>
								<text class="list-item--row-left--time">{{item.ctime}}</text>
							</view>
							<view class="list-item--row-right">
								<text :style="{color: item.amount >= 0 ? 'green' : 'red'}">{{item.amount > 0 ? '+' : ''}}{{item.amount}} Rs</text>
							</view>
						</view>
					</view>
				</view>
			</template>
			<template v-slot:conversion>
				<view>conversion</view>
			</template>
		</tabs>
	</page>
</template>

<script>
	export default {
		data() {
			return {
				list: [{
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
				rewardData: [
					{
						amount: 50,
						ctime: '2024.5.2',
						event_id: '1',
						msg: 'Registration bonus'
					},
					{
						amount: 10,
						ctime: '2024.5.2',
						event_id: '2',
						msg: 'Novice Task finished'
					},
					{
						amount: -60,
						ctime: '2024.5.2',
						event_id: '3',
						msg: 'Convert into withdrawable amount'
					},
					{
						amount: 10,
						ctime: '2024.5.1',
						event_id: '4',
						msg: 'Invitee recharge rabate'
					},
				]
			}
		},
		methods: {
			clickRight() {
				console.log('右侧点击');
			},
			tabChange(tab, index) {
				this.current = index
			}
		},
		mounted() {

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

		.title-icon {
			width: 36rpx;
			height: 36rpx;
		}

		.title-text {
			margin-left: 16rpx;
			font-weight: bold;
		}
		
		.empty {
			padding: 100rpx 0;
			display: flex;
			flex-direction: column;
			align-items: center;
				
			&-img {
				width: 140rpx;
				height: 160rpx;
			}
			
			&-text {
				color: #ccc;
				margin-top: 20rpx;
			}
		}
		
		.list {
			padding: 20rpx;
			&-item {
				&--title {
					display: block;
					font-weight: bold;
					margin: 30rpx 0 20rpx;
				}
				
				&--row {
					display: flex;
					align-items: center;
					justify-content: space-between;
					border-bottom: 1rpx solid #f7f7f7;
					padding: 20rpx 0;
					font-size: 28rpx;
					
					&-left {
						display: flex;
						flex-direction: column;
						
						&--title {
							
						}
						
						&--time {
							font-size: 24rpx;
							color: #ccc;
							word-spacing: 0rpx;
						}
					}
					
					&-right {
						font-weight: bold;
					}
				}
			}
		}
	}
</style>