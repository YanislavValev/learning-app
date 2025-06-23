<template>
  
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <div>
    <div class="header">
      <h3>Welcome</h3>
      <i class="fas fa-user user-icon"></i>
    </div>
    <br>
    <button @click="showForm = true" class="show-form-button" v-if="!showForm">Add new course</button>


    <form v-if="showForm" @submit.prevent="addCourse"> 
      <h3>Add new course</h3>
      <input v-model.number="newCourse.id" type="number" placeholder="ID" required />
      <input v-model="newCourse.name" placeholder="Name" required />
      <!--<input v-model="newCourse.description" placeholder="Description" required /> -->
      <input v-model.number="newCourse.lessonsCount" type="number" placeholder="Lessons(count)" required min="0"/>
      <button type="submit"><i class="fa fa-plus"></i></button>
      <button type="button" class="cancel-edit-button" @click="cancelAdd"><i class="fas fa-circle-xmark"></i></button>
    </form>

    

    <table border="2" cellpadding="10">
      <thead>
        <tr>
          <th>ID</th>
          <th>Course name</th>
          <th>Description</th>
          <th>Lessons(count)</th>
          <th>State</th>
          <th>Date</th>
          <th>Image</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="course in courses" :key="course.id">
          <td>{{ course.id }}</td>
          <td>{{ course.name }}</td>
          <td>{{ course.description }}</td>
          <td>{{ course.lessonsCount }}</td>
          <td>
            <select v-model="course.state">
              <option value="Active">Active</option>
              <option value="Archived">Archived</option>
            </select>
          </td>
          <td>{{ course.date }}</td>
          <td><img :src="course.image" width="40" /></td>
          <td>
            <button @click="startEdit(course)" class="edit-button" ><i class="fas fa-edit"></i></button>
            <button @click="deleteCourse(course.id)" class="delete-button"><i class="fas fa-trash-alt"></i></button>
          </td>
        </tr>
      </tbody>
    </table>

    <br>
    <br>
    <br>
    <br>
    <br>
    <router-link to="/" style="font-size: 20px; margin-right: 50px;">Home</router-link>
    <router-link to="/learn" style="font-size: 20px;">Learning app </router-link>
    <br>
    <br>
    <br>

    <!-- Edit -->
    <div v-if="editingCourse">
  <h3>Course edit</h3>

  <label>
    ID:
    <input v-model.number="editingCourse.id" required />
  </label>
  <br />
  <label>
    Name:
    <input v-model="editingCourse.name" />
  </label>
  <br />
  <label>
    Lessons(count):
    <input type="number" v-model.number="editingCourse.lessonsCount" min="0" />
  </label>
  <br />
      <button @click="saveEdit" ><i class="fas fa-save"></i></button>
      <button @click="cancelEdit" class="cancel-edit-button"><i class="fas fa-circle-xmark"></i></button>
  </div> 
  </div>
</template>


<script setup>

const STORAGE_KEY = 'my-view-courses';
import { ref, onMounted, watch } from 'vue'
const vueLogo = 'https://vuejs.org/images/logo.png'
const courses = ref([]);

onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY);
  if (saved) {
    courses.value = JSON.parse(saved);
  } else {
    // Hardcoded courses
    courses.value = [
      {
        id: 1,
        name: 'Vue.js Basics',
        description: 'Some description',
        lessonsCount: 12,
        state: 'Active',
        date: '2025-02-18',
        image: vueLogo
      },
      {
        id: 2,
        name: 'Advanced Vue',
        description: 'Some description',
        lessonsCount: 18,
        state: 'Active',
        date: '2025-05-01',
        image: vueLogo
      }
    ];
  }
});

watch(courses, (newValue) => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(newValue));
}, { deep: true });

const editingCourse = ref(null);

const newCourse = ref({
  id: null,
  name: '',
  description: '',
  lessonsCount: null
})

const showForm = ref(false)

function addCourse() {
  const today = new Date().toISOString().slice(0, 10)

  courses.value.push({
    id: newCourse.value.id,
    name: newCourse.value.name,
    description: 'Some description', 
    lessonsCount: newCourse.value.lessonsCount,
    state: 'Active',
    date: today,
    image: vueLogo
  })

  newCourse.value.id = null;
  newCourse.value.name = ''
  //newCourse.value.description = '' 
  newCourse.value.lessonsCount = null
  showForm.value = false
}

function cancelAdd() {
  newCourse.value.id = null;
  newCourse.value.name = ''
  //newCourse.value.description = '' //
  newCourse.value.lessonsCount = null
  showForm.value = false
}

function deleteCourse(id) {
  courses.value = courses.value.filter(c => c.id !== id)
  if (editingCourse.value?.id === id) {
    editingCourse.value = null
  }
  alert('The course was deleted successfully!');
}

function startEdit(course) {
  editingCourse.value = { ...course } 
}

function saveEdit() {
  const index = courses.value.findIndex(c => c.id === editingCourse.value.id);
  if (index !== -1) {
    const originalState = courses.value[index].state;

    courses.value[index] = {
      ...editingCourse.value,
      state: originalState
    };
  }
  editingCourse.value = null;
  alert('The course was edited successfully!');
}

function cancelEdit() {
  editingCourse.value = null
}
</script>


<style scoped>
input {
  margin: 0.3rem;
  padding: 0.3rem;
}
button {
  margin: 0.3rem;
  padding: 0.4rem 1rem;
  background: #42b983;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}
button:hover {
  background: #2c9d6f;
}
.delete-button {
  background-color: #e74c3c;
  color: white;
}
.delete-button:hover {
  background-color: #c0392b;
}
.edit-button {
  background-color: #b3b34e;
  color: white;
}
.edit-button:hover {
  background-color: #85853a;
}
.cancel-edit-button {
  background-color:#FFA500;
}
.cancel-edit-button:hover {
  background-color: #b77c10;
}
.show-form-button{
  margin: 0.3rem;
  margin-left: auto;
  padding: 0.4rem 1rem;
  height: 2.1rem;
  background: #455ab0;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer
}
.show-form-button:hover{
  background: #203074;
}
table{
  margin-left: auto;
  margin-right: auto;
}
.header {
  font-size: 50px;
  display: flex;
  align-items: center;       
  justify-content: space-between; 
  margin: 0 auto;            
}

.user-icon {
  font-size: 32px;
  color: #42b983;
}
th, td{
  min-width: 140px;
  padding: 8px 12px;
}
select {
  background-color: #3b5078;
}
</style>