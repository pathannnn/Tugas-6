<template>
  <div>
    <form @submit.prevent="save">
      <h2> Content Planning </h2>
      <h4> Masukan Judul </h4>
      <input type="text" id="title" v-model="form.title" /> <br />
      <h4> Konten apa yang mau dibuat? </h4>
      <textarea v-model="form.content"></textarea>  <br />
      <button type="submit">Save</button>
    </form>
    <ul>
      <li v-for="article in articles" :key="article.id">
        {{ article.title }} <br/>
        {{ article.content }} <br/>
        <button @click="edit(article)">Edit</button>
        <button @click="deleteData(article.id)">Delete</button> 
      </li>
    </ul>
    <button @click="load">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);

        if (form.id) {
          articles.value = articles.value.map((article) =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function deleteData(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, deleteData };
  },
};
</script>

<style scoped>
body {
  background-color: #f0f0f0; 
  margin: 0;
  padding: 0;
  font-family: Arial, sans-serif;
}
h2{
  color: black;
  text-align: center;
}
h4{
  color: black;
}

form {
  display: block;
  flex-direction: column;
  width: 100%; 
  max-width: 100%;
  margin: 0 auto; 
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
  box-sizing: border-box; 
}


input[type="text"],
textarea {
  width: 100%;
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box; 
}


button[type="submit"] {
  width: 100%;
  padding: 15px;
  margin: 10px 0;
  border: none;
  border-radius: 4px;
  background-color: #4CAF50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button[type="submit"]:hover {
  background-color: #45a049;
}


ul {
  list-style: none;
  padding: 0;
  width: 100%;
  max-width: 100%; /* Mengatur lebar maksimum ul menjadi 100% dari lebar layar viewport */
  margin: 20px auto;
  box-sizing: border-box; 
}

li {
  color: black;
  background-color: #fff;
  margin: 10px 0;
  padding: 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  box-sizing: border-box; 
}


li button {
  margin-top: 10px;
  background-color: #ff5722;
  padding: 10px 20px;
  width: auto;
}

li button:first-of-type {
  background-color: #2196F3;
}

li button:hover {
  background-color: #e64a19;
}

li button:first-of-type:hover {
  background-color: #1976D2;
}

button.load-button {
  display: block;
  width: 100%;
  padding: 15px;
  margin: 20px auto;
  background-color: #009688;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button.load-button:hover {
  background-color: #00796B;
}
</style>




