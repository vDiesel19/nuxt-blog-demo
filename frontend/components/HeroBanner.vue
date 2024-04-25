<script setup lang="ts">
const props = defineProps({
  header: String
});
</script>
<template>
  <div class="hero-banner">
    <h1>{{ props.header }}</h1>
  </div>
</template>

<style>
.hero-banner {
  padding: 40px;
  background-color: #0d334c;
  text-align: center;
}
.hero-banner h1 {
  color: #fff;
  font-family: 'Playfair Display';
  font-weight: 400;
  font-size: 4.5rem;
  margin: 0 auto;
}
@media (max-width: 767px) {
  .hero-banner h1 {
    font-size: 3.5rem;
  }
}
</style>