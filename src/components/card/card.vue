<template>
	<div class="card" :class="{shadow: isShadow}">
		<div class="card-img" :class="[isHorizontal ? 'horizontal' : 'vertical']">
			<slot></slot>
		</div>
		<div class="card-content">
			<slot name="customize"></slot>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'card',
		props: {
			isShadow: Boolean,
			isHorizontal: Boolean
		}
	}
</script>

<style lang="scss" scoped>
	.card {
		width: 90vw;
		max-width: 375px;
		margin-left: 5vw;
		padding: 8px;
		display: flex;
		flex-wrap: wrap;
		.card-img {
			img {
				width: 100%;
			}
		}
		.horizontal {
			flex: 4;
		}
		.vertical {
			flex-basis: 90vw;
		}
		.card-content {
			flex: 6;
			text-align: left;
			.card-text {
				font-size: 16px;
				padding: 2px 0;
				display: -webkit-box !important;
				overflow: hidden;
				text-overflow: ellipsis;
				word-break: break-all;
				-webkit-box-orient: vertical;
				-webkit-line-clamp: 2;
			}
		}
	}
	.shadow {
		box-shadow: 0 0 8px #eee;
	}
</style>