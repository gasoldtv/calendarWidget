<template>
    <div class="c-calendar">
        <div class="c-calendar__header">
            <span class="c-arrow c-arrow--left" @click="prevMonth">&lt;</span>
            <div class="c-txt">{{currentMonthName}} {{currentYear}}</div>
            <span class="c-arrow c-arrow--right" @click="nextMonth">&gt;</span>
        </div>
        <div class="c-calendar__month">
            <div v-for="dayOfWeek in daysOfWeek" :key="dayOfWeek" class="c-dow">{{dayOfWeek}}</div>
            <div 
                v-for="day in daysArr" 
                :key="day.index" 
                @mouseover="if (day.class.includes('event')) {day.hover = true};" 
                @mouseleave="if (day.class.includes('event')) {day.hover = false};"
                @click="dayClick(day)"
                class="c-dow"
                :class="{'c-dow--event': (day.class.includes('event')), 'c-dow--pale': (day.class.includes('pale'))}"
            >
                {{day.day}}
                <div 
                    v-if="day.hover && day.class.includes('event')" 
                    class="c-dow__tooltip"
                >
                    <span 
                        v-for="eventInDay in evnetsInDayFn(day.date)" 
                        :key="eventInDay.index"
                    >
                        {{eventInDay}}
                    </span>
                </div>  
            </div>
        </div>
    </div>
    
</template>

<script>

export default {
    name: 'c-calendar',
    props: {
        eventsProp: {
            type: Array,
            requred: true,
        },
        dateInPick: {
            type: String
        }
    },
    data () {
        return {
            daysOfWeek: ['Пн','Вт','Ср','Чт','Пт','Сб','Вс'],
            monthsOfYear: ['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь'],
            daysArr: [],
            currentMonthNum: new Date().getMonth(),
            currentMonthName: '',
            currentYear: new Date().getFullYear(),
            hover: {},
            clikcedDate: {},
            daysWithEvent: [],
        }
    },
    methods: {
        getCurrentMonth() {
            this.$emit('monthDate', (this.currentMonthNum + 1), this.currentYear);
            this.currentMonthName = this.monthsOfYear[this.currentMonthNum];
        },
        daysByMounth() {
            let lastDateInMonth = new Date(this.currentYear, this.currentMonthNum + 1, 0), 
            lastDateInPrevMonth = new Date(this.currentYear, this.currentMonthNum, 0);
            let daysInMonthArr = [], 
            daysBeforeMonthArr = [], 
            daysAfterMonthArr = [];
            let i = 0, 
            j = 0, 
            k = 0;
            let formatedDate = '';
            
            this.$nextTick(() => {
                this.daysWithEvent = [];
                this.eventsProp.forEach((value) => {
                    this.daysWithEvent.push(value.date);
                });

                for (i = 0; i < lastDateInMonth.getDate(); i++) {
                    formatedDate = `${String(i + 1).padStart(2, '0')}.${String(this.currentMonthNum + 1).padStart(2, '0')}.${this.currentYear}`;
                    daysInMonthArr[i] = {
                        'day' : i + 1, 
                        'date' : formatedDate, 
                        'class' : [`${this.daysWithEvent.includes(formatedDate) ? 'event' : ''}`]
                    };
                }

                if (lastDateInMonth.getDay() !== 0) {
                    for (j = 1; j < 8 - lastDateInMonth.getDay(); j++) {
                        formatedDate = `${String(j).padStart(2, '0')}.${lastDateInMonth.getMonth() !== 11 ?  String(lastDateInMonth.getMonth() + 2).padStart(2, '0') : '01'}.${lastDateInMonth.getMonth() < 11 ? lastDateInMonth.getFullYear() : lastDateInMonth.getFullYear() + 1}`;
                        daysAfterMonthArr.push({
                            'day' : j, 
                            'date' : formatedDate, 
                            'class' : ['pale', `${this.daysWithEvent.includes(formatedDate) ? 'event' : ''}`]
                        });
                    }
                }
                
                if (lastDateInPrevMonth.getDay() !== 0) {
                    for (k = 0; k < lastDateInPrevMonth.getDay(); k++) {
                        formatedDate = `${lastDateInPrevMonth.getDate() - k}.${String(lastDateInPrevMonth.getMonth() + 1).padStart(2, '0')}.${lastDateInPrevMonth.getFullYear()}`;
                        daysBeforeMonthArr.unshift({
                            'day' : lastDateInPrevMonth.getDate() - k, 
                            'date': formatedDate, 
                            'class' : ['pale', `${this.daysWithEvent.includes(formatedDate) ? 'event' : ''}`]
                        });
                    }
                }

                this.daysArr = [...daysBeforeMonthArr, ...daysInMonthArr, ...daysAfterMonthArr];
            })
            
            
        },
        nextMonth() {
            this.currentMonthNum < 11 ? this.currentMonthNum++ : (this.currentMonthNum = 0, this.currentYear++);
            this.daysByMounth();
        },
        prevMonth() {
            this.currentMonthNum > 0 ? this.currentMonthNum-- : (this.currentMonthNum = 11, this.currentYear--);
            this.daysByMounth();
        },
        dayClick(day) {
            this.$emit('date', day.date, false);
        },
        evnetsInDayFn(date) {
            let eventsInDay = [];
            this.eventsProp.forEach((value) => {
                if (value.date === date) {
                    eventsInDay.push(value.title);
                }
            });
            
            return eventsInDay;
        }
    },
    computed() {
        
    },
    mounted() {
        if (this.dateInPick != '') {
            this.currentMonthNum = Number(this.dateInPick.slice(3, 5)) - 1;
            this.currentYear = Number(this.dateInPick.slice(6, 10));
        }
        this.getCurrentMonth();
        this.daysByMounth();
    },
    watch: {
        currentMonthNum: {
            handler(newNum) {
                this.getCurrentMonth(newNum);
            }
        }
    }
}
</script>

