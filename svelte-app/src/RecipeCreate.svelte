<script>
    import { navigate } from 'svelte-routing';
    import { API_BASE_URL } from './constants.js';
  
    let title = '';
    let description = '';
    let ingredients = '';
    let steps = '';
    let error = '';
    let successMessage = '';
  
    // Handle form submission to create a new recipe
    async function createRecipe() {
      try {
        const response = await fetch(`${API_BASE_URL}/recipes`, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({
            title,
            description
          }),
          credentials: 'include',
        });
  
        if (!response.ok) {
          throw new Error('Failed to create recipe');
        }
  
        const createdRecipe = await response.json();
        successMessage = 'Recipe created successfully!';
        // Redirect to the new recipe page
        navigate(`/recipes/${createdRecipe.id}`);
      } catch (err) {
        error = err.message;
      }
    }
  </script>
  
  <div class="container mt-4">
    <h1>Create New Recipe</h1>
    
    {#if error}
      <div class="alert alert-danger">
        {error}
      </div>
    {/if}
  
    {#if successMessage}
      <div class="alert alert-success">
        {successMessage}
      </div>
    {/if}
  
    <form on:submit|preventDefault={createRecipe}>
      <div class="mb-3">
        <label for="title" class="form-label">Recipe Title</label>
        <input
          type="text"
          id="title"
          class="form-control"
          bind:value={title}
          required
        />
      </div>
  
      <div class="mb-3">
        <label for="description" class="form-label">Description</label>
        <textarea
          id="description"
          class="form-control"
          rows="3"
          bind:value={description}
          required
        ></textarea>
      </div>
  
      <div class="mb-3">
        <label for="ingredients" class="form-label">Ingredients (comma separated)</label>
        <input
          type="text"
          id="ingredients"
          class="form-control"
          bind:value={ingredients}
          required
        />
      </div>
  
      <div class="mb-3">
        <label for="steps" class="form-label">Steps (one per line)</label>
        <textarea
          id="steps"
          class="form-control"
          rows="5"
          bind:value={steps}
          required
        ></textarea>
      </div>
  
      <button type="submit" class="btn btn-success">Create Recipe</button>
    </form>
  </div>
  
  <style>
    .container {
      max-width: 600px;
      margin: auto;
    }
  
    .form-label {
      font-weight: bold;
    }
  
    .form-control {
      margin-bottom: 1rem;
    }
  
    .btn {
      width: 100%;
    }
  </style>
  