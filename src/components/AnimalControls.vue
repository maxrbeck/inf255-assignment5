<script setup>
// App owns activeFilter; this component only reads it to highlight a button.
defineProps({
	activeFilter: String,
});

// These tell App what the user picked. App is the one that changes the data.
const emit = defineEmits(["filter", "sort"]);

// Vue passes the browser's change event; event.target is the <select>, so its
// value is the sort the user just chose.
function onSortChange(event) {
	emit("sort", event.target.value);
}
</script>

<template>
	<div class="controls">
		<!-- Each button sends its filter name up with the event. -->
		<div class="filters" role="group" aria-label="Filter animals">
			<button
				type="button"
				:class="{ active: activeFilter === 'all' }"
				@click="emit('filter', 'all')"
			>
				All
			</button>
			<button
				type="button"
				:class="{ active: activeFilter === 'available' }"
				@click="emit('filter', 'available')"
			>
				Available
			</button>
			<button
				type="button"
				:class="{ active: activeFilter === 'adopted' }"
				@click="emit('filter', 'adopted')"
			>
				Adopted
			</button>
		</div>

		<label for="sort-animals">Sort by:</label>
		<select id="sort-animals" @change="onSortChange">
			<option value="default">Original order</option>
			<option value="name">Name</option>
			<option value="age">Age</option>
		</select>
	</div>
</template>
