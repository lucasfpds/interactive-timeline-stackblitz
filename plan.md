# Timeline Interativa

> Linha do tempo horizontal com eventos expansíveis ao scroll, incluindo transições e lazy-loading de conteúdo.

## Stack

- Vite + Vue 3 (`<script setup>`, JavaScript) — sem TypeScript, sem lint/test, zero libs extras
- CSS puro em `src/styles.css`
- Arquivos: `index.html`, `package.json` (deps: `vue`), `vite.config.js`, `.stackblitzrc`, `src/main.js`, `src/App.vue`, `src/styles.css`
- App.vue único — eventos como `v-for`, expansão por classes condicionais

## Implementação

### 1. Estrutura

- ~10 eventos fictícios (ex.: marcos da história da web) — `{ ano, titulo, resumo, detalhe, imagem }`
- Container `overflow-x: auto` com `scroll-snap-type: x proximity`; linha horizontal fixa no centro visual
- Cards alternando acima/abaixo da linha, conectados por um marcador com o ano
- Espaçamento final ("padding de scroll") para o último evento alcançar o centro

### 2. Expansão ao scroll

- Listener de `scroll` (passive) + `requestAnimationFrame`: distância de cada card ao centro do viewport
- Card central: `scale(1)`, sombra, conteúdo completo (resumo + detalhe carregado)
- Cards periféricos: `scale(0.85)`, opacidade reduzida, apenas ano + título
- Tudo com `transition: 0.3s ease` em `transform`/`opacity`
- Linha de progresso preenchendo conforme `scrollLeft / (scrollWidth - clientWidth)`

### 3. Lazy-loading

- O `detalhe` (texto + imagem) **não vem carregado** com a página
- Quando o card fica a menos de 1 viewport do centro pela primeira vez: `carregarDetalhe(evento)` simula rede (`setTimeout` ~600ms) e exibe skeleton shimmer no lugar
- Cache em memória — cada detalhe carrega uma única vez

### 4. Navegação auxiliar (desktop)

- Botões ‹ › com `scrollBy({ left: larguraDoCard, behavior: 'smooth' })`
- Arrastar com o mouse para rolar (pointer events alterando `scrollLeft`)

## Checklist — 100% da descrição

- [ ] Linha do tempo horizontal
- [ ] Eventos expandem/desexpande conforme o scroll
- [ ] Transições suaves
- [ ] Lazy-loading de conteúdo (skeleton + cache)
