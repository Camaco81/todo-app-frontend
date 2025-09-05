<template>
  <div id="app">
    <div class="container">
      <h1>Lista de Tareas</h1>
      <div class="input-section">
        <input 
          v-model="newTaskText" 
          @keyup.enter="addTask"
          placeholder="Agrega una nueva tarea..."
        />
        <button @click="addTask">Agregar</button>
      </div>

      <ul class="task-list">
        <li 
          v-for="task in tasks" 
          :key="task.id" 
          :class="{ completed: task.completed }"
          class="task-item"
        >
          <span @click="toggleTaskCompletion(task.id)" class="task-text">
            {{ task.text }}
          </span>
          <button @click="deleteTask(task.id)" class="delete-btn">
            &times;
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      tasks: [],
      newTaskText: '',
      apiBaseUrl: 'http://127.0.0.1:5000/api/tasks'
    };
  },
  created() {
    this.fetchTasks();
  },
  methods: {
    fetchTasks() {
      axios.get(this.apiBaseUrl)
        .then(response => {
          this.tasks = response.data.tasks;
        })
        .catch(error => {
          console.error("Hubo un error al obtener las tareas:", error);
        });
    },
    addTask() {
      if (this.newTaskText.trim() === '') return;
      
      axios.post(this.apiBaseUrl, { text: this.newTaskText })
        .then(() => {
          this.newTaskText = '';
          this.fetchTasks();
        })
        .catch(error => {
          console.error("Hubo un error al agregar la tarea:", error);
        });
    },
    toggleTaskCompletion(id) {
      const task = this.tasks.find(t => t.id === id);
      if (!task) return;

      axios.put(`${this.apiBaseUrl}/${id}`, { completed: !task.completed })
        .then(() => {
          this.fetchTasks();
        })
        .catch(error => {
          console.error("Hubo un error al actualizar la tarea:", error);
        });
    },
    deleteTask(id) {
      axios.delete(`${this.apiBaseUrl}/${id}`)
        .then(() => {
          this.fetchTasks();
        })
        .catch(error => {
          console.error("Hubo un error al eliminar la tarea:", error);
        });
    }
  }
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  background-color: #f4f4f4;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  margin: 0;
}

#app {
  text-align: center;
  color: #333;
}

.container {
  background: #fff;
  padding: 30px;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  width: 100%;
  max-width: 500px;
}

h1 {
  color: #555;
  margin-bottom: 20px;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.input-section input {
  flex-grow: 1;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
}

.input-section button {
  padding: 10px 15px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.input-section button:hover {
  background-color: #0056b3;
}

.task-list {
  list-style: none;
  padding: 0;
  text-align: left;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  background-color: #f9f9f9;
  border-bottom: 1px solid #eee;
  transition: all 0.3s ease;
}

.task-item:last-child {
  border-bottom: none;
}

.task-item .task-text {
  flex-grow: 1;
  cursor: pointer;
}

.task-item.completed .task-text {
  text-decoration: line-through;
  color: #888;
}

.delete-btn {
  background: transparent;
  border: none;
  color: #f00;
  font-size: 20px;
  cursor: pointer;
  padding: 0;
}

.delete-btn:hover {
  color: #c00;
}
</style>