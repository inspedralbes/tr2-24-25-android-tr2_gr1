<template>
  <div class="main">
    <h1>{{ noticia.titol }}</h1>
    <h3>{{ noticia.subtitol }}</h3>
    <p>{{ noticia.contingut }}</p>

    <p class="author">Per {{ noticia.autor }} el {{ noticia.updatedAt }}</p>
    <NavigationBar />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import NavigationBar from '@/components/NavigationBar.vue';
import { getNoticies } from '@/services/comunicationManager';


const props = defineProps(['id']);
const noticia = ref({});

const noticies = ref([]);
onMounted(async () => {
  try {
        noticies.value = await getNoticies();
        console.log(noticies.value); // Agrega esta línea para ver qué datos llegan
        noticia.value = noticies.value.find(noticia => noticia.id == props.id);
    } catch (error) {
        console.error('Error al obtenir les notícies:', error);
    }

})

</script>

<style scoped>
.main {
  background-color: var(--main-color);
  padding: 1rem;
  padding-bottom: 4rem;
  width: 100%;
  box-sizing: border-box;
  margin: 0;
  height: calc(100vh - 4rem);
  overflow-y: auto;
}

h1 {
  color: var(--secondary-dark-color);
  font-size: 2rem;
}

h3 {
  color: var(--secondary-light-color);
  font-size: 1.5rem;
}

p {
  color: var(--bold-color);
  font-size: 1.2rem;
}

.author {
  font-size: 0.75rem;
}

img {
  display: block;
  margin: 0 auto;
  width: 50%;
}
</style>