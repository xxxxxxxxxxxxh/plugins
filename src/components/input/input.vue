<template>
	<div class="inputText" :class="{curve: isCurve}">
		<input 
			ref="input" 
			type="text"
			:placeholder="placeholder"
			:value="value"
      @input="getInput($event)"
		>
		<div v-if="closeBtn">
			<div 
				v-if="show"
				class="close" 
				@click="clear"
			>×</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'inputText',
		data () {
			return {
				show: false,
			}
		},
		model: {
			prop: 'value',
			event: 'input'
		},
		props: {
			isCurve: Boolean,
			placeholder: String,
			value: String,
			closeBtn: Boolean
		},
		computed: {
			
		},
		methods: {
			change: function(e) {
				// console.log(e)
				console.log(this.value)
				if (!this.value) {
					this.show = false;
				}
			},
			getInput: function(e) {
				this.$emit('input', e.target.value);
				this.show = e.target.value.length ? true : false;
			},
			clear: function() {
				this.show = false;
				this.$emit('input', '');
        this.$emit('clear');
			}
		},
		mounted () {
			this.show = this.value.length ? true : false;
		},
		watch: {
		
		}
	}
</script>

<style lang="scss" scoped>
  .inputText {
		width: 80%;
		height: 38px;
		border: 1px solid #c7c7c7;
		display: flex;
		input[type="text"] {
			width: 80%;
			height: 36px;
			padding-left: 8px;
			font-size: 16px;
			border: none;
			outline: none;
		}
		.close {
			width: 14px;
			height: 14px;
			line-height: 14px;
			margin-top: 10px;
			margin-left: 10%;
			font-size: 20px;
			background: #999;
			color: #fff;
			border-radius: 50%;
		}
	}
	.curve {
		border-radius: 30px;
		padding-left: 15px;
	}
</style>