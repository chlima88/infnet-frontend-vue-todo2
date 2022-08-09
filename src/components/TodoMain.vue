<template>
  <div class="TodoMain">
    <h1>Do!App</h1>

    <form >
      <input
        class="default-input"
        placeholder="Digite a tarefa"
        @keyup.esc="taskInput='', readTask()"
        v-model="taskInput"
      >
      <div class="button-group">  
        <button class="default-button" type="submit" @click.prevent="readTask">Search</button>
        <button class="default-button" type="submit" @click.prevent="createTask">Add</button>
        <button class="default-button" type="submit" @click.prevent="clearTaskList">Clear</button>
      </div>
    </form>

    <ul class="default-ul">
      <li
        class="default-li"
        v-for="(task, index) in filteredTaskList"
        :key="index"
      >
        <div class="task">
          <input type="checkbox" v-model="task.done" :id="task.text">
          <textarea 
            v-if="editing.enabled && editing.text === task.text" 
            type="text" 
            v-model="tempTask"
          ></textarea>
          <label 
            v-else 
            :style="task.done ? 'text-decoration: line-through' : '' "
            :for="task.text"
          >{{task.text}}</label>
          
          <div class="task-controls">
            <button
              v-if="editing.enabled && editing.text === task.text"
              @click.prevent="updateTask(task)"
            >Save</button>
            <button
              v-else
              @click.prevent="toggleEdit(task.text)"
            >Edit</button>
            <button @click.prevent="deleteTask(index)">X</button>
          </div>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'


const taskInput = ref('')
const tempTask = ref('')
const editing = ref({text: "", enabled: false})
const taskList = [
  {text: "Estudar Vue o domingo inteiro", done: false},
  {text: "Lavar o carro", done: false},
  {text: "Ler um livro", done: false}
]

const filteredTaskList = ref([...taskList])

const createTask = () => {
  taskInput.value && filteredTaskList.value.push({text: taskInput.value, done: false})
  sortBy(filteredTaskList.value, "text", ( result ) => filteredTaskList.value = result)
  taskInput.value = ''
}

const readTask = () => {
  return taskInput.value
    ? filteredTaskList.value = taskList.filter(
        task => task.text.toLowerCase().includes(taskInput.value.toLowerCase())
      ) 
    : filteredTaskList.value = taskList
}

const updateTask = (task) => {
  task.text = tempTask.value
  editing.value = {text: "", enabled: false}
  sortBy(filteredTaskList.value, "text", ( result ) => filteredTaskList.value = result)
}

const deleteTask = (index) => {
  filteredTaskList.value.splice(index,1)
}

const toggleEdit = (text) => {
  editing.value = {text, enabled: true}
  tempTask.value = text
}

const clearTaskList = () => {
  filteredTaskList.value.splice(0)
}

const sortBy = (list, param="text",  callback, order=1) => {
  list.sort((a, b) => {
    return a[param].toLowerCase() > b[param].toLowerCase() 
    ? 1 * order 
    : -1 * order
  })
  callback 
  ? callback(list)
  : list
}


</script>


<style scoped lang="scss">

.TodoMain {
  @apply flex flex-col space-y-10 items-center justify-center;

  h1 {
    @apply text-6xl py-2 font-medium text-white;
  }

  form {
    .button-group{
      @apply flex justify-center;

      button {
        @apply flex-1 bg-violet-600 border-y-transparent border-x-violet-500 py-1 px-2 text-xl transition-all;

        &:first-child {
          @apply border-transparent rounded-bl-md;
        }
        &:last-child {
          @apply border-transparent rounded-br-md;
        }
        &:hover {
          @apply  bg-violet-500 brightness-90 transition-all;
        }
      }
    }

    input {
      @apply border border-solid w-96 border-transparent rounded-t-md py-1 px-2 text-xl;
    }
  }

  ul {
    @apply flex flex-col gap-3;
    .task {
      @apply flex shrink-0 justify-between ;
      
      label {
        padding-inline : 1rem;
      }

      textarea {
        margin-inline : 2rem;
      }

      .task-controls {
        @apply flex justify-between gap-2;

        input[type="checkbox"] {
          @apply checked:bg-blue-100 default:ring-2;
        }
        
        button {
          @apply  border-transparent bg-violet-600 px-2;

          &:hover {
            @apply bg-violet-500 brightness-90 transition-all;
          }
        }

      }


    }
  }

}

</style>
