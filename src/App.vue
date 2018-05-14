<template>
    <div id="app">
        <div class="container">
            <router-view/>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'App',
        updated() {
            console.log('app updated')
            this.checkCurrentLogin()
        },
        created() {
            console.log('app created')
            this.checkCurrentLogin()
        },
        methods:{
            checkCurrentLogin(){
                if (!localStorage.token && this.$route.path !== '/' && this.$route.path !== '/register') {
                    this.$router.push('/?redirect=' + this.$route.path)
                }
            }
        }
    }
</script>

<style>
</style>
