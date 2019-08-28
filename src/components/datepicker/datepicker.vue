<template>
	<div class="datepicker">
		
		<div class="picker-scroll" v-if="show">
			<div class="picker-bar">
				<div class="btn-sure" @click="getSelector">确定</div>
			</div>
			<div class="selector">
				<div
					ref="selector"
					class="column-selector"
					v-for="(list, index) in lists"
					:key="index"
					@touchstart="touchStart(index,$event)"
					@touchmove="touchMove(index,$event)"
					@touchend="touchEnd(index,$event)"
				>
					<ul ref="wrapper">
						<li
							v-for="(item, _index) in list.column"
							:key="_index"
						>{{item}}</li>
					</ul>
				</div>
				<div class="column-selector-mask"></div>
				<div class="selected-bar"></div>
			</div>
		</div>
	</div>
</template>

<script>
	export default {
		name: 'datepicker',
		data () {
			return {
				show: true,
				yAxis: 0,
				lastAxis: 0,
				duration: 0,
				lists: [],
				itemHeight: 36,
				indexArr: [],
				firstIndex: '',
				distant: 0
			}
		},
		props: {
			defaultType: Boolean,
			defaultList: Array,
			default: String
		},
		methods: {
			/**
			 * @ 开始触摸
			 * @ index 索引
			 * @ e 事件
			 */
			touchStart: function (index, e) {
				let _top = this.$refs.selector[index].getBoundingClientRect().top,
						_height = this.$refs.selector[index].getBoundingClientRect().height;
				let _distance = 0;
				this.yAxis = e.targetTouches[0]['pageY'];
				_distance = Math.ceil((this.yAxis - _top)/this.itemHeight);
				this.firstIndex = _distance - 3 + this.indexArr[index];
				this.range(index)
				this.startTime = performance.now();
			},
			/**
			 * @ 触摸滑动
			 * @ index 索引
			 * @ e 事件
			 */
			touchMove: function (index, e) {
				this.lastAxis = e.targetTouches[0]['pageY']
				let _value = 0;
				_value = this.lastAxis - this.yAxis + this.distant;
				this.$refs.wrapper[index].style.transform = `translateY(${_value}px)`
			},
			/**
			 * @ 结束触摸
			 * @ index 索引
			 * @ e 事件
			 */
			touchEnd: function (index, e) {
				let endTime = performance.now();
				let _value = 0, _index = 0;
				if (endTime - this.startTime < 200) {
					this.clickDistant(index);
					return;
				}
				let _length = this.lastAxis - this.yAxis;
				if (Math.abs(_length) > 5 * this.itemHeight) {
					this.clickDistant(index)
				} else {
					_value = Math.round(_length / this.itemHeight) * this.itemHeight + this.distant;
					if (_value >= 2 * this.itemHeight) {
						_value = 2 * this.itemHeight;
					}
					let arrLength = this.lists[index]['column'].length;
					if (_value < (arrLength - 3) * -this.itemHeight) {
						_value = (arrLength - 3) * -this.itemHeight;
					}
					this.$refs.wrapper[index].style.transform = `translateY(${_value}px)`;
					_index = Math.abs((2 * this.itemHeight - _value) / this.itemHeight);
					this.indexArr[index] = _index;
					this.distant = _value;
				}
			},
			/**
			 * @ 判断上下边界
			 */
			range: function (index) {
				let maxRange = this.lists[index]['column'].length - 1;
				if (this.firstIndex < 0) {
					this.firstIndex = 0;
				} else if (this.firstIndex > maxRange) {
					this.firstIndex = maxRange;
				}
			},
			/**
			 * @ 点击情况
			 * @ index 索引
			 */
			clickDistant: function (index) {
				let _value = 0;
				_value = 2 * this.itemHeight - this.firstIndex * this.itemHeight;
				this.$refs.wrapper[index].style.transform = `translateY(${_value}px)`
				this.indexArr[index] = this.firstIndex;
				this.distant = _value
			},
			/**
			 * @ 获取月份天数
			 */
			getday: function () {
				let year = new Date().getFullYear();
				let month = new Date().getMonth() + 1;
				switch (month) {
					case 1:
					case 3:
					case 5:
					case 7:
					case 8:
					case 10:
					case 12:
						return 31;
					case 4:
					case 6:
					case 9:
					case 11:
						return 30;
					case 2:
						if (+year%4 === 0) {
							return 29;
						} else {
							return 28;
						}
          default:
            return false;
        }
			},
			/**
			 * @ 日期滚动条的默认值设置、赋值
			 */
			getDefalutDate: function () {
				let year = {type: 'year', column: []},
						month = {type: 'month', column: []},
						date = {type: 'date', column: []};
				let days = this.getday();
				let _year = new Date().getFullYear();
				for (let i = 1; i <= days; i++) {
					date.column.push(i)
				}
				for (let i = 1; i <= 12; i++) {
					month.column.push(i)
				}
				for (let i = _year - 2; i < _year + 2; i++) {
					year.column.push(i)
				}
				this.lists = [year, month, date]
			},
			/**
			 * @ 获取滚动条内容
			 */
			getInitList: function () {
				if (this.defaultType) {
					// 自定义滚动内容
					this.lists = this.defaultList;
				} else {
					// 默认日期列表内容
					this.getDefalutDate();
				}
				this.getSelectData();
			},
			/**
			 * @ 初始值设置
			 * @ index 索引
			 * @ e 事件
			 */
			getSelectData: function () {
				console.log(this.$refs.wrapper)
				let str = this.default,
						strArr = [];
				if (str) {
					strArr = str.split('-');
				} else {
					for (let i = 0; i < this.lists.length; i++) {
						strArr.push(0);
					}
				}
				for (let i = 0; i < strArr.length; i++) {
					for (let j = 0; j < this.lists[i]['column'].length; j++) {
						if (strArr[i] == this.lists[i]['column'][j]) {
							this.indexArr.push(j);
							break;
						}
					}
				}
				// console.log(strArr)
				this.setTransform(strArr);
			},
			/**
		   * @ 确定按钮
			 **/
			getSelector: function () {
				let strArr = [], str = '';
				let _len = this.indexArr.length,
						_value = '';
				console.log(this.lists)
				console.log(_len)
				for (let i = 0; i < _len; i++) {
					_value = this.indexArr[i]
					strArr.push(this.lists[i]['column'][_value]);
				}
				str = strArr.join('-');
				console.log(str)
				this.show = false;
			},
			setTransform: function (arr) {
				let _distance = 0;
				console.log(this.indexArr)
				if (this.default) {
					this.$nextTick(function () {
						for (let i = 0; i < arr.length; i++) {
							_distance = (2 - this.indexArr[i]) * this.itemHeight;
							this.$refs.wrapper[i].style.transform = `translateY(${_distance}px)`
						}
					})
				}
			}
		},
		created () {
			console.log(this.$refs)
			this.getInitList();
		},
		mounted () {
			
		}
	}
