<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const props = defineProps({
  images: Array,
  index: Number,
});

const emit = defineEmits(["close", "navigate"]);

const scale = ref(1);
const pan = ref({ x: 0, y: 0 });
const pointers = new Map();
let pinchDist = 0;
let pinchScale = 1;
let panStart = null;
let lastTap = 0;

function imgSrc() {
  return props.images[props.index].src;
}
function imgCaption() {
  return props.images[props.index].caption;
}

const imgStyle = ref({});

function updateTransform() {
  imgStyle.value = {
    transform: `translate(${pan.value.x}px, ${pan.value.y}px) scale(${scale.value})`,
  };
}

function onPointerDown(e) {
  pointers.set(e.pointerId, { x: e.clientX, y: e.clientY });
  e.target.setPointerCapture(e.pointerId);
  if (pointers.size === 1) {
    panStart = { x: e.clientX - pan.value.x, y: e.clientY - pan.value.y };
  } else if (pointers.size === 2) {
    const pts = [...pointers.values()];
    pinchDist = Math.hypot(pts[0].x - pts[1].x, pts[0].y - pts[1].y);
    pinchScale = scale.value;
  }
}

function onPointerMove(e) {
  if (!pointers.has(e.pointerId)) return;
  pointers.set(e.pointerId, { x: e.clientX, y: e.clientY });
  if (pointers.size === 2) {
    const pts = [...pointers.values()];
    const dist = Math.hypot(pts[0].x - pts[1].x, pts[0].y - pts[1].y);
    scale.value = Math.max(1, Math.min(4, pinchScale * (dist / pinchDist)));
    updateTransform();
  } else if (pointers.size === 1 && scale.value > 1) {
    pan.value.x = e.clientX - panStart.x;
    pan.value.y = e.clientY - panStart.y;
    updateTransform();
  }
}

function onPointerUp(e) {
  pointers.delete(e.pointerId);
  if (pointers.size < 2) pinchDist = 0;
  if (pointers.size === 0) {
    if (scale.value === 1) {
      const dx = pan.value.x;
      if (Math.abs(dx) > 80) {
        emit("navigate", dx > 0 ? -1 : 1);
      }
      pan.value = { x: 0, y: 0 };
      updateTransform();
    }
    panStart = null;
  }
}

function onWheel(e) {
  e.preventDefault();
  const delta = e.deltaY > 0 ? -0.2 : 0.2;
  scale.value = Math.max(1, Math.min(4, scale.value + delta));
  if (scale.value === 1) pan.value = { x: 0, y: 0 };
  updateTransform();
}

function onDoubleClick(e) {
  scale.value = scale.value >= 2.5 ? 1 : 2.5;
  pan.value = { x: 0, y: 0 };
  updateTransform();
}

function onTap() {
  const now = Date.now();
  if (now - lastTap < 300) {
    scale.value = 1;
    pan.value = { x: 0, y: 0 };
    updateTransform();
  }
  lastTap = now;
}

function onKey(e) {
  if (e.key === "Escape") emit("close");
  if (e.key === "ArrowLeft") emit("navigate", -1);
  if (e.key === "ArrowRight") emit("navigate", 1);
}

function reset() {
  scale.value = 1;
  pan.value = { x: 0, y: 0 };
  updateTransform();
}

onMounted(() => {
  window.addEventListener("keydown", onKey);
  updateTransform();
});

onBeforeUnmount(() => {
  window.removeEventListener("keydown", onKey);
});

defineExpose({ reset });
</script>

<template>
  <div class="lightbox" @click.self="$emit('close')">
    <button class="lb-btn lb-close" @click="$emit('close')">✕</button>
    <button class="lb-btn lb-prev" @click="$emit('navigate', -1)">‹</button>
    <button class="lb-btn lb-next" @click="$emit('navigate', 1)">›</button>
    <div
      class="lb-img-wrap"
      @pointerdown="onPointerDown"
      @pointermove="onPointerMove"
      @pointerup="onPointerUp"
      @pointercancel="onPointerUp"
      @wheel.prevent="onWheel"
      @dblclick="onDoubleClick"
      @click="onTap"
    >
      <img :src="imgSrc()" :alt="imgCaption()" :style="imgStyle" draggable="false" />
    </div>
    <div class="lb-caption">
      <span>{{ imgCaption() }}</span>
      <span class="lb-counter">{{ index + 1 }} / {{ images.length }}</span>
    </div>
  </div>
</template>
