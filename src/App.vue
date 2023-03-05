<template>
    <form @submit.prevent>
      <c-input placeholder="dd.mm.yyyy" @click="showCalendar" maxlength="10" v-model="inputDate" readonly/>
      <c-calendar v-if="showCal" :eventsProp="findedEvents" :dateInPick="inputDate" @monthDate="checkEvents" @date="inputDateFn"/>
      <c-input placeholder="Название события" v-model="eventName"/>
      <c-button @click="createEvent">{{buttonText}}</c-button>
    </form>
      
</template>

<script>
export default {
  data() {
    return {
      showCal: false,
      events: [
        {'date' : '01.02.2023', 'title' : 'День Робинзона Крузо'},
        {'date' : '01.02.2023', 'title' : 'День «Измените свой пароль»'},
        {'date' : '14.02.2023', 'title' : 'День всех влюблённых'},
        {'date' : '08.03.2023', 'title' : 'Международный женский день'},
        {'date' : '23.02.2023', 'title' : 'День защитника Отечества'},
        {'date' : '31.01.2023', 'title' : 'День защитника Отечества'},
        {'date' : '31.01.2023', 'title' : 'День защитника Отечества'},
        {'date' : '31.12.2022', 'title' : 'День защитника Отечества'},
        {'date' : '01.01.2023', 'title' : 'День защитника Отечества'},
      ],
      findedEvents: [],
      buttonText: 'Добавить событие',
      currentMonth: '',
      inputDate: '',
      eventName: '',
    }
  },
  methods: {
    showCalendar() {
      !this.showCal ? this.showCal = true : this.showCal = false;
    },
    checkEvents(month, year) {
      this.findedEvents = [];
      const formatedDate = (valMonth, valYear) => {
        return String(valMonth).padStart(2, '0') + '.' + String(valYear);
      }
      this.events.forEach((value) => {
        let eventMonthYear = value.date.slice(3, 10);
        switch (String(month).padStart(2, '0')) {
          case '01':
            if (eventMonthYear === formatedDate(1, year) || eventMonthYear === formatedDate(month + 1, year) || eventMonthYear === formatedDate(12, year - 1)) {
              this.findedEvents.push(value);
            }
            break;
          case '12':
            if (eventMonthYear === formatedDate(12, year) || eventMonthYear === formatedDate(1, year + 1) || eventMonthYear === formatedDate(11, year)) {
              this.findedEvents.push(value);
            }
            break;
          default:
            if (eventMonthYear === formatedDate(month, year) || eventMonthYear === formatedDate(month + 1, year) || eventMonthYear === formatedDate(month - 1, year)) {
              this.findedEvents.push(value);
            }
            break;
        }
      });
      console.log(this.findedEvents);
    },
    inputDateFn(date, bool) {
      this.inputDate = date;
      this.showCal = bool;
    },
    createEvent() {
      if (this.inputDate != '' && this.eventName != '') {
        this.events.push({'date' : this.inputDate, 'title' : this.eventName});
        this.inputDate = '';
        this.eventName = '';
      }else{
        this.buttonText = 'Заполните все поля';
        setTimeout(() => {
          this.buttonText = 'Добавить событие';
        },
        1000)
      }
    }
  }
}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Montserrat';
  }

  #app {
    padding: 20px;
  }

  form {
    display: flex;
    flex-direction: column;
    row-gap: 16px;
  }
</style>
