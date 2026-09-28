<script setup>
import { ref, onMounted, onBeforeUnmount, nextTick } from "vue";

const EVENTS = [
  { ano: 1991, titulo: "WWW", resumo: "Tim Berners-Lee publica o primeiro site." },
  { ano: 1993, titulo: "Mosaic", resumo: "Primeiro navegador gráfico popular." },
  { ano: 1995, titulo: "JavaScript", resumo: "Brendan Eich cria JS em 10 dias." },
  { ano: 1998, titulo: "CSS2", resumo: "Layout e separação de estilos." },
  { ano: 2004, titulo: "Web 2.0", resumo: "AJAX e apps interativas." },
  { ano: 2007, titulo: "iPhone", resumo: "A web móvel explode." },
  { ano: 2010, titulo: "HTML5", resumo: "Canvas, vídeo, semântica." },
  { ano: 2014, titulo: "Web Components", resstatus: true, resumo: "Componentes nativos da plataforma." },
  { ano: 2020, titulo: "WebGPU", resumo: "A próxima geração de gráficos na web." },
  { ano: 2024, titulo: "View Transitions", resumo: "Animações nativas entre estados." },
];

const scroller = ref(null);
const activeIndex = ref(0);
const progress = ref(0);
const detailCache = ref({});
const loading = ref({});
const rafId = { current: null };

function loadDetail(i) {
  if (detailCache.value[i] || loading.value[i]) return;
  loading.value[i] = true;
  setTimeout(() => {
    detailCache.value[i] = {
      texto: `Detalhes sobre ${EVENTS[i].titulo}: marco importante na evolução da web, com impacto duradouro em como construímos aplicações e experiências digitais hoje.`,
      imagem: `https://picsum.photos/seed/tl${EVENTS[i].ano}/400/200`,
    };
    loading.value[i] = false;
  }, 600);
}

function onScroll() {
  if (rafId.current) return;
  rafId.current = requestAnimationFrame(() => {
    rafId.current = null;
    const el = scroller.value;
    if (!el) return;
    const center = el.scrollLeft + el.clientWidth / 2;
    let nearest = 0;
    let nearestDist = Infinity;
    const cards = el.querySelectorAll(".event");
    cards.forEach((card, i) => {
      const cardCenter = card.offsetLeft + card.offsetWidth / 2;
      const dist = Math.abs(cardCenter - center);
      if (dist < nearestDist) {
        nearestDist = dist;
        nearest = i;
      }
      if (dist < el.clientWidth) loadDetail(i);
    });
    activeIndex.value = nearest;
    const max = el.scrollWidth - el.clientWidth;
    progress.value = max > 0 ? el.scrollLeft / max : 0;
  });
}

function nav(dir) {
  const el = scroller.value;
  if (!el) return;
  const card = el.querySelector(".event");
  const w = card ? card.offsetWidth + 40 : 300;
  el.scrollBy({ left: dir * w, behavior: "smooth" });
}

let dragId = null;
let dragStartX = 0;
let dragStartScroll = 0;

function onDragDown(e) {
  if (e.target.closest(".event")) return;
  dragId = e.pointerId;
  dragStartX = e.clientX;
  dragStartScroll = scroller.value.scrollLeft;
  scroller.value.setPointerCapture(e.pointerId);
}

function onDragMove(e) {
  if (e.pointerId !== dragId) return;
  scroller.value.scrollLeft = dragStartScroll - (e.clientX - dragStartX);
}

function onDragUp(e) {
  if (e.pointerId !== dragId) return;
  dragId = null;
}

onMounted(() => {
  onScroll();
});

onBeforeUnmount(() => {
  if (rafId.current) cancelAnimationFrame(rafId.current);
});
</script>

<template>
  <main class="app">
    <h1>Timeline Interativa</h1>
    <p class="subtitle">Marcos da história da web</p>
    <div class="progress-bar"><div class="progress-fill" :style="{ width: progress * 100 + '%' }"></div></div>
    <div class="scroller-wrap">
      <button class="nav nav--left" @click="nav(-1)" aria-label="Anterior">‹</button>
      <button class="nav nav--right" @click="nav(1)" aria-label="Próximo">›</button>
      <div
        ref="scroller"
        class="scroller"
        @scroll.passive="onScroll"
        @pointerdown="onDragDown"
        @pointermove="onDragMove"
        @pointerup="onDragUp"
        @pointercancel="onDragUp"
      >
        <div class="line"></div>
        <div
          v-for="(ev, i) in EVENTS"
          :key="ev.ano"
          class="event"
          :class="[
            i % 2 === 0 ? 'event--top' : 'event--bottom',
            { 'event--active': i === activeIndex }
          ]"
        >
          <div class="marker"><span>{{ ev.ano }}</span></div>
          <div class="card">
            <h3>{{ ev.titulo }}</h3>
            <p class="resumo">{{ ev.resumo }}</p>
            <div v-if="loading[i]" class="skeleton">
              <div class="skeleton__img"></div>
              <div class="skeleton__line"></div>
              <div class="skeleton__line short"></div>
            </div>
            <div v-else-if="detailCache[i]" class="detail">
              <img :src="detailCache[i].imagem" :alt="ev.titulo" loading="lazy" />
              <p>{{ detailCache[i].texto }}</p>
            </div>
          </div>
        </div>
        <div class="spacer"></div>
      </div>
    </div>
  </main>
</template>
