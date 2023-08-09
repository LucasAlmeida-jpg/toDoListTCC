<template>
  <div class="container">
    <h2 class="text-center my-5">TO DO LIST</h2>
    <div class="d-flex align-items-center justify-content-between my-5">
      <div>
        <span class="img-logo"><img src="../assets/logo.png" alt="logo Vue.js"></span>
      </div>
      <div>
        <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
          ADICIONAR
        </button>
      </div>
    </div>
    <ul class="list-group rounded">

      <li v-for="(task, index) in tasks" :key="index" class="list-group-item">
        <div>
          <h3>Nomes da Tarefa</h3>
          <span v-if="index === editedTaskIndex">
            <input required v-model="task.name" class="form-control" />
          </span>
          <span v-else>{{ task.name }}</span>
        </div>
        <div>
          <h3>Status</h3>
          <span v-if="index === editedTaskIndex">
            <select v-model="task.status">
              <option value="Pendente">Pendente</option>
              <option value="Em Progresso">Em Progresso</option>
              <option value="Concluído">Concluído</option>
            </select>
          </span>
          <span v-else>{{ task.status }}</span>
        </div>
        <div>
          <h3>Nível de criticidade <span @click="toggleSortOrder"><i class="fa-solid fa-filter"></i></span></h3>
          <span v-if="index === editedTaskIndex">
            <select v-model="task.criticidade">
              <option value="Tranquilo">Tranquilo</option>
              <option value="Normal">Normal</option>
              <option value="Importante">Importante</option>
              <option value="Extremo">Extremo</option>
            </select>
          </span>
          <span v-else>{{ task.criticidade }}</span>
        </div>
        <div>
          <h3>Data</h3>
          <span v-if="index === editedTaskIndex">
            <input type="date" v-model="task.date" class="form-control" />
          </span>
          <span v-else>{{ formatDate(task.date) }}</span>
        </div>
        <div>
          <h3>Editar</h3>
          <div v-if="index === editedTaskIndex">
            <button @click="updateTask(index)">Ajustando</button>
          </div>
          <div v-else @click="openEditTaskModal(index)" data-bs-toggle="modal" data-bs-target="#exampleModal"
            class="pointer">
            <i class="fa-solid fa-pen"></i>
          </div>
        </div>
        <div>
          <h3>Excluir</h3>
          <div @click="deleteTask(index)" class="pointer">
            <i class="fa-solid fa-trash"></i>
          </div>
        </div>
      </li>
    </ul>
  </div>
  <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
      <div class="modal-content">

        <div class="modal-body">
          <div class="d-flex justify-content-between">
            <h1 class="modal-title fs-5" id="exampleModalLabel">{{ addingTask ? 'Adicionar Tarefa' : 'Editar Tarefa' }}
            </h1>

          </div>
          <div class="form-floating mb-3 my-4">
            <input v-model="modalTask.name" type="text" class="form-control" id="floatingInput" placeholder="Tarefa">
            <label for="floatingInput">Digite sua tarefa</label>
          </div>
          <div class="form-floating mb-3">
            <label for="status">Selecione o Status</label>
            <select id="status" v-model="modalTask.status">
              <option value="Pendente">Pendente</option>
              <option value="Em Progresso">Em Progresso</option>
              <option value="Concluído">Concluído</option>
            </select>
          </div>
          <div class="form-floating mb-3">
            <label for="critical">Selecione a Criticidade</label>
            <select id="critical" v-model="modalTask.criticidade">
              <option value="Tranquilo">Tranquilo</option>
              <option value="Normal">Normal</option>
              <option value="Importante">Importante</option>
              <option value="Extremo">Extremo</option>
            </select>
          </div>
          <div class="form-floating mb-3">
            <input v-model="modalTask.date" type="date" class="form-control" id="floatingPassword" placeholder="Data">
            <label>Data</label>
          </div>
          <div class="d-flex justify-content-between mb-1">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal" @click="cancelEdit">Cancelar</button>
            <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="saveModalTask">{{ addingTask ?
              'Adicionar' : 'Salvar Alterações' }}</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script>
