<script setup>
import dayjs from "dayjs";

// App hands each card one animal object to display.
defineProps({
	animal: Object,
});

// The card doesn't flip animal.adopted itself because the animal isn't its
// data. App owns the animals array and just lends each card one animal as a
// prop, which the card should treat as read-only. If the cards (or any other
// component) could change the data too, there'd be several places editing the
// same thing, and when something looked wrong you couldn't tell which
// component changed it. So the card only emits "toggle" to say its button was
// clicked, and App, as the one owner, makes the change.
const emit = defineEmits(["toggle"]);

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
		<!-- The card only reports the click; App decides what to change. -->
		<button type="button" @click="emit('toggle')">{{ animal.adopted ? "Return" : "Adopt" }}</button>
	</article>
</template>
