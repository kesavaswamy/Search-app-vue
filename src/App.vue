<script setup>
import Home from './components/Home.vue';
import './assets/searchbox.css';
import './assets/resultsgrid.css';
</script>

<template>
  <main>
    <Home />
  </main>
</template>

<style scoped>
main {
  max-width: 100%;
  margin: 2rem auto;
  padding: 1rem;
}
.error {
  color: #b00;
  margin-bottom: 1rem;
}
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
