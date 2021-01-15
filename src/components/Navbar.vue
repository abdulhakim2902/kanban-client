<template>
    <nav class="container-fluid">
        <div class="nav-title">
            <span>KANBAN</span></div>
    
        <div 
            class="nav-user" 
            @click.prevent="logout()" 
            v-if="isLoggedIn === true">

            <div class="user-name">
                <span> {{ name }} </span></div>

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
            
        }
    },
    methods: {
        logout() {
            localStorage.clear();
            this.$emit('successLoggedOut')
            this.$emit('clearName')

           this.signOut()
        },

        signOut() {
            let auth2 = gapi.auth2.getAuthInstance();
                auth2.signOut()
                .then(function () {
                console.log('User signed out.');
            });
        }
    },
    
    props: ['isLoggedIn', 'name']
}
</script>

<style>

</style>