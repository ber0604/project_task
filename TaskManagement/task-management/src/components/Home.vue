<template>
  <div>
    <TaskForm @task-added="refreshTasks" />
    <TaskList ref="taskList" />
  </div>
</template>

<script>
import TaskForm from '../components/TaskForm.vue';
import TaskList from '../components/TaskList.vue';

export default {
  components: {
    TaskForm,
    TaskList
  },
  methods: {
    refreshTasks() {
      this.$refs.taskList.fetchTasks(); // Refresh the task list
    }
  }
};
</script>
