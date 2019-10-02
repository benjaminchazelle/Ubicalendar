<template>
    <div class="calendar">
        <div class="header">
            <label>
                <select v-model="currentMonth">
                    <option v-for="(month, index) in months" :key="index" :value="index">
                        {{month | capitalize}}
                    </option>
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
                    <option value="reset">Tout déselectionner</option>
                    <option v-for="(weekday, index) in weekdays" :key="index" :value="index">
                        Tous les {{weekday}}s du mois
                    </option>
                </select>
            </label>
        </div>
        <div class="weekdays">
            <div v-for="(weekdayMin, index) in weekdaysMin" :key="index">
                {{weekdayMin | capitalize}}
            </div>
        </div>
        <div class="dates">
            <div v-for="(date, index) in dates" :key="index"
                 :class="{selected: isSelectedDate(date), out: isOutOfMonth(date)}"
                 @click="toggleDateSelection($event, date)" @mouseenter="toggleDateSelection($event, date)">
                {{date | dayOfMonth}}<span v-if="isOutOfMonth(date)">/{{date | monthNumber}}</span>
            </div>
        </div>
    </div>
</template>

<script>

    import moment from 'moment';

    moment.locale('fr');

    export default {
        name: 'UbiCalendar',
        props: {
            value: Array,
        },
        filters: {
            dayOfMonth: function (givenDate) {
                if (!givenDate) return '';
                return moment(givenDate).format('D');
            },
            monthNumber: function (givenDate) {
                if (!givenDate) return '';
                return moment(givenDate).format('M');
            },
            capitalize: function (value) {
                if (!value) return '';
                value = value.toString();
                return value.charAt(0).toUpperCase() + value.slice(1);
            }
        },
        data: function () {
            return {
                currentMonth: new Date().getMonth(),
                currentYear: new Date().getFullYear(),

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

                if ($event.type === "mouseenter" && $event.buttons !== 1) {
                    return;
                }

                let index = this.selectedDates.findIndex((date) => moment(date).isSame(givenDate));

                if (index === -1) {
                    this.selectedDates.push(givenDate);
                } else {
                    this.selectedDates.splice(index, 1);
                }
            },
            isSelectedDate: function (givenDate) {
                return this.selectedDates.findIndex((date) => moment(date).isSame(givenDate)) !== -1;
            },
            isOutOfMonth: function (givenDate) {
                let timeReference = new Date(this.currentYear, this.currentMonth);

                let from = moment(timeReference).startOf('month');
                let to = moment(timeReference).endOf('month');

                return !moment(givenDate).isBetween(from, to, null, "[]");
            },
            quickSelect: function () {

                if (this.quickSelection === "") {
                    return;
                }

                if (this.quickSelection === "reset") {
                    this.selectedDates = [];
                    this.quickSelection = "";
                    return;
                }

                let timeReference = new Date(this.currentYear, this.currentMonth);

                let from = moment(timeReference).startOf('month');
                let to = moment(timeReference).endOf('month');

                for (let currentDate = from; currentDate.isBefore(to) || currentDate.isSame(to); currentDate.add(1, 'days')) {
                    let dayIndex = (this.quickSelection + 1) % 7;
                    if (currentDate.toDate().getDay() === dayIndex && this.selectedDates.findIndex((date) => moment(date).isSame(currentDate)) === -1) {
                        this.selectedDates.push(currentDate.toDate());
                    }
                }

                this.quickSelection = "";
            }

        },
        watch: {
            selectedDates: function () {
                this.$emit('input', this.selectedDates);
            }
        }
    }
</script>

<style scoped>
    .calendar {
        margin-top: 40px;
        width: 400px;
        user-select: none;
        border-radius: 5px;
        border: 1px solid lightgray;
        box-shadow: 0 2px 180px 0 rgba(0, 0, 0, .2);
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
        border: 0;
        border-bottom: 1px solid #ddd;
    }

    .header label:nth-child(2) select {
        border-left: 1px solid #ddd;
        border-right: 1px solid #ddd;

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

    .weekdays {
        font-weight: bold;
        color: #222;
    }

    .dates {
        flex-wrap: wrap;
    }

    .dates div {
        transition: 0.2s background;
    }

    .dates div:hover {
        background: #ccc;
    }

    .dates .selected {
        background: deepskyblue;
        color: white;
    }

    .dates .out {
        color: #bbb;
    }

    .dates .selected.out {
        color: white;
    }


</style>
