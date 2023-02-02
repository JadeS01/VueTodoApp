<script setup>
// ref is for state
// onMounted is when component is first rendered
// computed for ordering
// watch is for fetching localstorage/data
import { ref, onMounted, computed, watch } from "vue";
const todos = ref([]);
const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => b.createdAt - a.createdAt));
const todos_desc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));

const todos_work = computed(() => todos.value.filter((e) => e.category === "work"));
const todos_personal = computed(() => todos.value.filter((e) => e.category === "personal"));

onMounted(() => {
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
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = todo => {
  todos.value = todos.value.filter(e => e !== todo);
};

watch(todos, (newVal) => {
  localStorage.setItem("todos", JSON.stringify(newVal));
}, { deep: true });
// deep looks for every array element
</script>

<template>
  <main class="app">
    <ThemeToggle />
    <fieldset class="section">
      <legend>Create A Todo</legend>
      <form @submit.prevent="addTodo">
        <h4>What are your plans?</h4>
        <input type="text" placeholder="e.g. Open a PR" v-model="input_content">
        <h4>Select a category</h4>
        <div class="row">
          <div class="center">
            <label for="options" :class="`col-3 glass btn-${input_category === 'work' && 'active'}`">
              <input type="radio" name="category" id="category1" value="work" v-model="input_category">
              <span class="bubble work"></span>
              <div>Work</div>
            </label>
            <div class="col-gap-1"></div>
            <label for="options" :class="`col-3 glass btn-${input_category === 'personal' && 'active'}`">
              <input type="radio" name="category" id="category1" value="personal" v-model="input_category">
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>
          </div>
          <br />
          <div class="center">
            <input type="submit" value="Add todo">
          </div>
        </div>
      </form>
    </fieldset>
    <fieldset class="section">
      <legend>Todo Lists</legend>
      <div class="list-row">

        <fieldset class="col-6">
          <legend>Work List</legend>
          <div v-for="todo in todos_work" :class="`todo-item ${todo.completed && 'strike'} row`">
            <label class="li">
              <input type="checkbox" v-model="todo.completed">
              <span :class="`bubble ${todo.category}`">{{ todo.category }}</span>
            </label>
            <div class="li">
              <input type="text" v-model="todo.content">
            </div>
            <div class="li">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </fieldset>

        <fieldset class="col-6">
          <legend>Personal List</legend>
          <div v-for="todo in todos_personal" :class="`todo-item ${todo.completed && 'strike'} row`">
            <label class="li">
              <input type="checkbox" v-model="todo.completed">
              <span :class="`bubble ${todo.category}`">{{ todo.category }}</span>
            </label>
            <div class="li">
              <input type="text" v-model="todo.content">
            </div>
            <div class="li">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </fieldset>

      </div>


      <div class="list">
        <!-- mapping each todo -->
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.completed && 'strike'} row`">
          <label class="li">
            <input type="checkbox" v-model="todo.completed">
            <span :class="`bubble ${todo.category}`">{{ todo.category }}</span>
          </label>
          <div class="li">
            <input type="text" v-model="todo.content">
          </div>
          <div class="li">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </fieldset>
  </main>
</template>

<script>
import ThemeToggle from "./components/ThemeToggle.vue";
export default {
  components: { ThemeToggle }
};
</script>
