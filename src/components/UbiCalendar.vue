<template>
    <div class="calendar">
        <div class="header">
            <label>
                <select>
                    <option>mois</option>
                </select>
                {{ currentMonth }}
            </label>
            <label>
                <select>
                    <option>années</option>
                </select>
            </label>
            <label>
                <select>
                    <option>actions</option>
                </select>
            </label>
        </div>
        <div class="weekdays">
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
            <div>Lu</div>
        </div>
        <div class="dates">
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
            <div>1</div>
        </div>
    </div>
</template>

<script>

    import moment from 'moment';

    moment.locale('fr');

    export default {
        name: 'UbiCalendar',
        props: {},
        data: function () {
            return {
                currentMonth: 9,
                currentYear: 2019
            }
        }
    }
</script>

<style scoped>
    .calendar {
        width:400px;
        height:400px;
        border:1px solid;
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
        height:40px;
        line-height:40px;
    }

    .weekdays, .dates {
        display: flex;
    }

    .weekdays div, .dates div {
        width: 14.28%;
        text-align: center;
        height:40px;
        line-height:40px;
    }

    .dates {
        flex-wrap: wrap;
    }


</style>
