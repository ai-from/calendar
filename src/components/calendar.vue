<template>
  <div class="calendar">

    <div class="calendar_header">
      <img class="calendar_left-arrow" @click="monthMinus" src="../assets/calendar/leftArrow.svg" alt="">
      <div class="calendar_month">{{ monthName }}<span v-if="differentYear"> {{ year }}</span></div>
      <img class="calendar_right-arrow" @click="monthPlus" src="../assets/calendar/rightArrow.svg" alt="">
    </div> <!-- calendar_header -->

    <div class="calendar_body">

      <div class="calendar_body-days">
        <div class="calendar_body-day"
             v-for="(day, index) in days"
             :key="index"
        >
          {{ day }}
        </div>
      </div> <!-- calendar_body-days -->

      <div class="calendar_body-lines">
        <div class="calendar_body-item"
          v-for="(item, index2) in likeMonth"
          :key="index2"
          :class="{'past': item.past, 'current': item.curr, 'weekend': item.weekend}"
        >
          <div class="calendar_body-date">
            {{ item.number }}
          </div>
          <div class="calendar_body-titles">
            <div class="calendar_body-title"
                 v-for="(event, index3) in filteredEvents"
                 :key="index3"
                 :class="event.type"
                 v-if="+(index2 + 2 - startingDay) === +event.day"
            >
              {{ event.name }}
            </div>
          </div>
        </div>
      </div> <!-- calendar_body-lines -->

    </div> <!-- calendar_body -->

  </div> <!-- calendar -->
</template>

<script>
import moment from "moment";
moment.locale('ru');

export default {
  name: 'Calendar',
  props: {
    events: []
  },
  data() {
    return {
      changedDate: {},
      days: ['Понедельник', 'Вторник', 'Среда', 'Четверг', 'Пятница', 'Суббота', 'Воскресенье'],
      year: 0,
      month: 0,
      day: 0,
      monthName: '',
      countOfDays: 0,
      startingDay: 0,
      rowsCount: 0,
      differentYear: false,
      filteredEvents: [],
      likeMonth: []
    }
  },
  methods: {
    monthMinus() {
      this.changedDate = moment({year: this.changedDate.year(), month: this.changedDate.month()}).subtract(1, 'months');
      this.setData();
    },
    monthPlus() {
      this.changedDate = moment({year: this.changedDate.year(), month: this.changedDate.month()}).add(1, 'months');
      this.setData();
    },
    rowsCountFunc() {
      let firstLine = 7 - this.startingDay + 1
      let exceptFirstLine = this.countOfDays - firstLine
      let fullLines = Math.floor(exceptFirstLine / 7)
      let restLine = exceptFirstLine % 7 > 0 ? 1 : 0
      this.rowsCount =  1 + fullLines + restLine
    },
    filteredEventsFunc() {
      this.filteredEvents = []
      this.events.forEach(item => {
        let parts = item.date.split('.')
        if(this.month + 1 === +parts[1] && this.year === +parts[2]) {
          this.filteredEvents.push({
            id: item.id,
            name: item.name,
            day: parts[0],
            type: item.type
          })
        }
      })
    },
    linkMonthFunc() {
      this.likeMonth = []
      let firstPart = [], secondPart = [], thirdPart = []

      let prevDaysCount = this.startingDay - 1
      let prevMonth = moment({year: this.year, month: this.month}).subtract(1, 'months')
      let countOfDaysPrevMonth = prevMonth.daysInMonth()

      let firstPartYear
      let thirdPartYear
      let firstPartMonth
      let thirdPartMonth
      if(this.month === 0) {
        firstPartYear = this.year - 1
        thirdPartYear = this.year
        firstPartMonth = 11
        thirdPartMonth = 1
      }
      else if(this.month === 11) {
        firstPartYear = this.year
        thirdPartYear = this.year + 1
        firstPartMonth = 10
        thirdPartMonth = 0
      }
      else if(this.month > 0 && this.month < 11) {
        firstPartYear = this.year
        thirdPartYear = this.year
        firstPartMonth = this.month - 1
        thirdPartMonth = this.month + 1
      }

      for(let i = countOfDaysPrevMonth - prevDaysCount + 1; i <= countOfDaysPrevMonth; i++) firstPart.push({
        number: i,
        past: moment({year: firstPartYear, month: firstPartMonth, day: i}).valueOf() < moment().valueOf(),
        curr: false,
        weekend: (+moment({year: firstPartYear, month: firstPartMonth, day: i}).day() === 0
          || +moment({year: firstPartYear, month: firstPartMonth, day: i}).day() === 6)
      })

      for(let i = 0; i < this.countOfDays; i++) secondPart.push({
        number: i + 1,
        past: moment({year: this.year, month: this.month, day: i + 1}).valueOf() < moment().valueOf(),
        curr: this.year === moment().year() && this.month === moment().month() && (i + 1) === moment().date(),
        weekend: (+moment({year: this.year, month: this.month, day: i + 1}).day() === 0
          || +moment({year: this.year, month: this.month, day: i + 1}).day() === 6)
      })

      let nextDaysCount = this.rowsCount * 7 - (firstPart.length + secondPart.length)
      for(let i = 1; i <= nextDaysCount; i++ ) thirdPart.push({
        number: i,
        past: moment({year: thirdPartYear, month: thirdPartMonth, day: i}).valueOf() < moment().valueOf(),
        curr: false,
        weekend: (+moment({year: thirdPartYear, month: thirdPartMonth, day: i}).day() === 0
          || +moment({year: thirdPartYear, month: thirdPartMonth, day: i}).day() === 6)
      })

      if(firstPart.length > 0) this.likeMonth = [...firstPart]
      this.likeMonth = [...this.likeMonth, ...secondPart]
      if(thirdPart.length > 0) this.likeMonth = [...this.likeMonth, ...thirdPart]
    },
    setData() {
      this.year = this.changedDate.year()
      this.month = this.changedDate.month()
      this.day = this.changedDate.date()

      this.monthName = moment({year: this.year, month: this.month}).format('MMMM').charAt(0).toUpperCase()
        + moment({year: this.year, month: this.month}).format('MMMM').slice(1)
      this.countOfDays = moment({year: this.year, month: this.month}).daysInMonth()
      this.startingDay = moment({year: this.year, month: this.month}).day()
      if(this.startingDay === 0) this.startingDay = 7

      this.rowsCountFunc();
      this.differentYear = !(moment().year() === this.year)
      this.filteredEventsFunc()
      this.linkMonthFunc()

    }
  },
  created() {
    this.changedDate = moment()
    this.setData()
  }
}
</script>

