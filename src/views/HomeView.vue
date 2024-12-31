<script setup lang="ts">
import data from '@/assets/data.json';
import { ref } from 'vue';


const BASE_URL = 'https://www.hafizahmedyar.com/audio'
const src = ref(getAudioSrc());
const value = ref('26/135');
const key = ref(Date.now());

// REFS
const player = ref<HTMLAudioElement | null>(null);



function getAudioSrc(file: number = 127) {
  return `${BASE_URL}/${file}.mp3`;
}

function goto() {
  key.value = Date.now();
  const [surah, ayah] = value.value.split('/').map((v) => parseInt(v, 10));
  const surahIdx = data.findIndex((s) => s.surah === surah);

  if (surahIdx === -1) {
    src.value = getAudioSrc();
  }

  const ayaIdx = data[surahIdx].data.findIndex((a) => a.ayah === ayah);

  if (surahIdx === -1 || ayaIdx === -1) {
    src.value = getAudioSrc();
  }

  const file = data[surahIdx].data[ayaIdx].file;
  const timestamp = data[surahIdx].data[ayaIdx].timestamp;

  if (file) {
    src.value = getAudioSrc(file);
  }

  setTimeout(() => {
    if (player.value) {
      player.value.currentTime = Number(timestamp);
      player.value.play();

    }
  }, 500);
}

function moveBy(seconds: number) {
  if (player.value) {
    player.value.currentTime += seconds;
  }
}
</script>

<template>
  <main>
    File: <a :href="src" target="_blank">{{  src }}</a>
    <audio :key="key" id="player" controls ref="player">
      <source id="sourceMp3" :src="src" type="audio/mp3" />
      Your browser does not support the audio element.
    </audio>

    <div>
      <div class="w-full max-w-sm min-w-[200px]">
        <div class="relative">
          <input type="input" v-model="value" @keyup.enter="goto" class="w-full bg-transparent placeholder:text-slate-400 text-slate-100 text-sm border border-slate-200 rounded-md pl-3 pr-16 py-2 transition duration-300 ease focus:outline-none focus:border-slate-400 hover:border-slate-300 shadow-sm focus:shadow" placeholder="26/135" />
          <button
            class="absolute right-1 top-1 rounded bg-slate-800 py-1 px-2.5 border border-transparent text-center text-sm text-white transition-all shadow-sm hover:shadow focus:bg-slate-700 focus:shadow-none active:bg-slate-700 hover:bg-slate-700 active:shadow-none disabled:pointer-events-none disabled:opacity-50 disabled:shadow-none"
            type="button"
            @click="goto"
          >
            Go to
          </button>
        </div>
        <div class="my-2">
          <button type="button" @click="moveBy(-3)" class="py-2.5 px-5 me-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-100 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">Backword</button>
          <button type="button" @click="moveBy(+3)" class="py-2.5 px-5 me-2 mb-2 text-sm font-medium text-gray-900 focus:outline-none bg-white rounded-lg border border-gray-200 hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-4 focus:ring-gray-100 dark:focus:ring-gray-700 dark:bg-gray-800 dark:text-gray-400 dark:border-gray-600 dark:hover:text-white dark:hover:bg-gray-700">Forward</button>

        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
  #player {
    width: 100%;
    margin-top: 2rem;
  }
</style>
