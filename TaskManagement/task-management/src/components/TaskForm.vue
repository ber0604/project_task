<template>
  <div>
    <h2>Add Task</h2>
    <form @submit.prevent="addTask">
      <input v-model="taskDescription" placeholder="Task description" required />
      <button type="submit">Add Task</button>
    </form>
  </div>
</template>

<script>
import axios from '../axios';

export default {
  data() {
    return {
      taskDescription: ''
    };
  },
  methods: {
    async addTask() {
      try {
        await axios.post('/tasks', { description: this.taskDescription });
        this.taskDescription = ''; // Clear input after successful request
        this.$emit('task-added'); // Notify parent component
      } catch (error) {
        console.error('Error adding task:', error);
      }
    }
  }
};
</script>