<template>
  <div>
    <header>
      <nav>
        <ul>
          <li><a @click="showTodos" href="#">Todos</a></li>
          <li><a @click="showPosts" href="#">Posts</a></li>
        </ul>
      </nav>
    </header>

    <div id="content">
      <template v-if="showing === 'todos'">
        <!-- Todos Section -->
        <div class="todos-section">
          <h2>Todos</h2>
          <button @click="toggleFilter">{{ showCompletedTasks ? 'Tampilkan Belum Selesai' : 'Tampilkan Sudah Selesai' }}</button>

          <div class="add-task">
            <input v-model="newTask" type="text" placeholder="ADD TEXT" @keyup.enter="addTask">
            <button @click="addTask">Tambah</button>
          </div>

          <div class="tasks">
            <div v-for="(task, index) in filteredTasks" :key="index" :class="{ 'completed': task.completed }">
              <span>{{ task.name }}</span>
              <button @click="toggleTask(index)">{{ task.completed ? 'Belum Selesai' : 'Selesai' }}</button>
              <button @click="cancelTask(index)">Batal</button>
            </div>
          </div>
        </div>
      </template>

      <template v-else-if="showing === 'posts'">
        <!-- Posts Section -->
        <div class="posts-section">
          <h2>Postingan</h2>
          <div class="user-select">
            <select @change="fetchPosts" v-model="selectedUser">
              <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
            </select>
          </div>

          <div class="post-list" v-if="posts.length">
            <div v-for="post in posts" :key="post.id" class="post">
              <h3>{{ post.title }}</h3>
              <p>{{ post.body }}</p>
            </div>
          </div>

          <div v-else>
            <p>Loading posts...</p>
          </div>
        </div>
      </template>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// Todos
const tasks = ref([]);
const newTask = ref('');
const showCompletedTasks = ref(true);

const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({ name: newTask.value, completed: false });
    newTask.value = '';
  }
};

const toggleTask = (index) => {
  tasks.value[index].completed = !tasks.value[index].completed;
};

const cancelTask = (index) => {
  tasks.value.splice(index, 1);
};

const incompleteTasks = computed(() => tasks.value.filter(task => !task.completed));

const toggleFilter = () => {
  showCompletedTasks.value = !showCompletedTasks.value;
};

const filteredTasks = computed(() => {
  return showCompletedTasks.value ? tasks.value : incompleteTasks.value;
});

// Posts
const users = ref([]);
const selectedUser = ref(1);
const posts = ref([]);

const fetchUsers = async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users');
    users.value = await response.json();
  } catch (error) {
    console.error('Error fetching users:', error);
  }
};

const fetchPosts = async () => {
  try {
    const response = await fetch(`https://jsonplaceholder.typicode.com/posts?userId=${selectedUser.value}`);
    posts.value = await response.json();
  } catch (error) {
    console.error('Error fetching posts:', error);
  }
};

const showTodos = () => {
  showing.value = 'todos';
};

const showPosts = () => {
  showing.value = 'posts';
  fetchUsers();
  fetchPosts();
};

const showing = ref('todos');
</script>

<style scoped>
header {
  text-align: center;
}

nav ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

nav ul li {
  display: inline;
  margin-right: 10px;
}

.todos-section,
.posts-section {
  max-width: 600px;
  margin: 0 auto;
}

.todos-section h2,
.posts-section h2 {
  text-align: center;
  margin-bottom: 20px;
}

.add-task input {
  padding: 8px;
  width: 70%;
  margin-right: 10px;
}

.add-task button {
  padding: 8px 12px;
  background-color: #007bff;
  color: #fff;
  border: none;
  cursor: pointer;
}

.tasks {
  border: 1px solid #ccc;
  padding: 10px;
}

.tasks > div {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
}

.tasks > div.completed {
  text-decoration: line-through;
}

.tasks button {
  padding: 6px 10px;
  cursor: pointer;
}

.tasks button:nth-child(2) {
  background-color: #28a745;
  color: #fff;
}

.tasks button:nth-child(3) {
  background-color: #dc3545;
  color: #fff;
}

.user-select select {
  padding: 8px;
}
</style>
