<script setup>
import {ref, computed, onMounted, watch} from 'vue';

const doList = ref([]);
const name = ref('');

const inputValue = ref('');
const inputValueDate = ref(null);
const inputValueTime = ref(null);

const doListSorted = computed(() =>
  doList.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (
    inputValue.value.trim() === '' ||
    inputValue.value === null ||
    inputValueDate.value === null ||
    inputValueTime.value === null
  ) {
    return;
  }
  doList.value.push({
    content: inputValue.value,
    time: inputValueTime.value,
    date: inputValueDate.value,
    createdAt: new Date().getTime(),
    success: false,
  });
  inputValue.value = '';
  inputValueDate.value = '';
  inputValueTime.value = '';
};

const removeTodo = (todo) => {
  doList.value = doList.value.filter((to) => to !== todo);
};

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});
watch(
  doList,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  },
  {deep: true}
);
watch(doList, () => {});
onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  doList.value = JSON.parse(localStorage.getItem('todos')) || [];
});
</script>

<template>
  <main class="app">
    <section class="welcome">
      <h4 class="title">Представьтесь (‾◡◝)</h4>
      <input
        style="width: 10rem; border-width: 0px; border: none"
        type="text"
        placeholder="Введите ваше имя..."
        v-model="name"
      />
    </section>

    <section class="create">
      <div class="create__todo">
        <h4>Создать задачу:</h4>
        <form @submit.prevent="addTodo">
          <input
            type="text"
            style="height: 5rem"
            class="todo__body-input"
            placeholder="Описание задачи"
            v-model="inputValue"
          />
          <span>Дедлайн:</span>
          <input type="date" v-model="inputValueDate" />
          <input type="time" v-model="inputValueTime" />
          <button
            class="btn waves-effect waves-light"
            type="submit"
            name="action"
          >
            Создать
            <i class="material-icons right"></i>
          </button>
        </form>
      </div>
      <!-- {{ doList }} -->
    </section>

    <section class="todo">
      <h4>Список задач для {{ name }}</h4>
      <div class="todo__list">
        <div class="todo__item" v-for="todo in doListSorted" :key="todo">
          <div class="todo__body">
            <div v-if="todo.success === false">
              <span>Описание задачи</span>
              <input
                class="todo__body-input"
                type="text"
                v-model="todo.content"
              />
              <span>Дедлайн</span>
              <input type="text" v-model="todo.date" />
              <input type="text" v-model="todo.time" />
              <p>
                <label>
                  <input type="checkbox" v-model="todo.success" />
                  <span v-if="todo.success === false" class="todo__status">
                    Не выполнена
                  </span>
                  <span v-else class="todo__status"> Выполнена </span>
                </label>
              </p>
              <div class="todo__button">
                <button
                  class="btn waves-effect waves-light"
                  type="submit"
                  name="action"
                  @click="removeTodo(todo)"
                >
                  Удалить задачу
                  <i class="material-icons right"></i>
                </button>
              </div>
            </div>
            <div v-else>
              Задача выполнена - {{ todo.content }}
              <div class="todo__button">
                <button
                  class="btn waves-effect waves-light"
                  type="submit"
                  name="action"
                  @click="removeTodo(todo)"
                >
                  Удалить задачу
                  <i class="material-icons right"></i>
                </button>
                <p>
                  <label>
                    <input type="checkbox" v-model="todo.success" />
                    <span v-if="todo.success === false" class="todo__status">
                      Не выполнена
                    </span>
                    <span v-else class="todo__status"> Выполнена </span>
                  </label>
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style>
.todo__item {
  border: 2px solid green;
  padding: 4rem;
  margin-top: 1.25rem;
}
input {
  color: white;
}
</style>
