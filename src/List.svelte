<script>
	import { fly } from 'svelte/transition';

	const ENTER_KEY = 13;
	const ESCAPE_KEY = 27;

	let listName = {// will be pulling from db
		name: 'Default List',
		editing: false
	};
	let currentFilter = 'all';
	let newTask = '';
	let tempId = 1;
	let tasks = [];
	let inputText = '+ new task';

	const editListName = () => {
		listName.editing = true;
		listName = listName;
	}

	const doneEditListName = () => {
		listName.editing = false;
		listName = listName;
	}

	const doneEditKeydownListName = (e) => {
		if (event.which === ENTER_KEY) {
			doneEditListName(listName);
		}
	}
	// can't accept empty names
	const addTask = (e) => {
		if(e.which === ENTER_KEY) {
			tasks.push({
				id: tempId,
				completed: false,
				title: newTask,
				editing: false
			})

			tasks = tasks;
			tempId++;
			newTask = '';
		}
	}

	const addTaskBlur = () => {
		if (newTask !== '') {
			tasks.push({
				id:tempId,
				completed: false,
				title: newTask,
				editing: false
			})

			tasks=tasks;
			tempId++;
			newTask = '';
			inputText = '+ new task';
		}
		else {
			replaceText();
		}
	}

	const removePlaceholder = () => {
 		inputText = '';
	}

	const replaceText = () => {
		inputText = '+ new task';
	}

	const editTask = (task) => {
		task.editing = true;
		tasks = tasks;
	}

	// can't accept empty names
	const doneEdit = (task) => {
		task.editing = false;
		tasks = tasks;
	}

	const doneEditKeydown = (task, e) => {
		if (event.which === ENTER_KEY) {
			doneEdit(task);
		}
	}

	const deleteTask = (id) => {
		tasks = tasks.filter(task => task.id !== id);
	}

	const checkAllTasks = (e) => {
		tasks.forEach(task => task.completed = event.target.checked);
		tasks = tasks;
	}

	const clearCompleted = () => {
		tasks = tasks.filter(task => !task.completed);
	}

	const updateFilter = (filter) => {
		currentFilter = filter;
    }
    
    const saveList = () => {
        //save to firebase 
        /* db.collection("list").add(
			Tasks: tasks,
			User: firebase.auth().currentUser,
			Name: Task List
        )
        */
    }

	$: tasksLeft = tasks.filter(task => !task.completed).length;

	$: filteredTasks = currentFilter === 'all'
		? tasks
		: currentFilter === 'completed'
			? tasks.filter(task => task.completed)
			: tasks.filter(task => !task.completed);
</script>

<style>
	.container {
		max-width: 600px;
		margin: 0 auto;
		padding: 20px;
	}

	.logo {
		display: block;
		margin: 20px auto;
	}

	.input-container {
		position: sticky;
		position: -webkit-sticky;
		bottom: 80px;
	}

	.task-input {
		width: 100%;
		padding: 10px 18px;
		font-size: 24px;
		margin-bottom: 16px;
		border: none;
	
	}

	input[type="checkbox"] {
		-webkit-transform: scale(2);
		transform: scale(2);
		padding: 10px;
	}

	.task-item {
		margin-bottom: 12px;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.remove-item {
		cursor: pointer;
		margin-left: 14px;
		font-size: 24px;
	}

	.remove-item:hover {
		color: grey;
	}

	.task-item-left {
		display: flex;
		align-items: center;
	}

	.task-item-label {
		font-size: 24px;
		padding: 10px;
		border: 1px solid white;
		margin-left: 12px;
		margin-bottom: 12px;
		overflow-wrap: break-word;
	}

	.task-item-edit,
	.list-name-edit {
		font-size: 24px;
		margin-left: 12px;
		width: 100%;
		padding: 10px;
		border: 1px solid #ccc;
		font-family: Helvetica, Arial, sans-serif;
	}

	.task-item-edit:focus {
		outline:none;
	}

	.completed {
		text-decoration: line-through;
		color: grey;
	}

	.button-container {
		display:flex;
		align-items: center;
		justify-content: space-between;
		font-size: 20px;
		padding: 14px 0px;
		margin-bottom: 10px;
		background: white;
		position: sticky;
		position: -webkit-sticky;
		bottom: 0;
	}

	.button-container input {
		margin-right: 16px;
	}

	button {
		font-size: 18px;
		background-color: white;
		border-radius: 5px;
		appearance: none;				
	}

	button:hover {
		background: #ee8152;
		color: white;
	}

	button:focus {
		outline: none;
	}

	.active {
		background: #ee8152;
		color: white;
	}

	@media screen and (max-width: 400px) {
		.logo {
			width: 250px;
		}

		button {
			padding: 12px;
			font-size: 20px;
		}
	}
</style>


<main>

	<div class ="container">

		<img src={'/img/worktomate.png'} alt="worktomate logo" class="logo">

		{#if !listName.editing}
				<h1 class="list-name"
				on:dblclick={() => editListName(listName)}>{listName.name}</h1>
			{:else}
				<input class="list-name-edit" bind:value={listName.name} type="text"
				on:blur={doneEditListName} on:keydown={doneEditKeydownListName}>
			{/if}

		{#each filteredTasks as task}
			<div class="task-item">
				<div class="task-item-left" transition:fly="{{ y: 20, duration: 300 }}">
					<input class="task-checkbox" type="checkbox" bind:checked={task.completed}>
					{#if !task.editing}
						<div class="task-item-label" class:completed={task.completed}
						on:dblclick={() => editTask(task)}>{task.title}</div>
					{:else}
						<input class="task-item-edit" bind:value={task.title} type="text"
						on:blur={() => doneEdit(task)} on:keydown={() => doneEditKeydown(task)}>
					{/if}
				</div>
				<div class="remove-item" on:click={() => deleteTask(task.id)}>
					&times;
				</div>
			</div>
		{/each}

		<div class="input-container">
			<input type="text" class="task-input" placeholder={inputText} on:focus={removePlaceholder}
			 on:blur={addTaskBlur} bind:value={newTask} on:keydown={addTask}>
		</div>

		<div class="button-container">
			<div><label><input type="checkbox" on:change={checkAllTasks}>Check All</label></div>
			<div>{tasksLeft} tasks left</div>
		</div>

		<div class="button-container">
			<div>
				<button
					on:click="{() => updateFilter('all')}"
					class:active="{currentFilter === 'all'}"
				>All</button>
				<button 
					on:click="{() => updateFilter('active')}"
					class:active="{currentFilter === 'active'}"
				>In Progress</button>
				<button
					on:click="{() => updateFilter('completed')}"
					class:active="{currentFilter === 'completed'}"
				>Completed</button>
			</div>
		</div>

		<div>
			<button on:click={clearCompleted}>Clear Completed</button>
		</div>

		<div>
			<button on:click={saveList}>Save List</button>
		</div>

	</div>
</main>
