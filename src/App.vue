<script setup>
// ref is for state
// onMounted is when component is first rendered
// computed for ordering
// watch is for fetching localstorage/data
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const name = ref('');
const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => b.createdAt - a.createdAt));
const todos_desc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));

onMounted(() => {
  name.value = localStorage.getItem('name') || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    completed: false,
    createdAt: new Date().getTime()
  });

  // reset state
  input_category.value = "";
  input_category.value = null;
};

const removeTodo = todo => {
  todos.value = todos.value.filter(e => e !== todo);
};

watch(name, (newVal) => { localStorage.setItem('name', newVal); });
watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal));
}, { deep: true });
// deep looks for every array element
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Name" v-model="name" />
      </h2>
    </section>
    <ThemeToggle />
    <section class="create-todo">
      <h3 class="">Create A Todo</h3>
      <form @submit.prevent="addTodo">
        <h4>What are your plans?</h4>
        <input type="text" placeholder="e.g. Open a PR" v-model="input_content">
        <h4>Select a category</h4>
        <div class="options">
          <label for="options">
            <input type="radio" name="category" id="category1" value="work" v-model="input_category">
            <span class="bubble work"></span>
            <div>Work</div>
          </label>
          <label for="options">
            <input type="radio" name="category" id="category1" value="personal" v-model="input_category">
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo">
      </form>
    </section>
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <!-- mapping each todo -->
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && done}`">
          <label>
            <input type="checkbox" v-model="todo.completed">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script>
import ThemeToggle from "./components/ThemeToggle.vue";
export default {
  components: { ThemeToggle }
};
</script>
