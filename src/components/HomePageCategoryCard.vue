<template>
    <div class="card mb-3 mt-2">
        <div class="card-body">
            <h5 class="card-title mb-5">{{ task.description }}</h5>
            <h6 class="card-subtitle text-muted mb-1">By: {{ task.name }}</h6>
            <h6 class="card-subtitle text-muted">{{ task.updatedAt }}</h6>
            <!-- <h6>{{ task.category }}</h6> -->
            <div class="card-icon mt-2">
                <i v-if="task.category !== 'backlog'" class="previous fas fa-arrow-circle-left"
                   @click.prevent="editCategoryConfirmation( task, 'prev' )"></i>

                <i id="pencil" 
                    class="fas fa-pencil-alt" 
                    @click.prevent="editDescriptionConfirmation( task.id )"></i>

                <i id="trash"
                    class="fas fa-trash"
                    @click.prevent="deleteTaskConfirmation( task.id )"></i>

                <i class="next fas fa-arrow-circle-right"
                   v-if="task.category !== 'done'"
                   @click.prevent="editCategoryConfirmation( task, 'next' )" 
                   ></i>
            </div>
        </div>
    </div>    
</template>

<script>
import axios from 'axios';
import Swal from 'sweetalert2';
import 'regenerator-runtime/runtime'

export default {
    name: 'TaskCard',
    data() {
        return {
            server: 'https://safe-savannah-96382.herokuapp.com',
            name: localStorage.name
        }
    },
    methods: {
        editDescriptionConfirmation(taskId) {
            axios({
                method: 'GET',
                url: `${this.server}/tasks/${taskId}`,
                headers: {
                    access_token: localStorage.access_token
                }
            })
            .then(({data}) => {
                this.updateDescription(data.id, data.description)
            })
            .catch(err => {
                console.log(err);
                Swal.fire({
                    icon: 'error',
                    title: 'No access!'
                })
            })
        },
        deleteTaskConfirmation(taskId){
           axios({
                method: 'GET',
                url: `${this.server}/tasks/${taskId}`,
                headers: {
                    access_token: localStorage.access_token
                }
            })
            .then(task => {
                return Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                })
                
            })
            .then((result) => {
                if (result.isConfirmed) {
                    this.deleteTask(taskId)                          
                }
            })
            .catch(err => {
                console.log(err);
                Swal.fire({
                    icon: 'error',
                    title: 'No access!'
                })
            }) 
        },
        deleteTask(taskId) {
            axios({
                method: "DELETE",
                url: `${this.server}/tasks/${taskId}`,
                headers: {
                    access_token: localStorage.access_token
                }
            })
            .then(response => {
                Swal.fire(
                    'Deleted!',
                    'Your file has been deleted.',
                    'success'
                )
                this.$emit('taskDeleted', response.data)
            })
            .catch(err => {
                console.log(err);
            })
        },
        async updateDescription(taskId, taskDescription) {
            const { value: task } = await Swal.fire({
                input: 'textarea',
                inputLabel: 'Edit Task',
                inputValue: taskDescription,
                inputPlaceholder: 'Add task description...',
                inputAttributes: {
                    'aria-label': 'Type your message here'
                },
                inputValidator(value) {
                    if (!value) {
                        return 'Task description must be filled!'
                    }
                },
                showCancelButton: true
            })

            if (task) {
                axios({
                    method: 'PATCH',
                    url: `${this.server}/tasks/description/${taskId}`,
                    headers: {
                        access_token: localStorage.access_token,
                    },
                    data: {
                        description: task
                    }
                })
                .then(response => {
                    Swal.fire({
                        icon: 'success',
                        text: `Task has been updated`
                    })
                    this.$emit('taskUpdated', response.data)
                })
                .catch(err => {
                    console.log(err);
                })
            }
        },
        editCategoryConfirmation(task, state) {
            axios({
                method: 'GET',
                url: `${this.server}/tasks/${task.id}`,
                headers: {
                    access_token: localStorage.access_token
                }
            })
            .then(({data}) => {
                let category = task.category;

                if (state === 'prev') {
                    switch(category) {
                        case 'todo':
                            category = 'backlog'
                        break;

                        case 'ongoing':
                            category = 'todo'
                        break;

                        case 'done':
                            category = 'ongoing'
                        break;
                    }
                } else if (state === 'next') {
                    switch(category) {
                        case 'backlog':
                            category = 'todo'
                        break;

                        case 'todo':
                            category = 'ongoing'
                        break;

                        case 'ongoing':
                            category = 'done'
                        break;
                    }
                }

                this.updatedCategory(data, category)
            })
            .catch(err => {
                console.log(err);
                Swal.fire({
                    icon: 'error',
                    title: 'No access!'
                })
            })
        },
        updatedCategory(task, category) {
            axios({
                method: 'PATCH',
                url: `${this.server}/tasks/category/${task.id}`,
                headers: {
                    access_token: localStorage.access_token
                },
                data: {
                    category
                }
            })
            .then(({data}) => {
                this.$emit('taskCategoryUpdated', {
                    category: data.category,
                    description: data.description,
                    id: data.id,
                    updatedAt: data.updatedAt,
                    userId: data.userId,
                    name: data.name,
                    previousCategory: task.category
                })
            })
            .catch(err => {
                console.log(err);
            })        
        }
    },
    props: ['task']
}
</script>

<style>

</style>