<template>
    <component 
        :is="screens[page]"
        @pageLoad="loadPage"
    />
</template>

<script>
    import LoginScreen from './components/LoginScreen.vue'
    import MainScreen from './components/MainScreen.vue'

    export default {
        name: 'App',
        components: {
            LoginScreen,
            MainScreen,
        },

        methods: {
            loadPage(page) {
                this.page = page
            },
        },

        watch: {
            page(val) {localStorage.page = JSON.stringify(val);}
        },

        mounted() {
            if (localStorage.page) {this.page = parseInt(localStorage.page);}
        },

        data() {
            return {
                screens: ['LoginScreen', 'MainScreen'],
                page: 0
            }
        }
    }
</script>

<style lang="scss">
    @import "./styles/variables";
    @import "./styles/global";
    @import url('https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300;0,400;0,500;0,600;0,700;0,800;1,300;1,400;1,500;1,600;1,700;1,800&display=swap');
</style>
  