<template>
  <div>
    <NuxtPage />
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

:root {
  --nintendo-font: 'Press Start 2P', cursive;
}

body {
  margin: 0;
  padding: 0;
  font-family: var(--nintendo-font);
  line-height: 1.4;
}

/* Ajustar el tamaño de fuente para mejor legibilidad */
p, span, button, input, select, textarea {
  font-size: 0.7rem;
}

h1 {
  font-size: 1.1rem;
}

h2 {
  font-size: 0.9rem;
}

h3 {
  font-size: 0.8rem;
}

/* Ajustar el espaciado para la fuente pixelada */
button, input, select, textarea {
  padding: 0.5rem 0.75rem;
  letter-spacing: 0.3px;
}

/* Ajustar el tamaño de los iconos y elementos de UI */
svg {
  width: 1rem;
  height: 1rem;
}
</style>
