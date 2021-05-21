<template>
  <AddTask v-if="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import axios from 'axios';

import Tasks from '../components/Tasks';
import AddTask from '../components/AddTask';
export default {
  name: 'Home',
  props: {
    showAddTask: Boolean,
  },
  components: {
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async fetchTasks() {
      const res = await axios.get('/api/tasks');
      return res.data;
    },
    async fetchTask(id) {
      const res = await axios.get(`api/tasks/${id}`);
      return res.data;
    },
    async addTask(task) {
      const res = await axios.post('/api/tasks', task);
      this.tasks = [...this.tasks, res.data];
    },
    async deleteTask(id) {
      if (confirm('Are you sure?')) {
        const res = await axios.delete(`/api/tasks/${id}`);
        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert('Error deleting task');
      }
    },
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id);
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await axios.put(`/api/tasks/${id}`, updTask);

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: res.data.reminder } : task
      );
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>
