<template>
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
</template>

<script>
import Swal from 'sweetalert2';
import axios from 'axios';

export default {
    nama: 'RegisterForm',
    data () {
        return {
            user: {},
            server: 'http://localhost:3000'
        }
    },
    methods: {
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
                this.$emit('successLoggedIn')

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
        }
    }
}
</script>

<style>

</style>