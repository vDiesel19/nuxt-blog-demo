<script setup>
  const baseURL = 'http://localhost:8888/api/recipes';
  let recipes = ref([]);
  let filteredRecipes = ref([]);
  let filtersActive = ref(false);
  let showFilterDrawer = ref(false);
  let selectedFilters = ref({});
  let currentFilter = ref('');
  const authors = ['brigitte90@example.com', 'dubuque.luther@example.net', 'maximillian.douglas@example.com', 'bins.vinnie@example.com', 'jeffery.schuppe@example.com'];
  const ingredients = ['Wild Alaska Pollock', 'Wild Alaskan Sablefish', 'Wild Alaskan Sockeye', 'Wild Alaskan Cod','Wild Alaskan Lingcod', 'Wild Alaskan Rockfish', 'Wild Alaskan Halibut', 'Wild Alaskan Coho'];
  let currentPage = ref(0);
  let moreRecipesAvailable = ref(true);

  function setFilteredRecipes() {
    // filter new array of recipes to preserve loaded data
    let recipesToFilter = recipes.value;
    let activeFilters = Object.keys(selectedFilters.value).length;

    if (!activeFilters) {
      return filteredRecipes.value = recipes.value;
    }

    if (activeFilters > 1) {
      const author = selectedFilters.value['author_email'];
      const ingredient = selectedFilters.value['ingredients'];

      filteredRecipes.value = recipesToFilter.filter(recipe => {
        return recipe.ingredients[0].name.includes(ingredient) && recipe['author_email'] === author;
      });

    } else {
      let filterValue = selectedFilters.value[currentFilter.value];

      filteredRecipes.value = recipesToFilter.filter(recipe => {
        if (currentFilter.value === 'ingredients') {
          return recipe.ingredients[0].name.includes(filterValue);
        } else {
          return recipe['author_email'] === filterValue;
        }
      });
    }
  }

  function getSearchParams() {
    const searchParams = new URLSearchParams(baseURL);

    if (currentPage.value > 0) {
      searchParams.append("page", (currentPage.value + 1).toString());
    }

    for (const [key, value] of Object.entries(selectedFilters.value)) {
      let param = `search[${key}]`;

      if (key === 'ingredients') {
        param = `search[${key}][0]`;
      }

      searchParams.append(param, value);
    }

    return searchParams.toString();
  }
  
  async function fetchRecipes() {
    const searchQuery = getSearchParams();

    if (!moreRecipesAvailable.value) return;

    try {
      const response = await fetch(`${baseURL}?${searchQuery}`);
      const data = await response.json();
      const lastPage = data.meta.last_page;

      if (currentPage.value <= lastPage) {
        // push new set of data into recipes array
        recipes.value = [...recipes.value, ...data.data];

        currentPage.value++;
        setFilteredRecipes();
        
      } else {
        moreRecipesAvailable.value = false;
      }

    } catch (error) {
      console.error('Error fetching data:', error);
    }
  }

  function setActiveFilters(event) {
    const filterKey = event.target.name;
    let filterValue = event.target.value;
    filtersActive.value = true;

    if (filterKey === 'ingredients') {
      filterValue = filterValue.split(' ').pop();
    }

    selectedFilters.value[filterKey] = filterValue;
    currentFilter.value = filterKey;
    setFilteredRecipes();
  }

  function clearMobileFilters() {
    document.querySelectorAll('.recipe-filters input').forEach(radioBtn => {
      radioBtn.checked = false;
    })
    selectedFilters.value = {};
    filtersActive.value = false;
    setFilteredRecipes();
  }

  function clearFilters() {
    document.querySelectorAll('.recipe-filters input').forEach(radioBtn => {
      radioBtn.checked = false;
    })
    selectedFilters.value = {};
    filtersActive.value = false;
    setFilteredRecipes();
    toggleFilterDrawer();
  }

  function createObserver() {
    let observer;
    let el = document.getElementById('scroll-trigger');

    let options = {
      root: null,
      rootMargin: "0px",
      threshold: 1.0,
    };

    observer = new IntersectionObserver(fetchRecipes, options);
    observer.observe(el);
  }

  function toggleFilterDrawer() {
    showFilterDrawer.value = !showFilterDrawer.value;
  }

  onMounted(() => {
    fetchRecipes();
    setTimeout(createObserver, 2000);
  });
</script>

<template>
  <Header></Header>
  <div class="hero-banner hero-banner-rlp">
    <h1>Recipes</h1>
  </div>
  <main class="recipes-container">
    <section class="recipe-filters" :class="{ active: showFilterDrawer }">
      <div class="filters-container">
        <div class="filters-top-btns">
          <button 
            class="filter-btn"
            :class="{ active: filtersActive }"
            :disabled="!filtersActive"
            @click="clearFilters"
          >
            Clear Filters
          </button>
          <button class="close-btn" @click="toggleFilterDrawer">X</button>
        </div>
        <p class="filters-header">Filter by:</p>
        <fieldset>
          <legend>Ingredients</legend>
          <div 
            class="filter-wrapper" 
            v-for="(ingredient, index) in ingredients" 
            :key="index"
          >
            <input 
              @click="setActiveFilters" 
              type="radio" 
              :id="ingredient" 
              name="ingredients" 
              :value="ingredient" 
            />
            <label :for="ingredient">{{ ingredient }}</label>
          </div>
        </fieldset>
        <fieldset>
          <legend>Author</legend>
          <div 
            class="filter-wrapper" 
            v-for="(author, index) in authors" 
            :key="index"
          >
            <input 
              @click="setActiveFilters"
              type="radio" 
              :id="author" 
              name="author_email" 
              :value="author" />
            <label :for="author">{{ author.split('@')[0] }}</label>
          </div>
        </fieldset>
        <button 
          class="filter-btn apply-btn"
          :class="{ active: filtersActive }"
          :disabled="!filtersActive"
          @click="toggleFilterDrawer"
        >
          Apply Filters
        </button>
      </div>
    </section>
    <section class="recipe-grid-container">
      <div class="results-wrapper">
        <div class="mobile-results-header">
          <p 
            v-if="filtersActive"
            class="clear-text"
            @click="clearMobileFilters"
          >
            Clear Filters
          </p>
          <p class="results-label">
            Showing 
            <span>
              {{ filteredRecipes.length }}
            </span>
            of 
            <span>
              {{ recipes.length }}
            </span> 
            results
          </p>
        </div>
        <button class="mobile-filter-btn" @click="toggleFilterDrawer">
          <span>Filter</span>
          <FilterIcon></FilterIcon>
        </button>
      </div>
      <ul class="recipe-grid">
        <RecipeCard 
          v-for="recipe in filteredRecipes" 
          :key="recipe.id" 
          :recipe="recipe" 
        />
      </ul>
    </section>
  </main>
  <div id="scroll-trigger"></div>
  <Footer></Footer>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;500&family=Roboto&family=Roboto+Serif:opsz@8..144&display=swap');

</style>