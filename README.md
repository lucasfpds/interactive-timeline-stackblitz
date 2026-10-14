# Timeline Interativa

> Linha do tempo horizontal com eventos expansíveis ao scroll, incluindo transições e lazy-loading de conteúdo.

## Tecnologias

- **Vue 3** com `<script setup>` (JavaScript)
- **Vite** como bundler
- **CSS puro** com `scroll-snap-type: x proximity`
- **requestAnimationFrame** para cálculo do card central no scroll
- Lazy-loading simulado com `setTimeout` (skeleton shimmer)

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
  App.vue              scroller, eventos, expansão, lazy-loading, navegação
  styles.css           layout, linha, cards, skeleton, marcadores
```

### Funcionalidades

- 10 eventos fictícios (marcos da história da web)
- Cards alternando acima/abaixo da linha horizontal
- Card central em destaque (scale 1, sombra, conteúdo completo)
- Cards periféricos com scale 0.85 e opacidade reduzida
- Transições suaves (0.3s ease) em transform/opacity
- Linha de progresso preenchendo conforme o scroll
- Lazy-loading: o detalhe (texto + imagem) carrega sob demanda com skeleton shimmer
- Cache em memória — cada detalhe carrega uma única vez
- Botões ‹ › para navegação (scrollBy smooth)
- Arrasto com o mouse para rolar (pointer events)


## Rodando no StackBlitz

Basta importar o repositório e rodar normalmente:

```
https://stackblitz.com/github/lucasfpds/interactive-timeline-stackblitz
```

O projeto não depende de binários nativos — Vite é JavaScript puro, então
sobe sem bindings nativos no WebContainer do StackBlitz.
