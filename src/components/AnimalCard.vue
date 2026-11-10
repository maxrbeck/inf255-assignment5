<script setup>
import dayjs from "dayjs";

// App hands each card one animal object to display.
defineProps({
	animal: Object,
});

// TODO 3: Declare the toggle event with defineEmits().

// Turns "2026-08-20" into "Arrived Aug 20, 2026 (N days ago)", same Day.js
// calls as Assignment 4, just returned as text for the template.
function arrivalText(intakeDate) {
	const arrived = dayjs(intakeDate);
	return `Arrived ${arrived.format("MMM D, YYYY")} (${dayjs().diff(arrived, "day")} days ago)`;
}
</script>

<template>
	<!-- The adopted class grays the card out (see style.css). -->
	<article class="animal-card" :class="{ adopted: animal.adopted }">
		<h3>{{ animal.name }}</h3>
		<p>{{ animal.species }}, {{ animal.age }} years old</p>
		<p>{{ arrivalText(animal.intakeDate) }}</p>
		<p>{{ animal.adopted ? "Adopted" : "Available" }}</p>
		<button type="button">{{ animal.adopted ? "Return" : "Adopt" }}</button>
	</article>
</template>
