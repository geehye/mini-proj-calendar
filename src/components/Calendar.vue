<template>
<b-container class="">
  <div class="calendar-container">
    <div class="calendar-head">
      <div class="nav-search-bar">
        <div class="calendar-nav">
          <span class="prev" v-on:click="addMonth(-1)"><</span>
          <!-- yyyy.mm 형태로 표현 -->
          <span class="current">{{currentYear}}.{{('0' + currentMonth).slice(-2)}}</span>
          <span class="next" v-on:click="addMonth(1)">></span>
        </div>
      </div>
      <div class="dayname-container">
        <div class="calendar-cell" v-for="dayname in daynames">
          <div class="day-label name-of-days">{{dayname}}</div>
        </div>
      </div>
    </div>
    <div class="calendar-body">{{calcWeeks()}}
      <div class="calendar-week" v-for="week in weeks">{{calcWeekDays(week)}}
        <div class="calendar-cell" v-for="day in weekDays">
          <div class="day-label" v-bind:style="[(day == todayDate && currentMonth == todayMonth)? {'color': '#ff6813'} : {'color': '#777'}]">{{day}}</div>
          <div class="day-content" v-bind:id="'item-' + day" v-on:click="popAlert(day)">
            <!-- evt.date 는 yyyy-mm-dd 형식 -->
            <div class="calendar-item" v-for="evt in events" v-if="evt.date == (currentYear + '-' + ('0' + currentMonth).slice(-2) + '-' + ('0' + day).slice(-2))">
              <!-- 이전 달에 출력되는 다음 달 첫 주, 이번 달에 출력되는 이전 달의 마지막 주의 일정 출력 방지 (week은 1부터 시작) -->
              <span v-if="(week == 1 && day < 20) || (week > 0 && week < 4) || (week >= 4 && day > 20)">{{evt.event}}</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</b-container>
</template>

<script>
import schedule from '../assets/Web.json'

export default {
  name: 'Calendar',
  components: {
  },
  data() {
    var today = new Date()
    return {
      daynames: ['SUN', 'MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT'],
      currentYear: today.getFullYear(),
      currentMonth: (today.getMonth()+1),
      todayMonth: (today.getMonth()+1),
      todayDate: today.getDate(),
      dateCnt: 0,
      prevLastDate: 0,
      currentLastDate: 0,
      firstDay: 0,
      weeks: 0,
      weekDays: [],
      events: schedule,
      evtsForAlert: []
    }
  },
  methods: {
    addMonth(num) {
      this.currentMonth += num
      if(this.currentMonth == 0) { // 0이면 지난 해 12월
        this.currentMonth = 12
        this.currentYear--
      } else if(this.currentMonth > 12) { // 13이면 다음 해 1월
        this.currentMonth = 1
        this.currentYear++
      }
      this.calcWeeks()
    },
    calcWeeks() {
      this.dateCnt = 1
      this.prevLastDate = new Date(this.currentYear, this.currentMonth-1, 0).getDate()
      this.currentLastDate = new Date(this.currentYear, this.currentMonth, 0).getDate()
      this.firstDay = new Date(this.currentYear, this.currentMonth-1, 1).getDay()
      // firstDay = 1월1일의 요일, 0부터 시작하며 일요일이다
      this.weeks = Math.ceil((this.firstDay + this.currentLastDate) / 7)
    },
    calcWeekDays(week) {
      for(var i = 0; i < 7; i++) {
        if(week == 1 && this.firstDay > i) // 첫째주에 이전 달 마지막주 출력
          this.weekDays[i] = (this.prevLastDate - this.firstDay + 1) + i
        else if(this.currentLastDate < this.dateCnt) // 마지막주에 다음 달 첫째주 출력
          this.weekDays[i] = this.dateCnt++ % this.currentLastDate
        else
          this.weekDays[i] = this.dateCnt++
      }
    },
    popAlert(day) {
      alert(document.getElementById('item-' + day).innerText)
    }
  }
}
</script>
