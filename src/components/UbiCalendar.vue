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
                <select>
                    <option>actions</option>
                </select>
            </label>
        </div>
        <div class="weekdays">
            <div v-for="(weekday, index) in weekdays" :key="index">
                {{weekday}}
            </div>
        </div>
        <div class="dates">
            <div v-for="(date, index) in dates" :key="index">
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
                weekdays: moment.weekdaysMin(true),
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
        }
    }
</script>

<style scoped>
    .calendar {
        width: 400px;
        height: 400px;
        border: 1px solid;
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


</style>