<style scoped>
    .c-calendar {
        width: 320px;
        color: #111214;
    }
    .c-calendar__header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 0 16px;
        height: 56px;
    }

    .c-arrow {
        height: 24px;
        width: 24px;
        display: flex;
        justify-content: center;
        align-items: center;
        font-weight: 600;
        color: #fff;
        background-color: #ed1a34;
        border-radius: 50%;
        cursor: pointer;
        -webkit-touch-callout: none;
        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none; 
    }
    .c-calendar__month {  
        height: auto;
        width: 100%;
        display: grid;
        grid-template-columns: repeat(7, 1fr);
        grid-template-rows: repeat(auto, 64px);
    }

    .c-dow {
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 20px 0px;
        cursor: pointer;
        position: relative;
    }

    .c-dow__tooltip {
        position: absolute;
        top: 50%;
        left: 44px;
        transform: translateY(-50%);
        width: max-content;
        height: auto;
        background-color: #fff;
        color: #000;
        z-index: 1;
        padding: 8px 16px;
        display: flex;
        flex-direction: column;
        border-radius: 4px;
        box-shadow: 4px 4px 8px 0px rgba(34, 60, 80, 0.2);;
    }

    .c-dow__number {
        width: 24px;
        height: 24px;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .c-dow:nth-child(7n) {
        border-left: 1px solid #e8e9eb;
        border-top: 1px solid #e8e9eb;
        border-right: 1px solid #e8e9eb;
    }

    .c-dow:not(:nth-child(7n)) {
        border-left: 1px solid #e8e9eb;
        border-top: 1px solid #e8e9eb;
    }

    .c-dow:nth-last-child(1),
    .c-dow:nth-last-child(2),
    .c-dow:nth-last-child(3),
    .c-dow:nth-last-child(4),
    .c-dow:nth-last-child(5),
    .c-dow:nth-last-child(6),
    .c-dow:nth-last-child(7) {
        border-bottom: 1px solid #e8e9eb;
    }

    .c-dow:nth-child(7n):not(.c-dow--pale),
    .c-dow:nth-child(7n - 1):not(.c-dow--pale) {
        color: #ed1a34;
    }
    
    .c-dow--pale {
        color: #9099a3;
    }

    .c-dow--event:after {
        content: '';
        position: absolute;
        width: 8px;
        height: 8px;
        background-color: #ed1a34;
        border-radius: 50%;
        bottom: 6px;
    }
</style>