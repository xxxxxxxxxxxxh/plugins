<template>
  <div class="shop-select">
    <header class="shop-header">
      <div class="shop-header-l">
        <div class="img-box">
          <img src="../../images/1.jpg" alt="">
        </div>
      </div>
      <div class="shop-header-r">
        <p style="color: #f00">￥{{currentPrice}}</p>
        <p>库存{{goodsRest}}件</p>
        <p>请选择：{{selectItem}}</p>
      </div>
    </header>
    <section class="shop-opts">
      <div class="shop-options" v-for="(item, index) in shopOpts">
        <h4>{{item.name}}</h4>
				<!--
        <div>
          <radio
            v-for="(value, _index) in item.select"
            :radioName="item.name"
            :value="value.type"
            v-model="item.checked"
            :selectVal="item.checked"
          ></radio>
        </div>
				-->
        <div>
					<p
            v-for="(value, _index) in item.select"
            :class="{selected: value.active, lighten: value.disabled}"
            @click="setValue(value, index, _index)">{{value.type}}</p>
        </div>
      </div>
      <div class="shop-count">
        <h4>购买数量</h4>
        <div class="shop-count-select">
					<number-box :number="number" @getnum="getNum"></number-box>
        </div>
      </div>
    </section>
    <section class="shop-btn">
      <div v-if="isSureBtn" class="sure-btn" @click="makeSure">确定</div>
      <div class="flex-box" v-else>
        <div class="shopcart" @click="pushIntoShopcart">加入购物车</div>
        <div class="purchase" @click="pushIntoPurchase">购买</div>
      </div>
    </section>
  </div>
</template>

<script>
  import Radio from "../radio/radio";
  import NumberBox from "../numberBox/numberBox";

  export default {
    components: {Radio, NumberBox},
    name: "shop-select",
    data () {
      return {
        number: 1,
				num: 1,
				useGroupData: false,
				groupIndex: 0,
      }
    },
    props: {
      isSureBtn: Boolean,
			goodsData: Object,
			goods: Object,
			shopOpts: Array,
    },
    computed: {
      selectItem: function () {
        let str = '', array = this.shopOpts, result = [];
				array.forEach(function(item) {
				  result.push(item.name)
				})
				for (let i = 0; i < array.length; i++) {
          for (let j = 0; j < array[i]['select'].length; j++) {
					  if (array[i]['select'][j]['active'] === true) {
						  result.splice(i, 1, array[i]['select'][j]['type'])
						}
					}
        }
				str = result.join(' ');
        return str;
      },
			isDisabled: function () {
			  
			},
			currentPrice: function () {
			  if (this.useGroupData) {
				  return this.goods.group[this.groupIndex]['price'];
				} else {
				  return this.goods.price;
				}
			},
			goodsRest: function () {
			  if (this.useGroupData) {
				  return this.goods.group[this.groupIndex]['rest'];
				} else {
				  return this.goods.allrest;
				}
			},
    },
    methods: {
		  setOpts: function () {
			  this.shopOpts.forEach(function (item) {
				  item['select'].forEach(function (item) {
					  item.active = false;
						item.disabled = true;
					})
				})
			},
		  hasRest: function () {
			  let group = this.goods.group,
				    totalOpts = this.shopOpts;
				for(let i = 0; i < totalOpts.length; i++) {
				  for (let j = 0; j < group.length; j++) {
					  for (let k = 0; k < totalOpts[i]['select'].length; k++) {
						  if (totalOpts[i]['select'][k]['type'] == group[j][`type${i}`]) {
							  totalOpts[i]['select'][k]['disabled'] = false;
								break;
							}
						}
					}
				}
			},
		  getNum: function (data) {
			  this.num = data;
			},
			setValue: function (val, index, _index) {
				let array = this.shopOpts[index]['select'];
				for (let i = 0; i < array.length; i++) {
				  if (array['disabled']) {
					  return;
					}
				  if (i !== _index) {
						array[i]['active'] = false;
					}
				}
			  val.active = !val.active;
			},
			makeSure: function () {
        this.$emit('make-sure', this.getGoodsData());
      },
      pushIntoShopcart: function () {
        this.$emit('push-into-shopcart', this.getGoodsData());
      },
      pushIntoPurchase: function () {
        this.$emit('push-into-purchase', this.getGoodsData());
      },
			getGoodsData: function () {
			  return {
				  selectMessage: this.selectItem,
					selectNum: this.num,
				}
			},
    },
    mounted () {
      this.setOpts();
			this.hasRest();
			console.log(this.shopOpts)
    },
    watch: {
      'num': function (newer, older) {
			},
			'selectItem': function (newer, older) {
				console.log(newer)
				let comparison = [], compared = [];
				this.shopOpts.forEach(function (item) {
				  comparison.push(item.name);
				})
				comparison.forEach(function (item, index) {
				  if (!newer.includes(item)) {
					  compared.push(index);
					}
				})
				console.log(compared)
				if (compared.length === comparison.length) {
				  this.useGroupData = true;
				} else {
				  this.useGroupData = false;
				}
				// 获取对比group查看是否有存货（无组合、存货0）获取索引
				for (let i = 0; i < compared.length; i++) {
				  for (let j = 0; j < this.goods.group.length; j++) {
					  
					}
				}
			},
    }
  }