export default {
  name: 'TodoList',
  data() {
    return {
      task: "",
      sortOrder: 'asc',
      editedTaskIndex: null,
      addingTask: true,
      tasks: [],
      modalTask: {
        name: "",
        status: "Status",
        criticidade: "Criticidade",
        date: "",
      },
    };
  },
  created() {
    const savedTasks = localStorage.getItem('tasks');
    if (savedTasks) {
      this.tasks = JSON.parse(savedTasks);
    }
  },
  methods: {
    openAddTaskModal() {
      this.modalTask = {
        name: '',
        status: 'Pendente',
        criticidade: 'Tranquilo',
        date: ''
      };
      this.addingTask = true;
    },
    openEditTaskModal(index) {
      this.modalTask = { ...this.tasks[index] };
      this.editedTaskIndex = index;
      this.addingTask = false;
    },

    saveModalTask() {
      if (!this.modalTask.name) return;

      if (this.addingTask) {
        this.tasks.push(this.modalTask);
      } else {
        this.tasks[this.editedTaskIndex] = this.modalTask;
      }

      this.modalTask = {
        name: '',
        status: 'Pendente',
        criticidade: 'Tranquilo',
        date: ''
      };
      this.editedTaskIndex = null;
      this.addingTask = true;
      this.saveTasksToLocalStorage();
    },
    editTask(index) {
      this.editedTaskIndex = index;
    },
    cancelEdit() {
      this.editedTaskIndex = null;
    },
    updateTask(index) {
      this.editedTaskIndex = null;
      this.saveTasksToLocalStorage();
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasksToLocalStorage();
    },
    updateStatus(index) {
      this.saveTasksToLocalStorage();
    },
    updateCriticidade(index) {
      this.saveTasksToLocalStorage();
    },
    openEditTaskModal(index) {
      this.modalTask = { ...this.tasks[index] };
      this.editedTaskIndex = index;
      this.addingTask = false;
    },
    submitTask() {
      if (!this.task.length) return;

      if (this.editedTaskIndex !== null) {
      } else {
        this.tasks.push({
          name: this.task,
          status: 'Escolha o Status',
          criticidade: 'Escolha a Criticidade',
          date: 'dd/mm/aaaa'
        });
      }

      this.task = "";
      this.addingTask = true;

      this.saveTasksToLocalStorage();
    },
    saveTasksToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    },
    toggleSortOrder() {
      this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc';
      this.sortTasks();
    },
    sortTasks() {
      const multiplier = this.sortOrder === 'asc' ? 1 : -1;
      this.tasks.sort((a, b) => {
        const criticidadeValues = {
          Tranquilo: 4,
          Normal: 3,
          Importante: 2,
          Extremo: 1
        };
        const criticidadeA = criticidadeValues[a.criticidade];
        const criticidadeB = criticidadeValues[b.criticidade];
        return (criticidadeA - criticidadeB) * multiplier;
      });
    },
    formatDate(value) {
      if (value) {
        const date = new Date(value);
        const day = date.getDate().toString().padStart(2, '0');
        const month = (date.getMonth() + 1).toString().padStart(2, '0'); // Mês começa de 0
        const year = date.getFullYear();
        return `${day}/${month}/${year}`;
      }
      return '';
    },
  },


};
</script>

<style>
body {
  font-family: Arial, Helvetica, sans-serif !important;
}

.pointer {
  cursor: pointer;
}

select {
  width: 100%;
  padding: 15px;
  border-radius: 6px;
  cursor: pointer;
  border: 1px solid #42b983;
}

body,
tr,
input,
select {
  background-color: #1a1a1a !important;
  color: #42b983 !important;
}

.form-control {
  border: 1px solid #42b983 !important;
}

.btn-primary, .btn-secondary {
  background: #42b983 !important;
  padding: 7px 30px !important;
  border: none !important;
  color: #1a1a1a !important;
  font-weight: bold !important;
}

.fa-pen,
.fa-trash {
  color: #42b983 !important;
}

.fa-filter {
  position: absolute;
  margin-left: 9px;
  cursor: pointer;
}

.modal-content {
  background: #242424 !important;
  color: #42b983;
}

h3 {
  font-size: 11px !important;
  text-align: center;
}

.img-logo img {
  width: 65px;
}

table th,
tr,
thead,
tbody {
  border: 1px solid #42b983 !important;
}

.list-group-item {
  display: flex !important;
  justify-content: space-between !important;
  margin: 10px;
  border-radius: 6px;
  background: #242424 !important;
  color: #42b983 !important;
  padding: 20px !important;
}
#exampleModal{
background: rgba(255, 255, 255, 0.2);
border-radius: 16px;
box-shadow: 0 4px 30px rgba(0, 0, 0, 0.1);
backdrop-filter: blur(8.4px);
-webkit-backdrop-filter: blur(8.4px);
border: 1px solid rgba(255, 255, 255, 0.3);
}
</style>
