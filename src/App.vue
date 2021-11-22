<template>
  <div id="app">
    <Calendar :events="events"/>
  </div>
</template>

<script>
import Calendar from './components/calendar';

export default {
  name: 'App',
  components: {
    Calendar
  },
  data() {
    return {
      events: [
        {id: 1, name: '12:00 - Купить хлеб', date: '2.11.2021', type: 'green'},
        {id: 2, name: '14:00 - Погулять', date: '17.11.2021', type: 'red'},
        {id: 3, name: '20:30 - Посмотреть фильм', date: '27.11.2021', type: 'orange'},
        {id: 4, name: '22:30 - Послушать музыку', date: '27.11.2021', type: 'green'},
        {id: 5, name: '22:50 - Почитать книгу', date: '27.11.2021', type: 'red'},
        {id: 6, name: '12:50 - Проверить почту', date: '12.11.2021', type: 'orange'},
        {id: 7, name: '21:00 - Спорт', date: '20.10.2021', type: 'orange'},
        {id: 8, name: '10:00 - Покодить', date: '14.12.2021', type: 'orange'},
        {id: 9, name: '7:33 - Поехать в Москву', date: '31.12.2021', type: 'green'}
      ]
    }
  }
}
</script>

<style lang="sass">
body
  background: grey
</style>
