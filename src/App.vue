<template>
    <div id="app">
        <UbiCalendar/>
    </div>
</template>

<script>
    import UbiCalendar from './components/UbiCalendar.vue'

    export default {
        name: 'app',
        components: {
            UbiCalendar
        }
    }
</script>

<style>
    #app {
        font-family: sans-serif;
        display: flex;
        justify-content: center;
    }
</style>
