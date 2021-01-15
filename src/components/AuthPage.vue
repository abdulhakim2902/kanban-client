<template>
    <div class="auth-page mx-auto w-25 border p-4 rounded mt-5">
        <button 
            :class="{'btn-dark': currentForm === 'login'}"
            @click.prevent="changeForm('login')"
            class="btn">Login</button>
        
        <button 
            :class="{'btn-dark': currentForm === 'register'}"
            @click.prevent="changeForm('register')"
            class="btn">Registration</button>


            
        <login-form 
            v-if="currentForm === 'login'"
            @successLoggedIn="successLoggedIn"
            @getName="getName"></login-form>

        <register-form 
            v-else
            @successLoggedIn="successLoggedIn"
            @getName="getName"></register-form>
            
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
        getName(payload) {
            this.$emit('getName', payload)
        },
    },
    components: {
        LoginForm,
        RegisterForm,
    }
}
</script>

<style>

</style>