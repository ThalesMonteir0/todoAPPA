<script setup>
import {watch, reactive, onMounted} from "vue";
import axios from "axios"

const state = reactive({
  input: false,
  tasks: [],
  inputTasks: ""
})
const getTasks = async () => {
  state.tasks = []
  axios.get("http://localhost:3000/getTasks").then(res => {
    res.data.forEach(item => {
      item = {
        id: item.id,
        description : item.description,
        checked: false
      }
      state.tasks.push(item)
    })
  }).catch(console.error)
}
const createTask = async () => {
  if (state.inputTasks.trim().length > 0){
  let body = {
    description: state.inputTasks
  }
  axios.post("http://localhost:3000/createTask", body).then( async () => {
    state.inputTasks = ""
    await getTasks()
  }).catch(console.error)
  }
}

const deleteTask = async (id) => {
  let url = "http://localhost:3000/deleteTask/"+id
  axios.delete(url).then(async () => {
    await getTasks()
  }).catch(console.error)
}
onMounted(async () => {
  await getTasks()
})


</script>

<template>
  <div class="mt-12">
    <div class="flex justify-content-center">
      <p class="font-bold">TODO</p><p>-</p> <p class="text-emerald-500 font-bold">APP</p>
    </div>
    <div class="flex justify-center items-center">
      <div class="rounded border-slate-500 border-solid border-2 col-6">
        <div class="flex justify-content-between mt-2 mb-3">
          <input placeholder="Digite sua tarefa" class="rounded-xl bg-transparent border-solid border col-9"
                 style="color: #ffff;" v-model="state.inputTasks">
          <button type="button" class="btn btn-outline-light" @click="createTask()">Salvar</button>
        </div>
        <div>
          <hr>
          <div class="flex justify-content-between mb-3" v-for="(item, index) in state.tasks" v-if="state.tasks.length > 0">
            <div>
              <input type="checkbox" v-model="item.checked">
              <span class="ml-1" :style="item.checked ? 'text-decoration: line-through;' : '' ">{{item.description}}</span>
            </div>
            <button type="button" class="btn btn-outline-danger btn-sm mr-3" @click="deleteTask(item.id)">Excluir</button>
          </div>
          <div CLASS="flex justify-content-center" v-else>
            <span class="text-gray-500">VOCÊ AINDA NÃO TEM TAREFAS AQUI!</span>
          </div>
        </div>
      </div>
    </div>
  </div>

</template>

<style scoped>

</style>
