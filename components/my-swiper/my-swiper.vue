<template>
	<view class="swiper" :style="{'--current': current, '--deltaX': deltaX + 'px'}" @touchstart="onTouchStart" @mousedown="onMouseDown">
		<view :class="['swiper-container', {'swiper-transition': !isTouching}]">
			<slot></slot>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			current: {
				type: Number,
				default: 0
			}
		},
		data() {
			return {
				index: this.current,
				isTouching: false,
				startX: 0,
				deltaX: 0,
				direction: 'none',
				deltaXOffset: 50
			}
		},
		methods: {
			onTouchStart(event) {
				this.isTouching = true;
				this.startX = event.touches[0].clientX;
				document.addEventListener('touchmove', this.onTouchMove);
				document.addEventListener('touchend', this.onTouchEnd)
			},
			onTouchMove(event) {
				if (!this.isTouching) return;
				const currentX = event.touches[0].clientX;
				const deltaX = currentX - this.startX;
				this.deltaX = deltaX;
				// 左右拖动的逻辑
				if (deltaX > this.deltaXOffset) {
					this.direction = 'right'
				} else if (deltaX < -this.deltaXOffset) {
					this.direction = 'left'
				} else {
					this.direction = 'none'
				}
			},
			onTouchEnd() {
				this.isTouching = false;
				this.deltaX = 0;
				if (this.direction === 'left' && this.index < this.$slots.default.length - 1) {
					this.index++;
					this.$emit('change', this.index)
				} else if (this.direction === 'right' && this.index > 0) {
					this.index--;
					this.$emit('change', this.index)
				}
				document.removeEventListener('touchmove', this.onTouchMove);
				document.removeEventListener('touchend', this.onTouchEnd)
			},
			onMouseDown(event) {
				this.isTouching = true;
				this.startX = event.clientX;
				document.addEventListener('mousemove', this.onMouseMove);
				document.addEventListener('mouseup', this.onMouseUp);
			},
			onMouseMove(event) {
				if (!this.isTouching) return;
				const currentX = event.clientX;
				const deltaX = currentX - this.startX;
				this.deltaX = deltaX;
				// 左右拖动的逻辑
				if (deltaX > this.deltaXOffset) {
					this.direction = 'right'
				} else if (deltaX < -this.deltaXOffset) {
					this.direction = 'left'
				} else {
					this.direction = 'none'
				}
			},
			onMouseUp() {
				this.isTouching = false;
				this.deltaX = 0;
				if (this.direction === 'left' && this.index < this.$slots.default.length - 1) {
					this.index++;
					this.$emit('change', this.index)
				} else if (this.direction === 'right' && this.index > 0) {
					this.index--;
					this.$emit('change', this.index)
				}
				document.removeEventListener('mousemove', this.onMouseMove)
				document.removeEventListener('mouseup', this.onMouseUp);
			},
		}
	}
</script>

<style lang="scss" scoped>
	.swiper {
		width: 100%;
		overflow: hidden;

		&-container {
			display: flex;
			transform: translateX(calc(var(--current) * -100% + var(--deltaX)));
			
			/deep/ & > * {
				flex-shrink: 0;
			}
		}
		
		.swiper-transition {
			transition: all 0.8s;
		}
	}
</style>