<template>
    <div>
        <form>                    
            <div class="form-group mb-3 mt-3">
                <label>Name: </label><br>
                <input 
                    class="form-control" 
                    v-model="user.name" 
                    type="name" 
                    placeholder="eq: John Doe"></div>

            <div class="form-group mb-3">
                <label>Email address: </label><br>
                <input 
                    class="form-control" 
                    v-model="user.email" 
                    type="text" 
                    placeholder="eg: johndoe@mail.com">

                <small 
                    id="emailHelp" 
                    class="form-text text-muted">We'll never share your email with anyone else.</small></div>

            <div class="form-group mb-4">
                <label>Password: </label><br>
                <input 
                    class="form-control"
                    v-model="user.password" 
                    type="password" 
                    placeholder="min. 6 character"></div>

            <button 
                class="btn btn-success w-100"
                @click.prevent="register()">Register</button>
                
        </form>

        <hr>
        <div id="google-signin-button"></div>
    </div>
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';

export default {
    nama: 'RegisterForm',
    mounted() {
        gapi.signin2.render('google-signin-button', {
            onsuccess: this.onSignIn
        })
    },
    data () {
        return {
            user: {},
            server: 'https://safe-savannah-96382.herokuapp.com'
        }
    },
    methods: {
        onSignIn (user) {
            axios({
                method: 'POST',
                url: `${this.server}/google-login`,
                data: {
                    id_token: user.getAuthResponse().id_token
                }
            })
            .then(({ data }) => {
                console.log('user sign in');
                localStorage.access_token = data.access_token;
                localStorage.name = data.name;
                this.$emit('successLoggedIn')
                this.$emit('getName', localStorage.name)

                Swal.fire({
                    title: `Sign in successfully!`,
                    icon: 'success',
                    confirmButtonText: 'Okay'
                })
            })
            .catch(err => {
                Swal.fire({
                    title: 'Error!',
                    icon: 'error',
                    confirmButtonText: 'Okay'
                })
            })
        },  
        register() {
            axios({
                method: 'POST',
                url: `${this.server}/register`,
                data: {
                    name: this.user.name,
                    email: this.user.email,
                    password: this.user.password
                }
            })
            .then(user => {
                return axios({
                    method: 'POST',
                    url: `${this.server}/login`,
                    data: {
                        email: this.user.email,
                        password: this.user.password, 
                    },
                })
            })
            .then(({ data }) => {
                localStorage.access_token = data.access_token;
                localStorage.name = data.name
                this.$emit('successLoggedIn')
                this.$emit('getName', localStorage.name)

                Swal.fire({
                    title: `Registered successfully!`,
                    icon: 'success',
                    confirmButtonText: 'Okay'
                })
            })
            .catch(err => {
                Swal.fire({
                    title: 'Error!',
                    text: `Name, email, and password must be filled`,
                    icon: 'error',
                    confirmButtonText: 'Okay'
                })
            })
            .then(() => {
                this.user = {}
            })
        }
    }
}
</script>

<style>

</style>