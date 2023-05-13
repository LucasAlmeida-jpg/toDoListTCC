<template>
  <div class="container" style="max-width: 600px;">
    <h2 class="text-center mt-5">TO DO LIST</h2>
    <div class="d-flex mt-5 rounded">
      <input type="text" v-model="task" placeholder="Escreva suas tarefas" class="w-100 form control">
      <div class="btn btn-primary ms-2" @click="submitTask">
        {{ addingTask ? 'Adicionar' : 'Atualizar' }}
      </div>
    </div>

    <table class="table text-center table-bordered mt-5 rounded">
      <thead>
        <tr>
          <th scope="col">Tarefa</th>
          <th scope="col">Editar</th>
          <th scope="col">Deletar</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>{{ task.name }}</td>
          <td>
            <div @click="editTask(index)">
              <i class="fa-solid fa-pen pointer"></i>
            </div>
          </td>
          <td>
            <div @click="deleteTask(index)">
              <i class="fa-solid fa-trash pointer"></i>
            </div>
          </td>
        </tr>

      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      task: "",
      editedTask: null,
      addingTask: true,
      tasks: []
    }
  },
  methods: {
    capitalizeFirstChart(str) {
      return str.charAt(0).toUppercase() + str.slice(1);
    },
    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
      this.addingTask = false; // Altera para modo "atualizar"
    },
    deleteTask(index) {
      this.tasks.splice(index, 1)
    },
    submitTask() {
      if (this.task.length === 0) return
      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null
      } else {
        this.tasks.push({
          name: this.task,
        })
      }
      this.task = "";
      this.addingTask = true; // Altera para modo "adicionar"
    },
  },
}
</script>

<style scoped></style>
