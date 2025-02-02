<script setup lang="ts">
import { ref, onMounted, computed } from "vue";

// Interface pour le typage des lancements
interface Launch {
  id: string;
  name: string;
  date_utc: string;
  success?: boolean;
  details?: string;
  links?: {
    patch?: { large?: string };
    article?: string;
    webcast?: string;
  };
  launchpad?: string;
}

const launches = ref<Launch[]>([]);
const nextLaunch = ref<Launch | null>(null);
const launchFilter = ref("all");  // Filtre sélectionné
const countdown = ref(0);
const selectedLaunch = ref<Launch | null>(null);  // Pour le modal


const fetchLaunches = async () => {
  try {
    const res = await fetch("https://api.spacexdata.com/v5/launches");
    const data: Launch[] = await res.json();
    launches.value = data;

    // Récupération du prochain lancement
    const upcomingLaunches = launches.value.filter(
      (launch) => new Date(launch.date_utc) > new Date()
    );
    nextLaunch.value = upcomingLaunches[0] || null;
  } catch (error) {
    console.error("Erreur lors du fetch :", error);
  }
};

// Décompte du prochain lancement
const updateCountdown = () => {
  if (nextLaunch.value) {
    const launchDate = new Date(nextLaunch.value.date_utc).getTime();
    const timeNow = new Date().getTime();
    countdown.value = Math.floor((launchDate - timeNow) / 1000);
  }
};

setInterval(updateCountdown, 1000);

// Filtrage des lancements en fonction du filtre sélectionné
const filteredLaunches = computed(() => {
  if (launchFilter.value === "successful") {
    return launches.value.filter((launch) => launch.success === true);
  } else if (launchFilter.value === "failed") {
    return launches.value.filter((launch) => launch.success === false);
  }
  return launches.value;
});

// Gestion du modal
const openModal = (launch: Launch) => {
  selectedLaunch.value = launch;
};

const closeModal = () => {
  selectedLaunch.value = null;
};

onMounted(fetchLaunches);
</script>

<template>
  <h1 class="text-2xl font-bold text-center mt-6">🚀 Lancement SpaceX</h1>

  <div v-if="nextLaunch" class="mt-4 text-center">
    <h2 class="text-xl font-semibold">Prochain Lancement</h2>
    <p><strong>{{ nextLaunch.name }}</strong></p>
    <p>{{ new Date(nextLaunch.date_utc).toLocaleDateString() }}</p>
    <p class="text-red-500 font-bold mt-2">
      <span v-if="countdown > 0">
        Décompte: {{ countdown }} secondes
      </span>
      <span v-else>
        Lancement en cours !
      </span>
    </p>
  </div>

  <div class="mt-4">
    <label for="filter" class="mr-2">Filtrer les lancements:</label>
    <select id="filter" v-model="launchFilter" class="p-2 border rounded">
      <option value="all">Tous les lancements</option>
      <option value="successful">Lancements réussis</option>
      <option value="failed">Lancements échoués</option>
    </select>
  </div>

  <div v-if="launches.length" class="mt-6">
    <h2 class="text-xl font-semibold">Derniers lancements:</h2>
    <ul>
      <li v-for="launch in filteredLaunches" :key="launch.id" class="mt-4 p-4 border border-gray-300 rounded-lg">
        <p><strong>{{ launch.name }}</strong></p>
        <p>{{ new Date(launch.date_utc).toLocaleString() }}</p>
        <button @click="openModal(launch)" class="mt-2 p-2 bg-blue-500 text-white rounded">
          Voir les détails
        </button>
      </li>
    </ul>
  </div>

  <!-- Modal -->
  <div v-if="selectedLaunch" class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center">
    <div class="bg-white p-6 rounded-lg shadow-lg max-w-2xl w-full">
      <h2 class="text-xl font-bold">{{ selectedLaunch.name }}</h2>
      <p>Date : {{ new Date(selectedLaunch.date_utc).toLocaleDateString() }}</p>
      <p class="mt-2">{{ selectedLaunch.details || "Aucune description disponible." }}</p>

      <img v-if="selectedLaunch.links?.patch?.large" :src="selectedLaunch.links.patch.large" class="mt-4 w-40 h-40 object-cover mx-auto" />

      <a v-if="selectedLaunch.links?.article" :href="selectedLaunch.links.article" target="_blank" class="text-blue-500 mt-2 block">
        🔗 Lire l'article
      </a>

      <label class="flex items-center mt-4">
        <input type="checkbox" v-model="showVideo" class="mr-2" />
        Voir la vidéo du lancement
      </label>

      <iframe
        v-if="showVideo && selectedLaunch.links?.webcast"
        :src="selectedLaunch.links.webcast.replace('watch?v=', 'embed/')"
        class="mt-4 w-full h-64"
        frameborder="0"
        allowfullscreen
      ></iframe>

      <p class="mt-4">📍 Lieu : {{ selectedLaunch.launchpad || "Non spécifié" }}</p>

      <button @click="closeModal" class="mt-4 bg-red-500 text-white px-4 py-2 rounded">Fermer</button>
    </div>
  </div>
</template>
