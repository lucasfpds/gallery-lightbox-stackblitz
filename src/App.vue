<script setup>
import { ref } from "vue";
import Lightbox from "./components/Lightbox.vue";

const IMAGES = Array.from({ length: 12 }, (_, i) => ({
  src: `https://picsum.photos/seed/gal${i}/800/600`,
  caption: `Imagem ${i + 1}`,
}));

const open = ref(false);
const index = ref(0);
const lbRef = ref(null);

function openAt(i) {
  index.value = i;
  open.value = true;
}

function navigate(dir) {
  index.value = (index.value + dir + IMAGES.length) % IMAGES.length;
  if (lbRef.value) lbRef.value.reset();
}

function close() {
  open.value = false;
}
</script>

<template>
  <main class="app">
    <h1>Galeria com Lightbox</h1>
    <div class="grid">
      <figure
        v-for="(img, i) in IMAGES"
        :key="i"
        class="thumb"
        @click="openAt(i)"
      >
        <img :src="img.src" :alt="img.caption" loading="lazy" />
        <figcaption>{{ img.caption }}</figcaption>
      </figure>
    </div>
    <Transition name="lb">
      <Lightbox
        v-if="open"
        ref="lbRef"
        :images="IMAGES"
        :index="index"
        @close="close"
        @navigate="navigate"
      />
    </Transition>
  </main>
</template>
