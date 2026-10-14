# Galeria com Lightbox e Zoom

> Grades de imagens que abrem em modal lightbox, com pinch-to-zoom mobile e animações de transição.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **CSS puro** com `backdrop-filter: blur`
- **Pointer Events** nativos para pinch-to-zoom (Map de pointers ativos)
- **Transition** do Vue para entrada/saída do lightbox

## Requisitos

- Node.js 18+

## Como executar

```bash
npm install
npm run dev
```

Abra http://localhost:5173 no navegador.

## Scripts

| Script | Descrição |
| --- | --- |
| `npm run dev` | Servidor de desenvolvimento (Vite) |
| `npm run build` | Build de produção |
| `npm run preview` | Serve o build de produção |

## Estrutura

```
src/
  main.js              entry: cria o app e importa o CSS
  App.vue              grade de thumbnails, abre/fecha lightbox, navegação
  components/
    Lightbox.vue       modal, zoom, pan, pinch-to-zoom, wheel
  styles.css           grid, lightbox, controles, transições
```

### Funcionalidades

- 12 imagens via `picsum.photos` com legendas
- Grid responsivo: `repeat(auto-fill, minmax(220px, 1fr))`
- Modal lightbox com backdrop escuro + blur
- Entrada/saída via `<Transition>`: fade + scale (0.92 → 1)
- Controles: ✕, ‹ ›, contador (n/12), Esc, setas ← →
- Zoom e pan (mouse + touch): `scale` clamp 1–4, `pan { x, y }`
- Pinch-to-zoom mobile: rastreamento de 2 pointers, escala ancorada no ponto médio
- Desktop: wheel com zoom, duplo clique alterna 1 ↔ 2.5
- Double-tap no mobile reseta o zoom
- Quando `scale === 1`: gesto horizontal desliza para a imagem anterior/próxima


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/gallery-lightbox-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.
