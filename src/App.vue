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
import AnimalControls from "./components/AnimalControls.vue";

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

// Runs when a card emits "toggle". No redraw call needed: animal lives inside
// the animals ref, so Vue notices the change and updates that card.
function toggleAdoption(animal) {
	animal.adopted = !animal.adopted;
}

// ------------------------------------
// Filter, Sort, and Count
// ------------------------------------

// What the user currently wants to see. AnimalControls asks App to change these.
const activeFilter = ref("all");
const activeSort = ref("default");

// This computed property replaces updateDisplay() from Assignment 4. Instead
// of drawing the cards, it just returns the list that should be shown. Vue
// keeps track of which refs it read (animals, activeFilter, activeSort), and
// any time one of them changes, like adopting an animal or clicking a filter,
// Vue recalculates it and redraws the cards on its own. That's why nothing
// has to be called after a change anymore.
const visibleAnimals = computed(() => {
	// Copy first so sort() doesn't reorder the real array; "Original order"
	// needs it untouched.
	let visible = animals.value.slice();

	if (activeFilter.value === "available") {
		visible = visible.filter((a) => !a.adopted);
	} else if (activeFilter.value === "adopted") {
		visible = visible.filter((a) => a.adopted);
	}

	if (activeSort.value === "name") {
		visible.sort((a, b) => a.name.localeCompare(b.name));
	} else if (activeSort.value === "age") {
		// Negative result puts a first, so youngest animals come first.
		visible.sort((a, b) => a.age - b.age);
	}

	return visible;
});

// Counts from all animals, not visibleAnimals, so filtering doesn't change it.
const availableCount = computed(
	() => animals.value.filter((a) => !a.adopted).length,
);

// Called with the value AnimalControls emits ("all", "name", etc.).
function setFilter(filter) {
	activeFilter.value = filter;
}

function setSort(sort) {
	activeSort.value = sort;
}
</script>

<template>
	<header>
		<h1>Animal Adoption Board</h1>
		<p>Meet the animals currently staying at our shelter.</p>
	</header>

	<main>
		<section aria-labelledby="animals-heading">
			<h2 id="animals-heading">Animals</h2>

			<!-- Hidden while loading or after an error, since there's nothing to count. -->
			<p v-if="!loading && !error" id="animal-count">
				{{ availableCount }} of {{ animals.length }} animals are available for adoption.
			</p>

			<!-- activeFilter goes down so it can highlight the right button; the
			     chosen filter or sort comes back up as an event. -->
			<AnimalControls
				:active-filter="activeFilter"
				@filter="setFilter"
				@sort="setSort"
			/>

			<div id="animal-list" aria-live="polite">
				<!-- Only one of these shows at a time, depending on the request's state. -->
				<p v-if="loading">Loading animals...</p>
				<p v-else-if="error" class="error-message">{{ error }}</p>
				<p v-else-if="visibleAnimals.length === 0" class="empty-message">
					There are no animals to show.
				</p>
				<div v-else>
					<!-- One card per animal; :animal passes this loop's animal down as a prop. -->
					<!-- @toggle uses this loop's animal, so each card's click changes its own animal. -->
					<AnimalCard
						v-for="animal in visibleAnimals"
						:key="animal.id"
						:animal="animal"
						@toggle="toggleAdoption(animal)"
					/>
				</div>
			</div>
		</section>
	</main>
</template>