<style lang="sass">
.calendar
  display: table
  background: white
  border-radius: 25px
  padding: 20px
  &_header
    display: grid
    grid-template-columns: min-content 120px min-content
    grid-gap: 10px
    align-items: center
    justify-items: center
    margin-bottom: 20px
  &_left-arrow,
  &_right-arrow
    cursor: pointer
    height: 14px
    width: auto
  &_month
    white-space: nowrap
    text-transform: uppercase
    color: cornflowerblue
    font-weight: bold

  &_body
    display: grid
    grid-template-rows: repeat(2, min-content)
  &_body-days
    display: grid
    grid-template-columns: repeat(7, 140px)
    grid-gap: 4px
    justify-items: end
    margin-bottom: 10px
  &_body-day
    text-transform: uppercase
    font-size: 12px
    font-weight: bold
    color: black
    &:nth-last-child(2),
    &:last-child
      color: rgba(0, 0, 0, .5)

  &_body-lines
    display: grid
    grid-template-columns: repeat(7, 140px)
    grid-template-rows: auto
    grid-gap: 4px
  &_body-item
    border: 1px solid rgba(0, 0, 0, .1)
    border-radius: 10px
    padding: 10px
    min-height: 60px
    &.past
      border: 1px solid rgba(0, 0, 0, .1)
      background: rgba(0, 0, 0, .1)
      .calendar_body-date
        color: rgba(0, 0, 0, .5)
    &.weekend
      .calendar_body-date
        color: #c8a2c8
    &.current
      background: transparent
      border: 1px solid rgba(0, 0, 0, .5)
      .calendar_body-date
        color: green
  &_body-date
    text-align: right
    margin-bottom: 5px
    color: black
    font-weight: bold
  &_body-title
    justify-self: start
    white-space: nowrap
    display: block
    overflow: hidden
    text-overflow: ellipsis
    margin-bottom: 1px
    padding: 2px
    position: relative
    min-width: 100%
    &:hover
      width: max-content
      z-index: 10
    &.green
      color: #007400
      background: #61dd61
    &.red
      color: #dd0000
      background: #ff7e7e
    &.orange
      color: #7c550e
      background: #f9b539
</style>