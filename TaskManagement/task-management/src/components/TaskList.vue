<template>
  <div style="padding: 50px">
    <h3>Lista de tarefas</h3>
    <table>
      <thead>
        <tr>
          <th style="text-align:center">Descrição</th>
          <th style="text-align:center">Data de criação</th>
          <th style="text-align:center">Data de conclusão</th>
          <th style="text-align:center">Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in tasks" :key="task.id">
          <td>{{ task.description }}</td>
          <td style="text-align:center">{{ formatDate(task.createdAt) }}</td>
          <td style="text-align:center">{{ formatDate(task.completedAt) }}</td>
          <td style="text-align:center">
            <button @click="completeTask(task.id)" v-if="task.completedAt == null" class="btn btn-success">Concluir</button>
            <button @click="openModal(task)" class="btn btn-primary">Editar</button>
            <button @click="deleteTask(task.id)" class="btn btn-danger">Excluir</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Custom Modal -->
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h5>Editar Tarefa</h5>
          <button @click="closeModal" class="btn-close">&times;</button>
        </div>
        <div class="modal-body">
          <form>
            <div class="mb-3">
              <label for="taskDescription" class="form-label">Descrição:</label>
              <input type="text" id="taskDescription" class="form-control" v-model="currentTask.description" />
            </div>
            <div class="mb-3">
              <label for="taskCreatedAt" class="form-label">Data de Criação:</label>
              <input type="text" id="taskCreatedAt" class="form-control" :value="formatDate(currentTask.createdAt)" readonly />
            </div>
            <div class="mb-3">
              <label for="taskCompletedAt" class="form-label">Data de Conclusão:</label>
              <input type="date" id="taskCompletedAt" class="form-control" v-model="currentTask.completedAt" />
            </div>
          </form>
        </div>
        <div class="modal-footer">
          <button @click="closeModal" class="btn btn-secondary">Cancelar</button>
          <button @click="updateTask" class="btn btn-primary">Salvar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      tasks: [],
      currentTask: { description: '', createdAt: null, completedAt: null },
      showModal: false
    };
  },
  async created() {
    await this.fetchTasks();
  },
  methods: {
    async completeTask(id) {
      try {
        await axios.put(`/api/tasks/concluirTarefa/${id}`, { completedAt: new Date() });
        await this.fetchTasks();
      } catch (error) {
        console.error('Error completing task:', error);
      }
    },
    async deleteTask(id) {
      try {
        await axios.delete(`/api/tasks/${id}`);
        await this.fetchTasks();
      } catch (error) {
        console.error('Error deleting task:', error);
      }
    },
    async fetchTasks() {
      try {
        const response = await axios.get('/api/tasks');
        this.tasks = response.data;
      } catch (error) {
        console.error('Error fetching tasks:', error);
      }
    },
    formatDate(dateString) {
      if (dateString == null) {
        return '-';
      }
      const date = new Date(dateString);
      return date.toLocaleDateString();
    },
    openModal(task) {
      this.currentTask = { ...task, completedAt: task.completedAt ? this.formatDateToInput(task.completedAt) : '' };
      this.showModal = true;
    },
    formatDateToInput(dateString) {
      if (!dateString) return '';
      const date = new Date(dateString);
      const year = date.getFullYear();
      const month = String(date.getMonth() + 1).padStart(2, '0'); // Months are 0-based
      const day = String(date.getDate()).padStart(2, '0');
      return `${year}-${month}-${day}`;
    },
    closeModal() {
      this.showModal = false;
      this.currentTask = { description: '', createdAt: null, completedAt: null };
    },
    async updateTask() {
      if (this.currentTask.completedAt && new Date(this.currentTask.completedAt) < new Date(this.currentTask.createdAt)) {
        alert('A data de conclusão não pode ser menor que a data de criação.');
        return;
      }
      try {
        await axios.put(`/api/tasks/${this.currentTask.id}`, this.currentTask);
        await this.fetchTasks();
        this.closeModal();
      } catch (error) {
        console.error('Error updating task:', error);
      }
    }
  }
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  background-color: #f2f2f2;
}

button {
  margin-right: 5px;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 1000;
}

.modal-content {
  background: #fff;
  padding: 20px;
  border-radius: 8px;
  width: 50%;
  max-width: 600px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #ddd;
  padding-bottom: 10px;
}

.modal-footer {
  display: flex;
  justify-content: flex-end;
  margin-top: 20px;
}

.btn-close {
  background: none;
  border: none;
  font-size: 1.5rem;
}
</style>
