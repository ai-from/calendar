<template>
  <div class="calendar">

    <div class="calendar_header">
      <img class="calendar_left-arrow" @click="monthMinus" src="../assets/calendar/leftArrow.svg" alt="">
      <div class="calendar_month">{{ monthName }}<span v-if="differentYear"> {{ year }}</span></div>
      <img class="calendar_right-arrow" @click="monthPlus" src="../assets/calendar/rightArrow.svg" alt="">
    </div>

    <div class="calendar_body">

      <div class="calendar_body-days">
        <div class="calendar_body-day"
             v-for="(day, index) in days"
             :key="index"
        >
          {{ day }}
        </div>
      </div>

      <div class="calendar_body-lines">
        <div class="calendar_body-item"
          v-for="(item, index2) in (countOfDays + startingDay - 1)"
          :key="index2"
          :class="index2 + 1 >= startingDay ? 'show' : 'hidden'"
        >
          <div
            class="calendar_body-date"
            v-if="index2 + 1 >= startingDay"
          >
            {{ index2 + 2 - startingDay }}
          </div>
          <div class="calendar_body-titles">
            <div
              class="calendar_body-title"
              :class="event.type"
              v-for="(event, index3) in filteredEvents"
              :key="index3"
              v-if="+(index2 + 2 - startingDay) === +event.day"
            >
              {{ event.name }}
            </div>
          </div>

        </div>
      </div>

      <div v-if="false">
        Год: {{ year }} <br>
        Месяц строкой: {{ monthName }} <br>
        Месяц: {{ month }} <br>
        Число: {{ day }} <br>
        Количество дней в месяце: {{ countOfDays }} <br>
        С какого дня по счету начинается месяц: {{ startingDay }} <br>
        Количество рядов: {{ rowsCount }}
      </div>

    </div>

  </div>
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
      filteredEvents: []
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
    setData() {
      this.year = this.changedDate.year()
      this.month = this.changedDate.month()
      this.day = this.changedDate.date()

      let monthString = moment({year: this.year, month: this.month}).format('MMMM')
      this.monthName = monthString.charAt(0).toUpperCase() + monthString.slice(1)
      this.countOfDays = moment({year: this.year, month: this.month}).daysInMonth()
      this.startingDay = moment({year: this.year, month: this.month}).day()
      if(this.startingDay === 0) this.startingDay = 7

      // let firstLine = 7 - this.startingDay + 1
      // let exceptFirstLine = this.countOfDays - firstLine
      // let fullLines = Math.floor(exceptFirstLine / 7)
      // let restLine = exceptFirstLine % 7 > 0 ? 1 : 0
      // this.rowsCount =  1 + fullLines + restLine

      this.differentYear = !(moment().year() === this.year)

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
      console.log(this.filteredEvents)
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
    border: 1px solid rgba(0, 0, 0, .5)
    border-radius: 10px
    padding: 10px
    min-height: 60px
    &.hidden
      border: 1px solid transparent
  &_body-date
    text-align: right
    margin-bottom: 5px
    color: black
  &_body-title
    justify-self: start
    white-space: nowrap
    display: block
    overflow: hidden
    text-overflow: ellipsis
    margin-bottom: 1px
    padding: 2px
    position: relative
    z-index: 10
    min-width: 100%
    &:hover
      width: max-content
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