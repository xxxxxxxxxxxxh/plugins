<template>
  <div class="wx-calendar">
    <div class="calendar-bar">
      <div class="calendar-bar-btn" @click="_preMonth"><</div>
      <div class="calendar-time" @click="_selectDate">{{year}}-{{month}}</div>
      <div class="calendar-bar-btn" @click="_nextMonth">></div>
    </div>
    <div class="wx-week">
      <div v-for="(item, index) in week" :key="index">
        {{item}}
      </div>
    </div>
    <div class="wx-date">
      <div class="wx-date-day" v-for="(item, index) in dailyWork" :key="index">
        <div
          :class="{current: dailyWork[index]['_current']}"
          @click="_showContent(index)"
        >
          {{item._day}}
        </div>
        <div v-if="dailyWork[index]['show']">
          <div
            :class="{calendarTips: dailyWork[index]['quota']}"
          ></div>
        </div>
				<div>
          <div
						class="calendar-hasEvent"
            :class="{calendarTips: dailyWork[index].hasOwnProperty('event')}"
          ></div>
        </div>
      </div>
    </div>
    <div v-if="showMessege" class="calendar-content">
      <div class="calendar-content-close" @click="_hideContent">×</div>
      <div v-if="walk">步数：{{detailWalk}}</div>
			<div class="calendar-content-event" v-if="clickEvent">
				<p v-if="!isEdit">{{dailyWork[eventIndex]['event']}}</p>
        <input ref="input" v-else type="text" v-model="dailyWork[eventIndex]['event']">
        <button v-if="!isEdit" @click="isEdit = true">事件编辑</button>
        <button v-else @click="_setEvent">确定</button>
			</div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "calendar",
    data () {
      return {
        week: ['日', '一', '二', '三', '四', '五', '六'],
        year: '',
        month: '',
        //对象数组 （日，时间戳，判断，事件(暂无)）
        dailyWork: [],
        currentMonth: '',
        showMessege: false,
        // 点击弹窗数据
        detailWalk: '',
        originalData: null,
        showDateSelect: true,
				eventIndex: 0,
				isEdit: false,
      }
    },
    props: {
      walk: Boolean,
      clickEvent: Boolean,
			eventData: Array
    },
    methods: {
      /**
       * @
       * @ dateStr 当前月一号字符串
       * @ num 该月份天数
       */
      _drawCalendar: function (dateStr, num) {
        let dateList = [],
          str = '';
        let startIndex = new Date(dateStr).getDay();
        let nowIndex = new Date(new Date()).getDate();
        let _length = startIndex + num;
        let _compared = this.originalData;
        // console.log(_length)
        for (let i = 0; i < _length; i++) {
          if (i < startIndex) {
            let obj = {}
            obj._day = '';
            obj.timeStamp = '';
            obj._current = false;
            dateList.push(obj);
            obj = {}
          } else {
            str = dateStr.substr(0, dateStr.length - 2) + '/' + (i - startIndex + 1);
            let obj = {}
            obj._day = i - startIndex + 1;
            obj.timeStamp = new Date(str).getTime();
            obj._current = (i - startIndex + 1) === nowIndex ? true : false;
            dateList.push(obj);
            obj = {}
          }
        }
        if (_compared) {
          dateList = this._operateStep(_compared, dateList)
        }
				this._setEventKey(dateList);
        // console.log(dateList)
        return dateList;
      },
      /**
       * @ 获取任一月份的天数
       * @ month 月份字符串
       * @ year 年份
       */
      _getDayNum: function (month, year) {
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
       * @ 上下月份的操作
       * @ opt 字符串 (pre或者next)
       */
      _changeCalendar: function (opt) {
        let _dateStr = this.currentMonth;
        let _dateArr = _dateStr.split('/');
        let _month = 0,
            _year = 0;
        if (opt.indexOf('pre') > -1) {
          if (_dateArr[1] - 1 > 0) {
            _dateArr.splice(1, 1, _dateArr[1] - 1);
          } else {
            _dateArr.splice(0, 2, _dateArr[0] - 1, 12)
          }
        } else {
          // console.log(_dateArr[1])
          if (+_dateArr[1] + 1 <= 12) {
            _dateArr.splice(1, 1, +_dateArr[1] + 1);
          } else {
            _dateArr.splice(0, 2, +_dateArr[0] + 1, 1)
          }
        }
        _month = _dateArr[1];
        _year = _dateArr[0];
        let _dayNum = this._getDayNum(_month, _year);
        _dateStr = _dateArr.join('/');
        this.year = _year;
        this.month = this._replenish(_month);
        this.currentMonth = _dateStr;
        this.dailyWork = this._drawCalendar(_dateStr, _dayNum);
      },
      /**
       * @ 上个月
       */
      _preMonth: function () {
        this._changeCalendar('pre')
      },
      /**
       * @ 下个月
       */
      _nextMonth: function () {
        this._changeCalendar('next')
      },
      _selectDate: function () {

      },
      /**
       * @ 补充0的前置
       * @ num 处理的参数
       */
      _replenish: function (num) {
        return num = num >= 10 ? num : `0${num}`
      },
      /**
       * @ 点击日期显示内容
       */
      _showContent: function (_index) {
        if (!this.clickEvent) {
          return
        }
        let _data = null;
        // console.log(this.dailyWork)
        if (this.dailyWork[_index]['step']) {
          _data = this.dailyWork[_index]['step'];
        } else {
          _data = '暂无数据';
        }
				this.eventIndex = _index;
        this.showMessege = true;
        this.detailWalk = _data;
      },
      /**
       * @ 关闭弹窗
       */
      _hideContent: function () {
        this.showMessege = false;
      },
      /**
       * @ 赋值dailyWalk步数
       * @ comapre 父组件传来的变化后的步数数据对象
       * @ operate 需要合并步数及判断属性的数据对象
       */
      _operateStep: function (compare, operate) {
        for (let i = 0; i < operate.length; i++) {
          for (let j = 0; j < compare.length; j++) {
            if (operate[i]['timeStamp'] == (compare[j]['timestamp'] + '000')) {
              operate[i]['step'] = compare[j]['step'];
              operate[i]['quota'] = compare[j]['quota'];
              break;
            }
          }
        }
        for (let i of operate) {
          if (i.hasOwnProperty('quota')) {
            i['show'] = true;
          } else {
            i['show'] = false;
          }
        }
        return operate;
      },
			/**
			 * @ 增加 event 属性
			 */
			_setEventKey: function (list) {
				if (!this.clickEvent) {
					return;
				}
				console.log(this.eventData)
				if (!this.eventData) {
					return;
				}
				this._getTimeStamp();
				for (let i = 0; i < this.eventData.length; i++) {
					for (let j = 0; j < list.length; j++) {
						if (this.eventData[i]['timeStamp'] == list[j]['timeStamp']) {
							this.$set(list[j], 'event', this.eventData[i]['timeStamp']);
							// list[j]['event'] = this.eventData[j]['timeStamp'];
							break;
						}
					}
				}
			},
			/**
			 * @ 处理传入的时间字符串, 获取时间戳
			 */
			_getTimeStamp: function () {
				let _date = '';
				for (let i = 0; i < this.eventData.length; i++) {
					_date = this.eventData[i]['date']
					this.eventData[i]['timeStamp'] = new Date(_date).getTime();
				}
			},
			/**
			 * @ 增加事件内容, 传出事件所在对象 $emit(giveData)
			 *
			 */
			_setEvent: function () {
				let text = this.$refs.input.value,
						index = this.eventIndex;
				this.$set(this.dailyWork[index], 'event', text);
				this.isEdit = false;
				this.$emit('giveData', this.dailyWork[index])
			},
    },
    created () {
      let _date = new Date(),
          _year = _date.getFullYear(),
          _month = _date.getMonth() + 1;
      let _str = `${_year}/${_month}/1`,
          _dayNum = this._getDayNum(_month, _year);
      this.year = _year;
      this.month = this._replenish(_month);
      this.currentMonth = _str;
      this.dailyWork = this._drawCalendar(_str, _dayNum);
			console.log(this)
    },
    watch: {

    }
  }
