<template>
  <div class="container mx-auto mt-5 mb-5 row">
    <category-container 
      v-for="(categoryData, categoryName) in tasks"
      :categoryData="categoryData" 
      :categoryName="categoryName"
      :key="categoryName"
      @taskAdded="taskAdded"
      @taskUpdated="taskUpdated"
      @taskDeleted="taskDeleted"
      @taskCategoryUpdated="taskCategoryUpdated"
      ></category-container>
  </div>
</template>

<script>
import CategoryContainer from './HomePageCategory';
export default {
    name: 'HomePage',
    data() {
        return {}
    },
    methods: {
      taskCategoryUpdated(payload){
        this.$emit('taskCategoryUpdated', payload)
      },
      taskAdded(payload) {
        this.$emit('taskAdded', payload)
      },
      taskUpdated(payload) {
        this.$emit('taskUpdated', payload)
      },
      taskDeleted(payload) {
        this.$emit('taskDeleted', payload)
      }
    },
    components: {
      CategoryContainer
    },
    props: ['tasks']
}
</script>

<style>

</style>