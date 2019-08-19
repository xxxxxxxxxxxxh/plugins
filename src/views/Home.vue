<template>
  <div class="home">
    <!--<img alt="Vue logo" src="../assets/logo.png">-->
    <calendar
			:walk="false" 
			:clickEvent="true" 
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
			eventData: []
		}
	},
	methods: {
		getData: function (res) {
			console.log(res)
		}
	}
}
</script>
