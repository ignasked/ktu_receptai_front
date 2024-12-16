<script>
	import "bootstrap/dist/css/bootstrap.min.css";
	//import '/fonts/bootstrap-icons.css';
	//import 'https://cdn.jsdelivr.net/npm/bootstrap-icons/font/bootstrap-icons.css';
	//import './assets/css/bootstrap-icons.css';
	import { API_BASE_URL } from './constants.js';
	import { Router, Route, Link, navigate} from "svelte-routing";
	import Home from "./Home.svelte";
	import Login from './Login.svelte';
	import Register from "./Register.svelte";
	import RecipeList from "./RecipeList.svelte";
	import RecipeView from "./RecipeView.svelte";
	import RecipeCook from "./RecipeCook.svelte";
	import RecipeCreate from "./RecipeCreate.svelte";

	async function logout() {
		try {
			// Call your API to log the user out
			const response = await fetch(`${API_BASE_URL}/logout`, {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json',
			},
			credentials: 'include', // Include cookies if you're using them for session
			});

			if (!response.ok) {
			throw new Error('Failed to log out');
			}

			// Optionally, clear cookies if using cookies for authentication
			//document.cookie = 'authToken=; Max-Age=0'; // Clear the authToken cookie if used

			// Redirect to the login page
			//navigate('/login');
		} catch (err) {
			console.error('Logout failed:', err);
		}
	}
</script>


<Router>
	<nav>
		<Link to="/">Home</Link>
		<Link to="/login">Login</Link>
		<button on:click={logout}>Logout</button>
		<Link to="/register">Register</Link>
		<Link to="/recipes">Recipes</Link>
	</nav>

	<Route path="/" component={Home} />
	<Route path="*" component={Home} />
	<Route path="/login" component={Login} />
	<Route path="/register" component={Register} />
	<Route path="/recipes" component={RecipeList} />
	<Route path="/recipes/:id" component={RecipeView} />
	<Route path="/recipes/:id/cook/" component={RecipeCook} />
	<Route path="/recipe/create/" component={RecipeCreate} />

</Router>
