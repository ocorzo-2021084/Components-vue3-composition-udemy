<script setup>
import BlogPost from './components/BlogPost.vue';
import ButtonCounter from './components/ButtonCounter.vue';
import PaginatePost from './components/PaginatePost.vue';
import LoadingSpinner from './components/LoadingSpinner.vue';
import { ref, computed, onMounted } from 'vue';

const posts = ref([]);
const postXpage = 10;
const inicio = ref(0);
const fin = ref(postXpage);
const loading = ref(true);

const favorito = ref('');

const cambiarFavorito = (title) => {
  favorito.value = title;
}

const next = () => {
  inicio.value = inicio.value + postXpage;
  fin.value = fin.value + postXpage;
}

const prev = () => {
  inicio.value = inicio.value - postXpage;
  fin.value = fin.value - postXpage;
}

const maxLength = computed(() => posts.value.length);

onMounted(async () => {
  try {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts');
    posts.value = await res.json();
  } catch (error) {
    console.log(error);
  } finally {
    setTimeout(() => {
      loading.value = false
    }, 1000);
  }
});


// fetch('https://jsonplaceholder.typicode.com/posts')
//   .then((res) => res.json())
//   .then((data) => { posts.value = data })
//   .finally(() =>{
//     setTimeout(() =>{

//     }, 2000);
//     loading.value = false
//   } );
</script>

<template>
  <LoadingSpinner v-if="loading" />
  <div class="container" v-else>
    <h1>App</h1>
    <h2>Mis post favorito: {{ favorito }}</h2>

    <PaginatePost @next="next" @prev="prev" :inicio="inicio" :fin="fin" class="mb-2" :maxLength="maxLength" />
    <BlogPost v-for="post in posts.slice(inicio, fin)" :key="post.id" :id="post.id" :title="post.title" :body="post.body"
      @cambiarFavorito="cambiarFavorito" class="mb-2"></BlogPost>
  </div>
</template>