<template>
    <div>
        <form>
            <div class="form-group mb-3 mt-3">
                <label>Email address: </label><br>
                <input class="form-control"  
                    type="text" 
                    placeholder="johndoe@mail.com"
                    v-model="user.email">

                <small id="emailHelp" class="form-text text-muted">
                    We'll never share your email with anyone else.
                </small>
            </div>

            <div class="form-group mb-4">
                <label>Password: </label><br>
                <input class="form-control" 
                    type="password" 
                    placeholder="******"
                    v-model="user.password">
                </div>

            <button class="btn btn-primary w-100"
                @click.prevent="login()">
                Login
            </button>
        
        </form>  
    </div>
    
</template>

<script>
import Swal from 'sweetalert2'
import axios from 'axios';

export default {
    name: 'LoginForm',
    data () {
        return {
            user: {},
            server: 'https://safe-savannah-96382.herokuapp.com',
        }
    },
    methods: {
        login() {
            axios({
                method: 'POST',
                url: `${this.server}/login`,
                data: {
                    email: this.user.email,
                    password: this.user.password, 
                },
            })
            .then(({ data }) => {
                localStorage.access_token = data.access_token;
                localStorage.name = data.name;
                this.$emit('successLoggedIn')
                this.$emit('setName', localStorage.name)

                Swal.fire({
                    title: `Login successful!`,
                    icon: 'success',
                    confirmButtonText: 'Okay'
                })
            })
            .catch(err => {
                Swal.fire({
                    title: 'Error!',
                    text: `Invalid email / password`,
                    icon: 'error',
                    confirmButtonText: 'Okay'
                })
            })
            .then(() => {
                this.user = {}
            })
        },
    }
}
</script>

<style>

</style>