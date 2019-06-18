<script>
	import { fly } from 'svelte/transition';

	const ENTER_KEY = 13;
	const ESCAPE_KEY = 27;

	let currentFilter = 'all';
	let newTodo = '';
	let tempId = 4;

	let todos = [
		{
			id: 1,
			completed: false,
			title: 'Go to Store',
			editing: false,
		},
		{
			id: 2,
			completed: false,
			title: 'Finish Svelte Screencast',
			editing: false,
		},
		{
			id: 3,
			completed: false,
			title: 'Take over world',
			editing: false,
		}
	];

	function addTodo(event) {
		if (event.which === ENTER_KEY) {
			todos = [...todos, {
				id: tempId,
				completed: false,
				title: newTodo,
				editing: false,
			}];

			tempId += 1;
			newTodo = '';
		}
	}

	function editTodo(todo) {
		todo.editing = true;
		todos = todos;
	}

	function doneEdit(todo) {
		todo.editing = false;
		todos = todos;
	}

	function doneEditKeydown(todo, event) {
		if (event.which === ENTER_KEY) {
			doneEdit(todo);
		}
	}

	function deleteTodo(id) {
		todos = todos.filter(todo => todo.id !== id);
	}

	function clearCompleted() {
		todos = todos.filter(todo => !todo.completed);
	}

	function checkAllTodos(event) {
		todos.forEach(todo => todo.completed = event.target.checked);
		todos = todos;
	}

	function updateFilter(filter) {
		currentFilter = filter;
	}

	$: todosRemaining = todos.filter(todo => !todo.completed).length;

	$: filteredTodos = currentFilter === 'all'
		? todos
		: currentFilter === 'completed'
			? todos.filter(todo => todo.completed)
			: todos.filter(todo => !todo.completed);

</script>

<!-- Styles -->
<style>
	.container {
		max-width: 35rem;
		margin: 0 auto;
	}
		.container input[type=checkbox] {
			width: 1rem;
			height: 1rem;
		}

	.logo {
		display: block;
		margin: 1.5rem auto;
		height: 5rem;
	}

	.todo-input {
		width: 100%;
		padding: .5rem 1rem;
		font-size: 1rem;
		margin-bottom: 1rem;
	}

	.todo-item {
		margin-bottom: .8rem;
		display: flex;
		align-items: center;
		justify-content: space-between;
		animation-duration: 0.3s;
		border-bottom: 1px solid #aaa;
		padding: .5rem 1rem;
		margin: 0 1rem;
	}

	.remove-item {
		font-size: 2rem;
		cursor: pointer;
		margin-left: 1rem;
	}
		.remove-item:hover {
			color: coral;
		}

	.todo-item-left {
		display: flex;
		align-items: center;
	}

	.todo-item-label {
		padding: .6rem;
		/* border: 1px solid white; */
		margin-left: .8rem;
	}

	.todo-item-edit {
		font-size: 1rem;
		color: cornflowerblue;
		background: #333;
		margin-left: .8rem;
		width: 100%;
		padding: .5rem 1rem;
		border: 1px solid #aaa;
		font-family: 'Avenir', Helvetica, Arial, sans-serif;
	}
		.todo-item-edit:focus {
			outline: none;
		}

	.completed {
		text-decoration: line-through;
		color: #aaa;
	}

	.extra-container {
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 1rem;
		border-top: 2px solid coral;
		padding-top: 1rem;
		margin: 1rem 0;
	}
		.extra-container input {
			margin-right: .5rem;
		}

	button {
		font-size: 1rem;
		padding: .5rem 1rem;
		background-color: #aaa;
		appearance: none;
		border: 2px solid #eee;
		outline: none;
		cursor: pointer;
	}
		button:hover {
			background: coral;
		}
		button:focus {
			outline: none;
		}

	.active {
		background: coral;
	}

</style>

<!-- Mark Up -->
<div class="container">
	<img src={'../public/img/svelte-logo-horizontal.svg'} alt="svelte logo" class="logo">

	<input type="text" class="todo-input" placeholder="What needs to be done"
	bind:value={newTodo} on:keydown={addTodo}>

	{#each filteredTodos as todo}
		<div class="todo-item">
			<div class="todo-item-left" transition:fly={{ y: 30, duration: 400 }}>
				<input type="checkbox" bind:checked={todo.completed}>
				{#if !todo.editing}
					<div class="todo-item-label" class:completed={todo.completed} on:dblclick={() => editTodo(todo)}>{todo.title}</div>
				{:else}
					<input class="todo-item-edit" bind:value={todo.title} type="text" 
					on:blur={() => doneEdit(todo)} on:keydown={() => doneEditKeydown(todo, event)} autofocus>
				{/if}
			</div>
			<div class="remove-item" on:click={() => deleteTodo(todo.id)}>
				&times;	
			</div>
		</div>
	{/each}

	<div class="extra-container">
		<div>
			<label>
				<input type="checkbox" on:change={checkAllTodos}>Check All
			</label>
		</div>
		<div>{todosRemaining} items left</div>
	</div>

	<div class="extra-container">
		<div>
			<button on:click={() => updateFilter('all')} class:active={currentFilter === 'all'}>All</button>
			<button on:click={() => updateFilter('active')} class:active={currentFilter === 'active'}>Active</button>
			<button on:click={() => updateFilter('completed')} class:active={currentFilter === 'completed'}>Completed</button>
		</div>

		<div>
			<button on:click={clearCompleted}>Clear Completed</button>
		</div>
	</div>
</div>