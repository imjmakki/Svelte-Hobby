<script>
	// --- imports as ---
	import { onMount } from 'svelte';
	import Hobby from './Hobby.svelte';
	import Button from './Button.svelte';
	import { saveToLS, readFromLS } from './storage';
	import { generate } from 'shortid';

	// --- private variables ---
	let id;
	let name = '';
	let weight = 3;
	let description = '';
	let hobbies = JSON.parse(readFromLS('hobbies')) || [];
	let isEditMode = false;

	let elmToFocus;
	onMount(function() {
		elmToFocus.focus();
	});

	// Define the look of a Hobby based on the weight/rating
	const getHobbyLook = (weight) => {
		let background = '#ff6b6b';
		let color = '#FFFFFF';
		if (weight > 3 && weight <= 6) {
			background = '#efef04';
			color = '#000';
		} else if (weight > 6) {
			background = '#0eb30e';
			color = '#FFFFFF';
		}
		return {background: background, color: color};
	}

	// Method to add a hobby
	const addHobby = () => {
		const look = getHobbyLook(weight);

		if (isEditMode) {
			editHobby(id, name, weight, description, look);

		} else {
			const hobby = {
				id: generate(),
				name : name,
				weight : weight,
				description: description,
				look: look
			};
			hobbies = hobbies.concat(hobby);
			resetAndSave(hobbies);
		}
	}

	// Method to delete a hobby
	const deleteHobby = id => {
		console.log('hobby to delete', id);
		//find hobby by name
		let index = hobbies.findIndex(hobby => hobby.id === id);
		//remove hobby
		hobbies.splice(index, 1);
		hobbies = [...hobbies];
		console.log('hobbies after delete', JSON.stringify(hobbies));
		resetAndSave(hobbies);
	};

	// Method to edit a hobby
	const editHobby = (id, newName, newWeight, newDescription, newLook) => {
		console.log('hobby to edit', name);
		//find hobby by name
		let index = hobbies.findIndex(hobby => hobby.id === id);
		//edit hobby
		hobbies[index].name = newName;
		hobbies[index].weight = newWeight;
		hobbies[index].description = newDescription;
		hobbies[index].look = newLook;
		hobbies = [...hobbies];
		console.log('hobbies after edit', hobbies);
		resetAndSave(hobbies);
	};

	// Set the edit mode
	const editMode = (hobbyId) => {
		console.log('hobby to edit', name);
		//find hobby by name
		let hobby = hobbies.find(hobby => hobby.id === hobbyId);
		id = hobby.id;
		name = hobby.name;
		weight = hobby.weight;
		description = hobby.description;
		isEditMode = true;
	}

	// Method to reset the hobby form
	const reset = () => {
		id = '';
		name = '';
		weight = 3;
		description = '';
		isEditMode = false;
	}

	// Method to reset and save
	const resetAndSave = hobbies => {
		reset();
		saveToLS('hobbies', hobbies);
	}
</script>

<main>
	<h1>Hello {name}!</h1>
	<p>Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn how to build Svelte apps.</p>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
