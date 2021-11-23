<template>
  <div id="app">
    <Calendar :events="events"/>
  </div>
</template>

<script>
import moment from "moment";
moment.locale('ru');
import Calendar from './components/calendar';

export default {
  name: 'App',
  components: {
    Calendar
  },
  data() {
    return {
      events: [],
    }
  },
  created() {
    const currYear = moment().year()
    const currMonth = moment().month()
    let prevMonth
    let nextMonth
    let prevYear
    let nextYear
    if(currMonth === 0) {
      prevMonth = 11
      nextMonth = 1
      prevYear = currYear - 1
      nextYear = currYear
    } else if (currMonth === 11) {
      prevMonth = 10
      nextMonth = 0
      prevYear = currYear
      nextYear = currYear + 1
    } else if(currMonth > 0 && currMonth < 11) {
      prevMonth = currMonth - 1
      nextMonth = currMonth + 1
      prevYear = currYear
      nextYear = currYear
    }
    this.events = [
      {id: 1, name: '12:00 - Купить хлеб', date: moment({year: currYear, month: currMonth, day: 2}).format('L'), type: 'green'},
      {id: 2, name: '14:00 - Погулять', date: moment({year: currYear, month: currMonth, day: 17}).format('L'), type: 'red'},
      {id: 3, name: '20:30 - Посмотреть фильм', date: moment({year: currYear, month: currMonth, day: 27}).format('L'), type: 'orange'},
      {id: 4, name: '22:30 - Послушать музыку', date: moment({year: currYear, month: currMonth, day: 27}).format('L'), type: 'green'},
      {id: 5, name: '22:50 - Почитать книгу', date: moment({year: currYear, month: currMonth, day: 27}).format('L'), type: 'red'},
      {id: 6, name: '12:50 - Проверить почту', date: moment({year: currYear, month: currMonth, day: 12}).format('L'), type: 'orange'},
      {id: 7, name: '21:00 - Спорт', date: moment({year: prevYear, month: prevMonth, day: 20}).format('L'), type: 'orange'},
      {id: 8, name: '10:00 - Покодить', date: moment({year: nextYear, month: nextMonth, day: 14}).format('L'), type: 'orange'},
      {id: 9, name: '07:33 - Поехать в Москву', date: moment({year: nextYear, month: nextMonth, day: 24}).format('L'), type: 'green'}
    ]
  }
}
</script>

<style lang="sass">
body
  background: grey
</style>
