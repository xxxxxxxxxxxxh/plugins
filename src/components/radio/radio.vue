<template>
  <div class="radio" :class="[selectVal === value ? 'active' : 'unactive']">
    <input
      :name="radioName"
      class="transparent-radio"
      type="radio"
      :value="value"
			:selectVal="selectVal"
      @change="changeState($event)"
    >
    <div>{{value}}</div>
  </div>
</template>

<script>
  export default {
    name: "radio",
    data () {
      return {
        select: ''
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
    },
    methods: {
      changeState: function (e) {
        this.select = e.target.checked
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
    color: #cdcdcd;
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
</style>