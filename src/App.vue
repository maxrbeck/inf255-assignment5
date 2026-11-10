<script setup>
/*
 * Animal Adoption Board
 *
 * EXTRA CREDIT: If you recorded the video walkthrough (see README.md), paste
 * its link here. A video that is not linked here will not be graded.
 *
 *   Video link:
 */

import { ref, computed } from "vue";
import AnimalCard from "./components/AnimalCard.vue";

// ------------------------------------
// App Data
// ------------------------------------

// App is the only component that owns and changes this data. ref() makes
// each value reactive, so the template redraws when it changes.
const animals = ref([]);
const loading = ref(false);
// Empty string means "no error"; any text here is shown to the user.
const error = ref("");

// ------------------------------------
// Load the Animals
// ------------------------------------

// Same request as Assignment 4, but it only updates the refs. The template
// decides what to show based on loading, error, and animals.
async function loadAnimals() {
	loading.value = true;
	error.value = "";

	try {
		const response = await fetch("/animals.json");

		// A 404 still counts as a response, so fetch() won't throw on its own.
		if (!response.ok) {
			throw new Error(`Request failed with status ${response.status}`);
		}

		animals.value = await response.json();
	} catch (err) {
		// Named err because error is already the ref above.
		console.error(err);
		error.value =
			"Sorry, the animals couldn't be loaded. Try refreshing the page.";
	}

	loading.value = false;
}

loadAnimals();

// ------------------------------------
// Adopt or Return an Animal
// ------------------------------------

// TODO 3: Write toggleAdoption(animal).

// ------------------------------------
// Filter, Sort, and Count
// ------------------------------------

// TODO 4: Create activeFilter and activeSort, the visibleAnimals and
// availableCount computed properties, and setFilter() and setSort().
</script>

<template>
	<header>
		<h1>Animal Adoption Board</h1>
		<p>Meet the animals currently staying at our shelter.</p>
	</header>

	<main>
		<section aria-labelledby="animals-heading">
			<h2 id="animals-heading">Animals</h2>

			<!-- TODO 4: The available count goes here. -->

			<!-- TODO 4: The AnimalControls component goes here. -->

			<div id="animal-list" aria-live="polite">
				<!-- Only one of these shows at a time, depending on the request's state. -->
				<p v-if="loading">Loading animals...</p>
				<p v-else-if="error" class="error-message">{{ error }}</p>
				<div v-else>
					<!-- One card per animal; :animal passes this loop's animal down as a prop. -->
					<AnimalCard v-for="animal in animals" :key="animal.id" :animal="animal" />
				</div>
			</div>
		</section>
	</main>
</template>
