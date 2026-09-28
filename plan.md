# Galeria com Lightbox e Zoom

> Grades de imagens que abrem em modal lightbox, com pinch-to-zoom mobile e animações de transição.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css`
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/components/Lightbox.vue`, `src/styles.css`

## Implementação

### 1. Grade

- 12 imagens `https://picsum.photos/seed/<seed>/800/600` com legendas
- Grid responsivo: `repeat(auto-fill, minmax(220px, 1fr))`
- Legenda no hover (desktop) / fixa (mobile); clique abre o lightbox

### 2. Lightbox (`components/Lightbox.vue`)

- Modal com backdrop escuro + `backdrop-filter: blur`; imagem central, legenda, contador (n/12), controles ‹ › ✕
- Entrada/saída via `<Transition>`: fade + scale (0.92 → 1); troca de imagem com fade curto
- Fechar por ✕, clique no backdrop ou Esc; setas ← → navegam entre imagens

### 3. Zoom e pan (mouse + touch)

- Estado `scale` (clamp 1–4) e `pan { x, y }` aplicados via `transform` na imagem
- **Pinch-to-zoom mobile**: rastrear pointers ativos num `Map` (pointer events); com 2 dedos, `scale = distAtual / distInicial` ancorado no ponto médio
- Com 1 dedo e `scale > 1`: arrasta a imagem (pan); `touch-action: none` na imagem
- Desktop: wheel com zoom para o ponto do cursor; duplo clique alterna 1 ↔ 2.5
- Double-tap no mobile reseta o zoom
- Quando `scale === 1`: gesto horizontal desliza para a imagem anterior/próxima

## Checklist — 100% da descrição

- [ ] Grade de imagens
- [ ] Modal lightbox com animações de transição
- [ ] Pinch-to-zoom no mobile (2 dedos, ancorado no ponto médio)
- [ ] Zoom/pan também no desktop (wheel + duplo clique)
