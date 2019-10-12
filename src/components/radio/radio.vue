<template>
  <div class="radio" :class="[selectVal === value ? 'active' : '']">
    <input
      :name="radioName"
      class="transparent-radio"
      type="radio"
      :value="value"
			:selectVal="selectVal"
			:disabled="disabled"
      @change="changeState($event)"
    >
    <div :class="{disabled: disabled}">{{value}}</div>
  </div>
</template>

<script>
  export default {
    name: "radio",
    data () {
      return {
        stateVal: false
      }
    },
    model: {
      prop: 'label',
      event: 'change'
    },
    props: {
      value: String,
      radioName: String,
      selectVal: String,
			disabled: Boolean
    },
    methods: {
      changeState: function (e) {
        this.$emit('change', e.target.value)
      }
    },
    watch: {
      'select': function (older, newer) {
      }
    }
  }
</script>

<style lang="scss" scoped>
  .radio {
    display: inline-block;
    padding: 3px 6px;
    background: #f0f0f0;
    color: #2c3e50;
    border-radius: 5px;
    position: relative;
    .transparent-radio {
      width: 100%;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      opacity: 0;
    }
  }
  .active {
    background: pink;
    color: #f00;
  }
  .unactive {
    background: #f0f0f0;
    color: #cdcdcd;
  }
	.disabled {
		color: #cdcdcd;
	}
</style>