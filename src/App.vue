<template>
    <div id="app">
        <UbiCalendar v-model="selectedDates"/>
        <div v-if="selectedDates.length">
            <h1>Les dates que vous avez sélectionnées</h1>
            <p>(v-model étant implémenté, ces dates sont accessibles depuis un composant frère)</p>
            <ul>
                <li v-for="(date, index) in selectedDates" :key="index">
                    {{date | formatDate}}
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
    import UbiCalendar from './components/UbiCalendar.vue'

    import moment from 'moment';

    moment.locale('fr');

    export default {
        name: 'app',
        components: {
            UbiCalendar
        },
        filters: {
            formatDate: function (givenDate) {
                if (!givenDate) {
                    return "";
                }
                return moment(givenDate).format("LL");
            }
        },
        data: function () {
            return {
                selectedDates: []
            }
        }
    }
</script>

<style>
    #app {
        font-family: sans-serif;
        display: flex;
        flex-direction: column;
        align-items: center;
    }
</style>