</script>

<style lang="scss" scope>
	.datepicker {
		width: 100vw;
		position: fixed;
		bottom: 0;
		.picker-scroll {
			background: #fff;
			.picker-bar {
				width: 90%;
				height: 36px;
				line-height: 36px;
				margin-left: 5%;
				border-bottom: 1px solid #ddd;
				.btn-sure {
					float: right;
					margin-right: 4vw;
				}
			}
			.selector {
				height: 180px;
				margin: 10px 0;
				display: flex;
				position: relative;
				overflow: hidden;
				.column-selector {
					flex: 1;
					ul li {
						line-height: 36px;
					}
				}
				.selected-bar {
					position: absolute;
					top: 50%;
					left: 0;
					z-index: 3;
					width: 100%;
					height: 36px;
					-webkit-transform: translateY(-50%);
					transform: translateY(-50%);
					pointer-events: none;
				}
				.column-selector-mask {
					position: absolute;
					top: 0;
					left: 0;
					z-index: 2;
					width: 100%;
					height: 100%;
					background-image: linear-gradient(180deg, hsla(0, 0%, 100%, 0.9), hsla(0, 0%, 100%, 0.4)), linear-gradient(0deg, hsla(0, 0%, 100%, 0.9), hsla(0, 0%, 100%, 0.4));
					background-repeat: no-repeat;
					background-position: top, bottom;
					background-size: 100% 72px;
					-webkit-backface-visibility: hidden;
					backface-visibility: hidden;
					pointer-events: none;
				}
			}
		}
	}
</style>