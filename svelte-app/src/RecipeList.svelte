<script>
  import { onMount } from 'svelte';
  import { API_BASE_URL } from './constants.js';
  import {navigate} from 'svelte-routing';
  

  let recipes = [];
  let error = '';

  
  onMount(async () => {
    await loadAllRecipes();
  });

  async function loadAllRecipes(){
    try {
      const response = await fetch(`${API_BASE_URL}/recipes`, {
        method: 'GET',
        headers: {
          'Content-Type': 'application/json',
        },
        credentials: 'include',
      });
      if (!response.ok) {
        throw new Error('Failed to fetch recipes');
      }
      recipes = await response.json();
    } catch (err) {
      error = err.message;
    }
  }

    // Function to handle recipe click
  function viewRecipe(recipeId) {
    navigate(`/recipes/${recipeId}`); // Use navigate to handle route change
  }

  // Navigate to the recipe creation page
  function createNewRecipe() {
    navigate('/recipe/create');
  }
    
</script>

<div class="container mt-4">
  
   <!-- Header with Add Recipe Button -->
   <div class="header d-flex align-items-center mb-4">
    <h1 class="me-3">All Recipes</h1> <!-- Added margin to separate the button -->
    <button
      class="btn btn-success btn-lg rounded-circle add-recipe-btn"
      on:click={createNewRecipe}
      title="Add New Recipe"
    >
      +
    </button>
  </div>


  <!-- Error Message -->
  {#if error}
    <div class="alert alert-danger">
      {error}
    </div>
  {/if}
  
  <!-- Recipes List -->
  <div class="row">
    {#each recipes as recipe}
      <div class="col-md-4 mb-4">
        <div class="card h-100">
          <img
            src={'/default-recipe-image.jpg'}
            class="card-img-top"
            alt={recipe.title}
          />
          <div class="card-body">
            <h5 class="card-title">{recipe.title}</h5>
            <p class="card-text">{recipe.description}</p>
            <button class="btn btn-primary" on:click={() => viewRecipe(recipe.id)}>View Recipe</button>
          </div>
        </div>
      </div>
    {/each}
  </div>
</div>

<style>
  .card-img-top {
      height: 200px;
      object-fit: cover;
  }

  .card {
      transition: transform 0.3s, box-shadow 0.3s;
  }

  .card:hover {
      transform: scale(1.05);
      box-shadow: 0 4px 20px rgba(0, 0, 0, 0.2);
  }

  .header {
      display: flex;
      align-items: center;
    }

    .me-3 {
      margin-right: 1rem; /* Adjust spacing between the heading and button */
    }

    .add-recipe-btn {
    font-size: 1.5rem;
    width: 50px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.2);
    border: none;
    color: white;
    background-color: #28a745;
  }

  .add-recipe-btn:hover {
    background-color: #218838;
  }
</style>
  