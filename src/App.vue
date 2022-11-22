<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

const todo_asc = computed(() =>
  todos.value.slice(0).sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

watch(
  () => todos.value,
  (todos) => {
    localStorage.setItem("todos", JSON.stringify(todos));
  },
  { deep: true }
);

watch(name, (newName) => {
  localStorage.setItem("name", newName);
});

const createTodo = () => {
  if (input_content.value === "" || input_category.value === null) {
    return;
  }
  const todo = {
    id: todos.value.length + 1,
    content: input_content.value,
    category: input_category.value,
    completed: false,
    editabled: false,
    createdAt: new Date().getTime(),
  };
  todos.value.push(todo);
  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (id) => {
  todos.value = todos.value.filter((todo) => todo.id !== id);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up <input v-model="name" type="text" placeholder="Your name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a todo</h3>

      <form @submit.prevent="createTodo">
        <h4>What's on your today's list</h4>
        <input
          v-model="input_content"
          type="text"
          placeholder="e.g learn VueJs"
        />

        <h4>What category does it belong to</h4>
        <div class="options">
          <label>
            <input
              v-model="input_category"
              type="radio"
              value="work"
              name="category"
              id="category1"
            />
            <span class="bubble business"></span>
            <div>Work</div>
          </label>
          <label>
            <input
              v-model="input_category"
              type="radio"
              value="personal"
              name="category"
              id="category2"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Create Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="todo in todo_asc"
          :key="todo.id"
          :class="`todo-item ${todo.completed && 'done'}`"
        >
          <label>
            <input
              type="checkbox"
              :checked="todo.completed"
              @change="todo.completed = !todo.completed"
            />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo.id)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
