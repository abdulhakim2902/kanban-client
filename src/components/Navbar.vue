<template>
    <nav class="container-fluid">
        <div class="nav-title">
            <span>KANBAN</span></div>
    
        <div 
            class="nav-user" 
            @click.prevent="logout()" 
            v-if="isLoggedIn === true">

            <div class="user-name">
                <span> {{ userName }} </span></div>

            <div 
                class="user-img">
                <img 
                    src="../assets/img/user.png" 
                    width="35" 
                    height="35"></div></div>  
    </nav>
</template>

<script>
export default {
    data() {
        return {
            userName: ''
        }
    },
    methods: {
        logout() {
            localStorage.clear();
            this.$emit('successLoggedOut', false)
        },
        getName() {
            this.userName = localStorage.name
        }
    },
    props: ['isLoggedIn']
}
</script>

<style>

</style>