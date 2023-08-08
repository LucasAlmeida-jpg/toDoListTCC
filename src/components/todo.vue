<template>
  <div class="container">
    <h2 class="text-center mt-5">TO DO LIST</h2>
    <div class="d-flex mt-5 rounded">
      <input v-model="task" placeholder="Escreva suas tarefas" class="w-100 form-control">
      <button class="btn btn-primary ms-2" @click="submitTask">
        {{ addingTask ? 'Adicionar' : 'Atualizar' }}
      </button>
    </div>

    <table class="table text-center table-bordered mt-5 rounded">
      <thead>
        <tr>
          <th scope="col">Tarefa</th>
          <th scope="col">Status</th>
          <th scope="col">Criticidade</th>
          <th scope="col">Editar</th>
          <th scope="col">Deletar</th>
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
            <div v-if="index === editedTaskIndex">
              <button @click="updateTask(index)">Salvar</button>
              <button @click="cancelEdit">Cancelar</button>
            </div>
            <div v-else @click="editTask(index)" class="pointer">
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
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  data() {
    return {
      task: "",
      editedTaskIndex: null,
      addingTask: true,
      tasks: []
    };
  },
  created() {
    const savedTasks = localStorage.getItem('tasks');
    if (savedTasks) {
      this.tasks = JSON.parse(savedTasks);
    }
  },
  methods: {
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
    submitTask() {
      if (!this.task.length) return;

      if (this.editedTaskIndex !== null) {
        this.tasks[this.editedTaskIndex] = {
          ...this.tasks[this.editedTaskIndex],
          name: this.task
        };
        this.editedTaskIndex = null;
      } else {
        this.tasks.push({
          name: this.task,
          status: 'Pendente',
          criticidade: 'Tranquilo'
        });
      }

      this.task = "";
      this.addingTask = true;

      this.saveTasksToLocalStorage();
    },
    saveTasksToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks));
    }
  }
};
</script>

<style scoped>
.container {
  max-width: 600px;
}
.pointer {
  cursor: pointer;
}
</style>
