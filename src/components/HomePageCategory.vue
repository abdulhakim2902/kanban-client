<template>
    <div id="category" class="col-md-3">
        <div class="category-header bg-dark p-3">
            <span>{{ categoryName }}</span>
        </div>

        <div class="col-md-12 bg-light p-2 overflow-auto h-75">
            <task-card 
                v-for="task in categoryData"
                :task="task"
                :key="task.id"
                @taskUpdated="taskUpdated"
                @taskDeleted="taskDeleted"
                @taskCategoryUpdated="taskCategoryUpdated">    
            </task-card> 
        </div>
        
        <div class="add-btn p-3"
            v-if="categoryName === 'backlog'">
            
            <div class="show-add-btn" 
                @click.prevent="addNewTask(categoryName)">
                
                <i class="fas fa-plus"></i>
                <span>Add Task</span>
            </div>
        </div>
    </div>
</template>

<script>
import TaskCard from './HomePageCategoryCard';
import axios from 'axios';
import Swal from 'sweetalert2';
import 'regenerator-runtime/runtime'

export default {
    name: 'CategoryContainer',
    data() {
        return {
            server: 'https://safe-savannah-96382.herokuapp.com'
        }
    },
    methods: {
        taskCategoryUpdated(payload){
            this.$emit('taskCategoryUpdated', payload)
        },
        taskDeleted(payload) {
            this.$emit('taskDeleted', payload)
        },
        taskUpdated(payload) {
            this.$emit('taskUpdated', payload)
        },
        async addNewTask(categoryType) {
            const { value: task } = await Swal.fire({
                input: 'textarea',
                inputLabel: 'Add New Task',
                inputPlaceholder: 'Add task description...',
                inputAttributes: {
                    'aria-label': 'Type your message here'
                },
                inputValidator: (value) => {
                    if (!value) {
                        return 'Task description must be filled!'
                    }
                },
                showCancelButton: true
            })

            if (task) {
                axios({
                    method: "POST",
                    url: `${this.server}/tasks`,
                    headers: {
                        access_token: localStorage.access_token,
                    },
                    data: {
                        description: task,
                        category: categoryType,
                    }
                })
                .then(response => {
                    Swal.fire({
                        icon: 'success',
                        text: `${task.toUpperCase()} has been added to your ${categoryType} list`
                    })
                    this.$emit('taskAdded', response.data)
                })
                .catch(err => {
                    console.log(err);
                })
            } 
        }
    },
    components: {
        TaskCard
    },
    props: ['categoryData', 'categoryName']
}
</script>

<style>

</style>