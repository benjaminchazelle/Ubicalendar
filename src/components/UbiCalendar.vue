<template>
    <div class="calendar">
        <div class="header">
            <label>
                <select v-model="currentMonth">
                    <option v-for="(month, index) in months" :key="index" :value="index">{{month}}</option>
                </select>
            </label>
            <label>
                <select v-model="currentYear">
                    <option v-for="(year, index) in years" :key="index" :value="year">{{year}}</option>
                </select>
            </label>
            <label>
                <select v-model="quickSelection" @change="quickSelect()">
                    <option value="">-- Sélection rapide --</option>
                    <option v-for="(weekday, index) in weekdays" :key="index" :value="index" @click="">
                        Tous les {{weekday}} du mois {{index}}
                    </option>
                </select>
            </label>
        </div>
        <div class="weekdays">
            <div v-for="(weekdayMin, index) in weekdaysMin" :key="index">
                {{weekdayMin}}
            </div>
        </div>
        <div class="dates">
            <div v-for="(date, index) in dates" :key="index" :class="{selected: isSelectedDate(date)}"  @click="toggleDateSelection($event, date)" @mouseenter="toggleDateSelection($event, date)">
                {{date | dayOfMonth}}
            </div>
        </div>
    </div>
</template>

<script>

    import moment from 'moment';

    moment.locale('fr');

    export default {
        name: 'UbiCalendar',
        props: {},
        filters: {
            dayOfMonth: function (givenDate) {
                return moment(givenDate).format('D');
            }
        },
        data: function () {
            return {
                currentMonth: 9, //TODO: rendre dynamique
                currentYear: 2019,

                months: moment.months(),
                weekdays: moment.weekdays(true),
                weekdaysMin: moment.weekdaysMin(true),

                selectedDates: [],

                quickSelection: ""
            }
        },
        computed: {
            years: function () {
                let years = [];
                let from = new Date().getFullYear() - 10;
                let to = new Date().getFullYear() + 10;
                for (let year = from; year <= to; year++) {
                    years.push(year);
                }
                return years;
            },
            dates: function () {
                let timeReference = new Date(this.currentYear, this.currentMonth);

                let from = moment(timeReference).startOf('month').startOf('week');
                let to = moment(timeReference).endOf('month').endOf('week');

                let dates = [];

                for (let currentDate = from; currentDate.isBefore(to) || currentDate.isSame(to); currentDate.add(1, 'days')) {
                    dates.push(currentDate.toDate());
                }

                return dates;
            }
        },
        methods: {
            toggleDateSelection: function ($event, givenDate) {

                if($event.type === "mouseenter" && $event.buttons !== 1) {
                    return;
                }

                let index = this.selectedDates.findIndex((date) => moment(date).isSame(givenDate));

                if(index === -1) {
                    this.selectedDates.push(givenDate);
                } else {
                    this.selectedDates.splice(index, 1);
                }
            },
            isSelectedDate: function (givenDate) {
                return this.selectedDates.findIndex((date) => moment(date).isSame(givenDate)) !== -1;
            },
            quickSelect: function () {

                if(this.quickSelection === "") {
                    return;
                }

                let timeReference = new Date(this.currentYear, this.currentMonth);

                let from = moment(timeReference).startOf('month');
                let to = moment(timeReference).endOf('month');

                for (let currentDate = from; currentDate.isBefore(to) || currentDate.isSame(to); currentDate.add(1, 'days')) {
                    let dayIndex = (this.quickSelection+1)%7;
                    if(currentDate.toDate().getDay() === dayIndex && this.selectedDates.findIndex((date) => moment(date).isSame(currentDate)) === -1) {
                        this.selectedDates.push(currentDate.toDate());
                    }
                }
            }

        }
    }
</script>

<style scoped>
    .calendar {
        width: 400px;
        height: 400px;
        border: 1px solid;
        user-select: none;
    }

    .header {
        display: flex;
        width: 100%;
    }

    .header label {
        flex: 1;
        display: flex;
    }

    .header select {
        flex: 1;
        height: 40px;
        line-height: 40px;
    }

    .weekdays, .dates {
        display: flex;
    }

    .weekdays div, .dates div {
        width: 14.28%;
        text-align: center;
        height: 40px;
        line-height: 40px;
    }

    .dates {
        flex-wrap: wrap;
    }

    .dates .selected {
        background: deepskyblue;
        color: white;
    }


</style>
