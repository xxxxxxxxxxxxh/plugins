<template>
  <div class="home">
    <!--<img alt="Vue logo" src="../assets/logo.png">-->
    <calendar
			:walk="false" 
			:clickEvent="true" 
			:eventData="eventData"
			v-on:giveData="getData"
			></calendar>
  </div>
</template>

<script>
// @ is an alias to /src

import Calendar from "../components/calendar/calendar";

export default {
  components: {Calendar},
  name: 'home',
	data () {
		return {
			eventData: [
				{date: '2019/8/15', event: '事件'},
				{date: '2019/8/7', event: '大事件'},
			]
		}
	},
	methods: {
		getData: function (res) {
			console.log(res)
		}
	}
}
</script>
