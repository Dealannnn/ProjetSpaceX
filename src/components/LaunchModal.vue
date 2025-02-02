<template>
    <div v-if="launch" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center">
      <div class="bg-white p-6 rounded-lg shadow-lg max-w-2xl w-full">
        <h2 class="text-xl font-bold">{{ launch.name }}</h2>
        <p>Date : {{ new Date(launch.date_utc).toLocaleDateString() }}</p>
        <p class="mt-2">{{ launch.details || "Aucune description disponible." }}</p>
        
        <img v-if="launch.links?.patch?.large" :src="launch.links.patch.large" class="mt-4 w-40 h-40 object-cover mx-auto" />
        
        <a v-if="launch.links?.article" :href="launch.links.article" target="_blank" class="text-blue-500 mt-2 block">
          🔗 Lire l'article
        </a>
        
        <label class="flex items-center mt-4">
          <input type="checkbox" v-model="showVideo" class="mr-2" />
          Voir la vidéo du lancement
        </label>
  
        <iframe
          v-if="showVideo && launch.links?.webcast"
          :src="launch.links.webcast.replace('watch?v=', 'embed/')"
          class="mt-4 w-full h-64"
          frameborder="0"
          allowfullscreen
        ></iframe>
  
        <p class="mt-4">📍 Lieu : {{ launch.launchpad }}</p>
  
        <button @click="$emit('close')" class="mt-4 bg-red-500 text-white px-4 py-2 rounded">Fermer</button>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref } from "vue";
  
  defineProps(["launch"]);
  const showVideo = ref(false);
  </script>
  