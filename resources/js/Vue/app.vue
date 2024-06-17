<template>
  <!-- Componente principal de la aplicación -->
  <div id="app">
    <!-- Título de la aplicación -->
    <h1>Lista de tareas</h1>
    <!-- Grupo de entrada para agregar nuevas tareas -->
    <div class="input-group">
      <!-- Entrada de texto para la nueva tarea -->
      <input type="text" v-model="newTask" placeholder="Agregar una nueva tarea" @keyup.enter="addTask">
      <!-- Botón para agregar la nueva tarea -->
      <button @click="addTask">Agregar</button>
    </div>
    <!-- Grupo de entrada para filtrar tareas -->
    <div class="input-group">
      <!-- Selector para filtrar tareas por estado -->
      <select v-model="filter" class="task-filter">
        <option value="">Todos</option>
        <option value="pending">Pendiente</option>
        <option value="in progress">En progreso</option>
        <option value="completed">Completado</option>
      </select>
    </div>
    <!-- Lista de tareas -->
    <div v-for="(item, index) in filteredItems" :key="index" class="task">
      <!-- Nombre de la tarea -->
      <div class="task-name">{{ item.title }}</div>
      <!-- Selector para cambiar el estado de la tarea -->
      <select v-model="item.status" class="task-status" @change="updateStatus(item)">
        <option value="pending">Pendiente</option>
        <option value="in progress">En progreso</option>
        <option value="completed">Completado</option>
      </select>
      <!-- Botón para eliminar la tarea -->
      <button @click="deleteTask(index)" class="task-button delete">Eliminar</button>
      <!-- Botón para mover la tarea hacia arriba -->
      <button @click="moveTask(index, -1)" class="task-button move">Subir</button>
      <!-- Botón para mover la tarea hacia abajo -->
      <button @click="moveTask(index, 1)" class="task-button move" :class="{ 'completed': item.status === 'completed' }">Bajar</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      newTask: '', // Almacena la nueva tarea ingresada por el usuario
      items: [], // Almacena la lista de tareas
      filter: '' // Almacena el filtro de estado seleccionado
    }
  },
  computed: {
    // Devuelve la lista de tareas filtrada por estado
    filteredItems() {
      if (this.filter) {
        return this.items.filter(item => item.status === this.filter);
      } else {
        return this.items;
      }
    }
  },
  methods: {
    // Agrega una nueva tarea a la lista
    addTask() {
      if (this.newTask) {
        const item = { title: this.newTask, order: this.items.length, status: 'pending' };
        axios.post('/api/item/store', { item })
          .then(response => {
            this.items.push(response.data);
            this.newTask = '';
          })
          .catch(error => {
            console.error(error);
          });
      }
    },
    // Elimina una tarea de la lista
    deleteTask(index) {
      const item = this.items[index];
      axios.delete(`/api/item/${item.id}`)
        .then(() => {
          this.items.splice(index, 1);
        })
        .catch(error => {
          console.error(error);
        });
    },
    // Mueve una tarea hacia arriba o hacia abajo en la lista
    moveTask(index, direction) {
      const newIndex = index + direction;
      if (newIndex >= 0 && newIndex < this.items.length) {
        const item = this.items.splice(index, 1)[0];
        this.items.splice(newIndex, 0, item);
        item.order = newIndex;
        axios.put(`/api/item/${item.id}`, { item })
          .catch(error => {
            console.error(error);
          });
      }
    },
    // Actualiza el estado de una tarea
    updateStatus(item) {
      axios.put(`/api/item/${item.id}`, { item })
        .catch(error => {
          console.error(error);
        });
    }
  },
  // Al crear el componente, obtiene la lista de tareas del servidor
  created() {
    axios.get('/api/items')
      .then(response => {
        this.items = response.data;
      })
      .catch(error => {
        console.error(error);
      });
  }
}
</script>
  
<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;500&display=swap');

body {
  background-color: #f2f2f2;
}

#app {
  font-family: 'Poppins', sans-serif;
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  color: #333;
}

h1 {
  text-align: center;
  color: #333;
}

.input-group {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.input-group input {
  flex-grow: 1;
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
}

.input-group button {
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  background-color: #007BFF;
  color: white;
  cursor: pointer;
}

.task {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.task-name {
  flex-grow: 1;
  margin-right: 10px;
}

.task-status {
  margin-right: 10px;
}

.task-button {
  margin-right: 10px;
  padding: 5px 10px;
  border: none;
  border-radius: 8px;
  color: white;
  cursor: pointer;
}

.task-button.delete {
  background-color: #dc3545;
}

.task-button.move {
  background-color: #007BFF;
}

.task-button.move.completed {
  background-color: #28a745;
}
</style>