</script>

<style scoped>
  .wx-calendar {
    width: 100%;
    margin-top: 10px;
    position: relative;
  }
  .calendar-bar {
    width: 100%;
    height: 40px;
    line-height: 40px;
    text-align: center;
    display: flex;
  }
  .calendar-bar-btn {
    width: 40px;
  }
  .calendar-time {
    flex: 1;
  }
  .wx-week {
    width: 100%;
    font-size: 14px;
    display: flex;
    flex-wrap: nowrap;
  }
  .wx-week div {
    flex: 1;
    text-align: center;
  }
  .wx-date {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
  }
  .wx-date-day {
    flex-basis: 14.25%;
    height: 50px;
    line-height: 40px;
    text-align: center;
    position: relative;
  }
  .current {
    width: 36px;
    height: 36px;
    border-radius: 50%;
    background: #0095ff;
    color: #fff;
    position: absolute;
    left: calc(50% - 18px);
    top: 2px;
  }
  .calendar-content {
    width: 70%;
    height: 40vw;
    background: #fff;
    padding: 20px;
    position: absolute;
    left: 15%;
    top: 20vw;
    box-shadow: 0 0 3px 3px #dfdfdf;
  }
	.calendar-content-event {
		
	}
	.calendar-content-event button {
	  background: #fff;
    padding: 5px 8px;
    color: #444;
		border: none;
		position: absolute;
    bottom: 15px;
    right: 15px;
	}
	.calendar-content-event p {
		text-align: left;
		line-height: 26px;
		padding: 15px 0;
		padding-left: 8px;
	}
	.calendar-content-event input {
		width: 100%;
		line-height: 30px;
		margin: 12px 0;
		padding-left: 8px;
		color: #444;
		font-size: 16px;
		border: none;
		border-bottom: 1px solid #eee;
	}
  .calendar-content-close {
    width: 20px;
    height: 20px;
    position: absolute;
    right: 20px;
    top: 10px;
  }
  .calendarTips {
    width: 5px;
    height: 5px;
    border-radius: 50%;
    position: absolute;
    left: calc(50% - 2.5px);
    bottom: 5px;
  }
	.calendar-hasEvent {
		background: #0095ff;
		bottom: 0;
	}
  .calendar-done {
    background: greenyellow;
  }
  .calendar-didnt {
    background: #f00;
  }
  .calendar-date-selector {
    width: 100vw;
    /*height: 50vh;*/
    background: #fff;
    position: fixed;
    bottom: 0;
    box-shadow: 0 0 10px #eee;
  }
  .calendar-date-bar {
    width: 100vw;
    height: 30px;
    line-height: 30px;
    padding: 0 5vw;
    text-align: right;
    box-sizing: border-box;
  }
  .calendar-date-select {
    width: 80vw;
    display: flex;
    margin: 0 10vw;
  }
  .calendar-date {
    flex-basis: 40vw;
    flex: 1;
    display: inline-block;
  }
  .calendar-date li {
    width: 40vw;
    height: 40px;
    line-height: 40px;
    text-align: center;
  }
</style>