</script>

<style lang="scss" scoped>
  $border: 1px solid #a2a2a2;
  $borderSelected: 1px solid #f00;
  .shop-select {
    width: 100vw;
    height: 70vh;
    background: #fff;
    position: fixed;
    bottom: 0;
    z-index: 20;
    .shop-header {
      height: 17vh;
      padding: 8px 0;
      border-bottom: $border;
      display: flex;
      .shop-header-l {
        flex-basis: 40vw;
        position: relative;
        .img-box {
          width: 30vw;
          height: 30vw;
          padding: 5px;
          border-radius: 8px;
          overflow: hidden;
          position: absolute;
          top: -8vw;
          left: 5vw;
          img {
            width: 100%;
            height: 100%;
          }
        }
      }
      .shop-header-r {
        flex-basis: 60vw;
        text-align: left;
      }
    }
    .shop-opts {
      width: 100vw;
      height: calc(53vh - 45px);
      text-align: left;
      overflow-y: scroll;
      .shop-options {
        padding: 8px;
        border-bottom: $border;
        h4 {
          font-weight: normal;
          padding: 8px 0;
        }
        div {
          /*padding: 0 0 8px 0;*/
          display: flex;
          align-items: left;
          justify-content: flex-start;
          .radio {
            margin-right: 2vw;
          }
          p {
            padding: 3px 5px;
            margin-right: 2vw;
            background: #f0f0f0;
						color: #2c3e50;
						border: 1px solid #f0f0f0;
            border-radius: 5px;
          }
          .lighten {
            color: #979797;
          }
          .selected {
            border: $borderSelected;
            color: #f00;
          }
        }
      }
      .shop-count {
        line-height: 30px;
        padding: 12px 8px;
        display: flex;
        h4 {
          font-weight: normal;
          flex: 3;
        }
        .shop-count-select {
          flex: 2;
          display: flex;
          align-items: center;
          justify-content: space-around;
          .lighten {
            color: #979797;
          }
          span {
            display: inline-block;
            width: 30px;
            height: 30px;
            font-size: 26px;
            font-weight: normal;
            text-align: center;
            border: $border;
          }
          input[type='text'] {
            width: 30px;
            height: 30px;
            text-align: center;
            border: none;
          }
        }
      }
    }
    .shop-btn {
      width: 100vw;
      height: 45px;
      line-height: 45px;
      text-align: center;
      color: #fff;
      .sure-btn {
        background: #f00;
      }
      .flex-box {
        display: flex;
        .shopcart {
          background: orange;
          flex: 1;
        }
        .purchase {
          background: #f00;
          flex: 1;
        }
      }

    }
  }
</style>