<template>
    <div class="auth-page mx-auto w-25 border p-4 rounded mt-5">
        <button class="btn"
            :class="{'btn-dark': currentForm === 'login'}"
            @click.prevent="changeForm('login')">
            Login
        </button>
        
        <button class="btn"
            :class="{'btn-dark': currentForm === 'register'}"
            @click.prevent="changeForm('register')">
            Registration
        </button>
            
        <login-form 
            v-if="currentForm === 'login'"
            @successLoggedIn="successLoggedIn"
            @setName="setName">
        </login-form>

        <register-form 
            v-else
            @successLoggedIn="successLoggedIn"
            @setName="setName">
        </register-form>    
    </div>
</template>

<script>
import LoginForm from './AuthPageLoginForm';
import RegisterForm from './AuthPageRegisterForm';

export default {
    name: 'AuthPage',
    data() {
        return {
            currentForm: 'login',
        }
    },
    methods: {
        changeForm(formType) {
            this.currentForm = formType
        },
        successLoggedIn() {
            this.$emit('successLoggedIn')
        },
        setName(payload) {
            this.$emit('setName', payload)
        }
    },
    components: {
        LoginForm,
        RegisterForm,
    }
}
</script>

<style>

</style>