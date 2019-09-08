<template>
  <div class="home">
    <!--<img alt="Vue logo" src="../assets/logo.png">-->
    <calendar
			:walk="false"
			:clickEvent="true" 
			:eventData="eventData"
			v-on:giveData="getData"
		></calendar>
		<!--<p>
			picker： 	
			defaultType ———— 滚动内容是否自定义（true 或者 false） 
			default ———— 滚动区域初始值  
			defaultList ———— 自定义内容 [{column: []}] 列表内容在column内 
		</p>-->
		<!--<datepicker :defaultType="type" :default="b" :defaultList="defaultList" ></datepicker>-->
		<!--<datepicker :defaultType="false" :default="a"></datepicker>-->
    <card :isShadow='true' :isHorizontal="false">
			<h2>默认slot</h2>
			<template v-slot:customize>
				<div style="color: #f00;">
					<p>slot插槽</p>
				</div>
			</template>
		</card>
  </div>
</template>

<script>
// @ is an alias to /src

import Calendar from "../components/calendar/calendar";
import Datepicker from "../components/datepicker/datepicker";
import Card from "../components/card/card";

export default {
  components: {Calendar, Datepicker, Card},
  name: 'home',
	data () {
		return {
			eventData: [
				
			],
			type: true,
			false: false,
			a: '2019-8-15',
			b: '宁波',
			defaultList: [{column: ['杭州', '宁波', '温州', '嘉兴', '湖州', '丽水']}],
		}
	},
	methods: {
		getData: function (res) {
			console.log(res)
		},
    
	},
	async created () {
		// let res = await fetch('http://127.0.0.1:3030/exam');
		// let data = await res.json();
		// console.log(data)
		// this.eventData = data.eventData
		//this.defaultList = data.defaultList
	}
}
</script>
