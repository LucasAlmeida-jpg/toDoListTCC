<template>
  <div class="container">
    <h2 class="text-center my-5">TO DO LIST</h2> <br>
    <span class="img-logo"><img src="../assets/logo.png" alt="logo Vue.js"></span>
    <div class="d-flex justify-content-center text-center">
      <button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
        Adicionar tarefa
      </button>
    </div>
    <table class="table text-center table-bordered mt-5 rounded">
      <thead>
        <tr>
          <th scope="col-md">Tarefa</th>
          <th scope="col-md">Status</th>
          <th scope="col-md">Criticidade
            <span @click="toggleSortOrder" class="pointer">
              <i class="fa-solid fa-filter"></i>
            </span>
          </th>
          <th scope="col-md">Data</th>
          <th scope="col-md">Editar</th>
          <th scope="col-md">Deletar</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span v-if="index === editedTaskIndex">
              <input v-model="task.name" class="form-control" />
            </span>
            <span v-else>{{ task.name }}</span>
          </td>
          <td>
            <span v-if="index === editedTaskIndex">
              <select v-model="task.status">
                <option value="Pendente">Pendente</option>
                <option value="Em Progresso">Em Progresso</option>
                <option value="Concluído">Concluído</option>
              </select>
            </span>
            <span v-else>{{ task.status }}</span>
          </td>
          <td>
            <span v-if="index === editedTaskIndex">
              <select v-model="task.criticidade">
                <option value="Tranquilo">Tranquilo</option>
                <option value="Normal">Normal</option>
                <option value="Importante">Importante</option>
                <option value="Extremo">Extremo</option>
              </select>
            </span>
            <span v-else>{{ task.criticidade }}</span>
          </td>
          <td>
            <span v-if="index === editedTaskIndex">
              <input type="date" v-model="task.date" class="form-control" />
            </span>
            <span v-else>{{ task.date }}</span>
          </td>
          <td>
            <div v-if="index === editedTaskIndex">
              <button @click="updateTask(index)">Ajustando</button>
            </div>
            <div v-else @click="openEditTaskModal(index)" data-bs-toggle="modal" data-bs-target="#exampleModal"
              class="pointer">
              <i class="fa-solid fa-pen"></i>
            </div>
          </td>
          <td>
            <div @click="deleteTask(index)" class="pointer">
              <i class="fa-solid fa-trash"></i>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
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
              <label for="floatingInput">Tarefa</label>
            </div>
            <div class="form-floating mb-3">
              <select v-model="modalTask.status">
                <option value="Pendente">Pendente</option>
                <option value="Em Progresso">Em Progresso</option>
                <option value="Concluído">Concluído</option>
              </select>
            </div>
            <div class="form-floating mb-3">
              <select v-model="modalTask.criticidade">
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
              <button type="button" class="btn btn-secondary" data-bs-dismiss="modal"
                @click="cancelEdit">Cancelar</button>
              <button type="button" class="btn btn-primary" data-bs-dismiss="modal" @click="saveModalTask">{{ addingTask ?
                'Adicionar' : 'Salvar Alterações' }}</button>
            </div>
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

    // Method to save or update the task from the modal
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
  }
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
  background-color: #1C1E25 !important;
  color: #42b983 !important;
}

.form-control {
  border: 1px solid #42b983 !important;
}

th {
  font-size: 21px;
}

.btn-primary {
  background: #42b983 !important;
  padding: 7px 30px !important;
  border: none !important;
  color: #1C1E25 !important;
  font-weight: bold !important;
}

.fa-pen,
.fa-trash {
  color: #42b983 !important;
}

.fa-filter {
  margin-top: 4px;
  position: absolute;
  margin-left: 29px;
}

.modal-content {
  background: #1C1E25 !important;
  color: #42b983;
}

.img-logo {
  display: flex !important;
  justify-content: center;
  align-items: center !important;
  text-align: center !important;
}

.img-logo img {
  width: 65px;
  margin-top: -74px;
}

table th,
tr,
thead,
tbody {
  border: 1px solid #42b983 !important;
}</style>
