<template>
    <div>
        <navbar 
            :name="name"
            :isLoggedIn="isLoggedIn"
            @successLoggedOut="setIsLoggedIn(false)"
            @clearName="clearName"></navbar>

        <auth-page 
            v-if="isLoggedIn === false"
            @successLoggedIn="setIsLoggedIn(true)"
            @getName="getName"></auth-page>

        <home-page 
            v-else
            :tasks="tasks"
            @taskAdded="taskAdded"
            @taskUpdated="taskUpdated"
            @taskDeleted="taskDeleted"
            @taskCategoryUpdated="taskCategoryUpdated"
            ></home-page>
    
    </div>
</template>

<script>
import axios from 'axios';
import Navbar from './components/Navbar';
import AuthPage from './components/AuthPage';
import HomePage from './components/HomePage';

export default {
    name: 'App',
    data() {
        return {
            isLoggedIn: false,
            server: 'http://localhost:3000',
            tasks: {
                backlog: [],
                todo: [],
                ongoing: [],
                done: []
            },
            name: localStorage.name
        }
    },
    components: {
        Navbar,
        AuthPage,
        HomePage
    },
    methods: {
        clearName() {
            this.name = ''
        },
        getName(payload) {
            this.name = payload;
        },
        authenticate() {
            if (localStorage.access_token) {
                this.name = localStorage.name;
                this.setIsLoggedIn(true);
                this.fetchTasks();
            } else {
                this.setIsLoggedIn(false);
                this.name = ''        
            }
        },
        setIsLoggedIn(param) {
            this.isLoggedIn = param;
        },
        fetchTasks() {
            axios({
                method: 'GET',
                url: `${this.server}/tasks`,
                headers: {
                    access_token: localStorage.access_token
                }
            })
            .then(({ data }) => {
                data.forEach(task => {
                    this.tasks[task.category].push(task)
                })
            })
            .catch(err => {
                console.log(err);
            })
        },
        taskAdded(payload) {
            this.tasks[payload.category].push(payload);
        },
        taskUpdated(payload) {
            let updated = this.tasks[payload.category].map(task => {
                if (task.id === payload.id) return payload
                return task
            })

            this.tasks[payload.category] = updated
        },
        taskDeleted(payload) {
            let filtered = this.tasks[payload.category].filter(task => task.id !== payload.id);

            this.tasks[payload.category] = filtered;
        },
        taskCategoryUpdated(payload) {
            let updated = this.tasks[payload.previousCategory].filter(task => task.id !== payload.id);
            this.tasks[payload.category].push(payload);
            this.tasks[payload.previousCategory] = updated
        }
    }, 
    created() {
        this.authenticate()
    }
}
</script>

<style>

</style>