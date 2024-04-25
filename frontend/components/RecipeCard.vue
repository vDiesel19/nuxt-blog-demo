<script setup lang="ts">
const props = defineProps({
  recipe: Object
});
</script>

<template>
  <li class="recipe-card">
    <a :href="`/recipes/${recipe.slug}`">
      <div 
        class="card-image"
        :style="{ 'background-image': 'url(' + recipe.images[0] + ')' }"
      >
      </div>
      <div class="card-details">
        <h3 class="recipe-name">{{ recipe.name }}</h3>
        <p class="recipe-ingredient">{{ recipe.ingredients[0].name }}</p>
        <p class="recipe-author">{{ recipe.author_email.split('@')[0] }}</p>
      </div>
    </a>
  </li>
</template>

<style>
.recipe-card {
  border-bottom: 2px solid rgba(85,85,85,.15);
  background-color: #fff;
  list-style-type: none;
  border-radius: 8px;
  overflow: hidden;
}
.recipe-card .card-image {
  padding-bottom: 40px;
  height: 200px;
  overflow: hidden;
  background-repeat: no-repeat;
  background-size: cover;
  background-position: 50%;
}
.recipe-card .card-details {
  padding: 20px 20px 30px;
}
.card-details .recipe-name {
  font-family: 'Playfair Display', sans-serif;
  font-weight: normal;
  text-transform: capitalize;
}
.card-details .recipe-ingredient {
  margin: 15px 0 5px;
}
.card-details .recipe-author {
  font-size: 14px;
  margin: 0;
}
.recipe-card a {
  color: black;
 text-decoration: none;
}
@media (max-width: 1024px) {
  .recipe-card {
    padding: 0;
    margin: 0;
    max-width: unset;
  }
}
@media (max-width: 767px) {
  .recipe-card .card-details {
    padding: 20px;
  }
}
</style>