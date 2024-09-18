<template>
  <div style="padding: 50px">
    <h3>Adicionar tarefa</h3>
    <form @submit.prevent="addTask">
      <div class="mb-3">
        <label for="taskDescription" class="form-label">Descrição:</label>
        <input v-model="taskDescription" class="form-control" placeholder="Descrição da tarefa" required />
      </div>
      <button type="submit" class="btn btn-primary">Adicionar</button>
    </form>
  </div>
</template>

<script>
import axios from 'axios';
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';

export default {
  data() {
    return {
      taskDescription: ''
    };
  },
  methods: {
    async addTask() {
      try {
        await axios.post('/api/tasks', { description: this.taskDescription });
        this.taskDescription = '';
        this.$emit('task-added');
      } catch (error) {
        console.error('Error adding task:', error);
      }
    }
  }
};
</script>