<script>
    import { onMount } from 'svelte';
    import { API_BASE_URL } from './constants.js';
    import { navigate } from 'svelte-routing';
    import { Star } from 'svelte-bootstrap-icons'; // Import the Star icon from the library
    export let id;
  
    let recipe = null;
    let error = '';
    let steps = [];
  
    onMount(async () => {
      //await loadRecipeData();
      //await loadRecipeSteps();
      await Promise.all([loadRecipeData(), loadRecipeSteps()]);
    });

    async function loadRecipeData(){
      try {
          const response = await fetch(`${API_BASE_URL}/recipes/${id}`, {
          method: 'GET',
          headers: {
            'Content-Type': 'application/json',
          },
          credentials: 'include',
        });
        if (!response.ok) {
          throw new Error('Recipe not found');
        }
        recipe = await response.json();
      } catch (err) {
        error = err.message;
      }
    }
  
    async function deleteRecipe() {
      try {
        const response = await fetch(`${API_BASE_URL}/recipes/${id}`, {
          method: 'DELETE',
          headers: {
            'Content-Type': 'application/json',
          },
          credentials: 'include',
        });
        if (!response.ok) {
          throw new Error('Failed to delete recipe');
        }
        //navigate('/recipes');
      } catch (err) {
        error = err.message;
      }
    }

    async function loadRecipeSteps(){
      try {
        const response = await fetch(`${API_BASE_URL}/recipes/${id}/steps`);
        if (!response.ok) {
          throw new Error('Recipe not found');
        }
        steps = await response.json();
      } catch (err) {
        error = err.message;  
      }
    }

    // Helper function to format duration (e.g., "00:10:00" -> "10 minutes")
  function formatDuration(duration) {
    const parts = duration.split(':');
    const hours = parseInt(parts[0]);
    const minutes = parseInt(parts[1]);
    const seconds = parseInt(parts[2]);
    let formatted = '';
    if (hours > 0) formatted += `${hours} hours `;
    if (minutes > 0) formatted += `${minutes} minutes `;
    if (seconds > 0) formatted += `${seconds} seconds`;
    return formatted.trim();
  }

  </script>
  
  <!-- Page Layout -->
<div class="container mt-4">
  {#if error}
    <div class="alert alert-danger">{error}</div>
  {:else if recipe}
    <div class="row">
      <!-- Back Button -->
      <div class="col-md-2 mb-4">
        <button class="btn btn-secondary" on:click={() => navigate('/recipes')}>Back to All Recipes</button>
      </div>

      <!-- Recipe Image -->
      <div class="col-md-6 mb-4">
        <img
          src={'/default-recipe-image.jpg'}
          alt={recipe.title}
          class="img-fluid rounded shadow"
        />
      </div>

      <!-- Recipe Info -->
      <div class="col-md-6 mb-4">
        <h1>{recipe.title}</h1>
        <!-- Rating Display -->
        <div class="rating">
          {#each Array(5) as _, index}
            <Star
              class="star"
              style="color: {index < recipe.rating ? '#ffd700' : '#ccc'}; font-size: 50px;"
            />
          {/each}
        </div>
        <p>{recipe.description}</p>
        <div class="col-md-4 mb-4">
          <button class="btn btn-danger" on:click={deleteRecipe}>Delete Recipe</button>
          <button class="btn btn-primary" on:click={navigate(`/recipes/${id}/cook`)}>Start this recipe</button>
        </div>
        <div class="col-md-2 mb-4">
          
        </div>
      </div>
    </div>

    <!-- Steps Section -->
    <h2>Steps</h2>
    <ol>
      {#each steps as step (step.id)}
        <li>
          <p><strong>Step {step.id}:</strong> {step.text}</p>
          <p><small>Duration: {formatDuration(step.duration)}</small></p>
        </li>
      {/each}
    </ol>
  {/if}
</div>


  
<style>
  /* Ensure the image is responsive and looks good on all screen sizes */
  .img-fluid {
    width: 100%;
    height: auto;
    object-fit: cover;
  }

  /* Card-style effect for the image */
  .shadow {
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }

  /* Make the heading more prominent */
  h1 {
    font-size: 2rem;
    font-weight: 600;
  }

  /* Add some margin to the description for better spacing */
  p {
    font-size: 1.1rem;
    line-height: 1.6;
    color: #555;
  }

  /* Add spacing and style the button */
  button {
    margin-top: 20px;
  }

  /* Rating stars styling */
  .rating {
    display: flex;
  }

  /* Style the individual stars if needed */
  .star {
    margin-right: 5px;
  }

  /* Add responsive design for mobile devices */
  @media (max-width: 768px) {
    .col-md-6 {
      margin-bottom: 20px;
    }
    h1 {
      font-size: 1.6rem;
    }
    p {
      font-size: 1rem;
    }
      /* Make the stars responsive */
    .rating .star {
      font-size: 20px; /* smaller on mobile */
    }
  }
    /* Increase the size for larger screens */
  @media (min-width: 768px) {
    .rating .star {
      font-size: 30px; /* standard size on larger screens */
    }
  }

  /* Styling adjustments for steps */
  ol {
    margin-top: 20px;
    padding-left: 20px;
  }

  li {
    margin-bottom: 15px;
    line-height: 1.5;
  }

  p {
    margin: 0;
  }

  small {
    color: #555;
  }
</style>