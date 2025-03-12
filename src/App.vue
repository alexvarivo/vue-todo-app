<script setup>
import { ref, watch, onMounted } from "vue";

// load saved tools from localStorage
const loadTodos = () => {
  try {
    return JSON.parse(localStorage.getItem("todos")) || []; // return saved todos or empty list
  } catch (error) {
    console.error("LocalStorage is blocked or unavailable:", error);
    return []; // return empty list if storage unavailable
  }
};

// save current todos list to localStorage
const saveTodos = (todos) => {
  try {
    localStorage.setItem("todos", JSON.stringify(todos)); // store todos list as JSON string
  } catch (error) {
    console.error("Failed to save to localStorage:", error);
  }
};

// reactive vars
const todos = ref(loadTodos()); // todo list
const newTodo = ref(""); // new task input

// add new todo item to list
const addTodo = () => {
  if (newTodo.value.trim() == "") return; // prevent adding empty todos
  todos.value.push({ text: newTodo.value, completed: false }); // add new task (default "non completed" status)
  newTodo.value = ""; // clear input after adding task
};

// toggle completion status of todo item (cross out when clicked)
const toggleTodo = (index) => {
  todos.value[index].completed = !todos.value[index].completed; // toggle b/t completed & not completed
};

// delete todo item from list
const deleteTodo = (index) => {
  todos.value.splice(index, 1); // remove task from array
};

// watch todos list changes and update localStorage
watch(
  todos,
  (newTodos) => saveTodos(newTodos), // every time todos change, save to localStorage
  { deep: true } // nested objects tracked
);

// load todos when component is mounted (page refresh)
onMounted(() => {
  todos.value = loadTodos(); // tasks loaded when page opens
});
</script>

<template>
  <div class="container">
    <h1>TODO App</h1>
    
    <!-- input field to enter new tasks -->
    <div class="input-container">
      <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new task..." />
      <button @click="addTodo">Add</button>
    </div>

    <!-- list of tasks (w/ toggle and delete) -->
    <ul>
      <li v-for="(todo, index) in todos" :key="index">
      <!-- clicking task toggles completion status -->
        <span :class="{ completed: todo.completed }" @click="toggleTodo(index)">
          {{ todo.text }}
        </span>
        <!-- clicking "X" removes task -->
        <button @click="deleteTodo(index)">X</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
/* center main container */
.container {
  max-width: 400px;
  margin: auto;
  text-align: center;
}

/* style input area */
.input-container {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

/* style input field */
input {
  flex: 1;
  padding: 8px;
}

/* style add button */
button {
  padding: 8px 12px;
  cursor: pointer;
}

/* remove default list styles */
ul {
  list-style: none;
  padding: 0;
}

/* style individual todo items */
li {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

/* style completed tasks (crossed-out text) */
.completed {
  text-decoration: line-through;
  color: gray;
}
</style>
