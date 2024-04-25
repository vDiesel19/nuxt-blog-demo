<script setup>
  const baseURL = 'http://localhost:8888/api/recipes';
  let recipe = ref({});
  let protein = ref('');
  let featuredImage = ref('');
  let price = ref(0);
  const route = useRoute();

  async function fetchRecipe() {
    try {
      const response = await fetch(`${baseURL}/${route.params.slug}`);
      const data = await response.json();
      
      recipe.value = data.data;
      protein = recipe.value.ingredients[0].name;
      featuredImage = recipe.value.images[0];
      price = parseInt(recipe.value.ingredients[0].price) / 100;

      let nameUppercase = recipe.value.name.split(' ').map(word => word.substring(0, 1).toUpperCase() + word.substring(1)).join(' ');

      useSeoMeta({
        title: `${nameUppercase}`,
        ogTitle: `${nameUppercase}`,
        description: `${recipe.value.description}`,
        ogDescription: `${recipe.value.description}`,
        ogImage: `${featuredImage}`
      });

    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }

  onMounted(() => {
    fetchRecipe();
  });
</script>

<template>
  <Header></Header>
  <div class="recipe-detail-page">
    <div class="hero-banner">
      <nav class="breadcrumbs">
        <ul>
          <li><NuxtLink to="/">Recipes</NuxtLink><span>/</span></li>
          <li>
            {{ recipe.name }}
          </li>
        </ul>
      </nav>
      <h1>{{ recipe.name }}</h1>
      <div class="by-line">
        <PageIcon></PageIcon>
        <p>{{ recipe.author_email }}</p>
      </div>
    </div>
    <main>
      <section class="details-container">
        <div class="details-content">
          <div class="description">
            <p>{{ recipe.description }}</p>
          </div>
          <div class="ingredients-container">
            <h2>Featured Ingredient</h2>
            <div class="price-container">
              <p>{{ protein }}</p>
              <p class="price">${{ price }}</p>
              <button>Add to Cart</button>
            </div>
            <h2>Ingredients</h2>
            <ul>
              <li 
                v-for="ingredient in recipe.ingredients"
                :key="ingredient.id"
              >
                {{ ingredient.qty }} {{ ingredient.unit }} of <span>{{ ingredient.name }}</span> 
              </li>
            </ul>
            <h2>Steps</h2>
            <ul>
              <li 
                v-for="(step, index) in recipe.steps"
                :key="index"
              >
                {{ step }}
              </li>
            </ul>
          </div>
        </div>
      </section>
      <section class="images-container">
        <div 
          v-for="(image, index) in recipe.images"
          class="square"
          :class="index"
          :key="index"
        >
          <img 
            :src="image"
            :key="index"
            class="recipe-image"
          >
        </div>
      </section>
    </main>
  </div>
  <Footer></Footer>
</template>

<style>
.breadcrumbs ul {
  margin: 0 auto;
  padding: 0;
  display: flex;
  width: fit-content;
}
.breadcrumbs li {
  margin: 0 5px;
  color: #fff;
  list-style-type: none;
  text-transform: capitalize;
}
.breadcrumbs li a {
  color: #fff;
}
.recipe-detail-page h1 {
  text-transform: capitalize;
}
.by-line, 
.author {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 30px auto 0;
}
.author {
  margin: 0;
  justify-content: start;
}
.by-line p {
  font-size: 14px;
  color: #fff;
  margin: 0 0 0 10px;
}
.details-container {
  width: 57%;
}
.details-content .author p {
  font-size: 14px;
  color: #0d334c;
}
.details-content .author svg path {
  fill: #0d334c;
}
.description p {
  margin: 0 0 15px;
  line-height: 26px;
}
.price-container {
  background-color: #fff;
  border-radius: 8px;
  padding: 20px;
  margin: 20px 0;
}
.price-container p {
  font-size: 20px;
  margin: 0 0 5px;
  font-weight: 500;
}
.price-container p.price {
  font-size: 16px;
  margin: 0 0 20px;
}
.ingredients-container {
  width: fit-content;
}
.ingredients-container ul {
  margin: 0;
  padding: 20px;
}
.ingredients-container li {
  margin: 10px 0;
  line-height: 26px;
}
.ingredients-container li span {
  font-weight: 500;
}
.recipe-detail-page main {
  max-width: 1200px;
  margin: 0 auto;
  display: flex;
  justify-content: space-evenly;
  padding: 40px 20px;
}
.images-container {
  width: 40%;
  height: fit-content;
  display: grid;
  grid-gap: 20px;
  grid-template-columns: repeat(2, 1fr);
}
.square:first-child {
  grid-column: 1 / 3;
}
.square:nth-child(2) {
  grid-column-start: 1;
}
.square:nth-child(3) {
  grid-column-start: 2;
}
.square {
  position: relative;
  overflow: hidden;
  border-radius: 8px;
}
.square::after {
  content: "";
  display: block;
  padding-bottom: 100%;
}
.square img {
  position: absolute;
  width: 100%;
  height: 100%;
  object-fit: cover;
  object-position: center;
}
@media (max-width: 1024px) {
  .recipe-detail-page main {
    flex-direction: column-reverse;
    padding: 20px;
  }
  .details-container, 
  .images-container {
    width: 100%;
  }
  .description {
    margin-top: 20px;
  }
}
@media (max-width: 767px) {
  .images-container {
    grid-gap: 15px;
  }
}
</style>