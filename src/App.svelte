<script>
	import { fade, fly } from 'svelte/transition';

	const ENTER_KEY = 13;
	const ESCAPE_KEY = 27;

	let newTodo = '';
	let tempID = 4;
	let currentFilter = 'all';
	let todos = [{
		id: 1,
		completed: false,
		title: 'Continue read "Eloquent javascript ebook (2nd edition)"',
		editing: false
	}, {
		id: 2,
		completed: false,
		title: 'Get started with Svelte 3',
		editing: false
	}, {
		id: 3,
		completed: false,
		title: 'Play some dota 2 ranked matches',
		editing: false
	}];

	function addTodo(event) {
		if (event.which === ENTER_KEY) {
			// This is how it's done with the spread syntax of ES6
			// todos = [...todos, {
			// 	id: tempID + 1,
			// 	completed: false,
			// 	title: newTodo,
			// 	editing: false
			// }];

			todos.push({
				id: tempID,
				completed: false,
				title: newTodo,
				editing: false
			});

			todos = todos;
			tempID = tempID + 1;
			newTodo = '';
			console.log(`New todo item with ID ${tempID} has been added`);
		}
	}

	function editTodo(todo) {
		todo.editing = true;

		todos = todos;
	}

	function onBlurred(todo) {
		todo.editing = false;
		todos = todos;

		console.log('Data has been updated!');
	}

	function editOnKeyDown(todo, event) {
		if (event.which === ENTER_KEY) {
			onBlurred(todo);
		}
		if (event.which === ESCAPE_KEY) {			
			// HOMEWORK: Some logic for canceling changes to the binding

			console.log('Escape key pressed. Should out from input');
		}
	}

	function deleteTodo(id) {
		// todos = todos.filter(todo => todo.id !== id);

		todos = todos.filter(function(todo) {
			return todo.id !== id;
		});
		console.log(`ID ${id} is deleted`);
	}

	function checkAllTodos(event) {
		todos.forEach(function (todo) {
			return todo.completed = event.target.checked
		});

		todos = todos;
	}

	function clearCompleted() {
		todos = todos.filter(function(todo) {
			return !todo.completed;
		});
	}
	function updateFilter(filter) {
		currentFilter = filter;
	}

	$: prevTitle = todos.filter(function(todo) {
		return todo.title;
	});

	$: todosRemaining = todos.filter(function(todo) {
		return !todo.completed
	}).length;

	$: filteredTodos = currentFilter === 'all'
		? todos
		: currentFilter === 'completed'
			? todos.filter(todo => todo.completed)
			: todos.filter(function (todo) {
				return !todo.completed
			});

</script>

<style lang="scss">
	.container {
		max-width: 600px;
		margin: 0 auto;
	}
	.logo {
		display: block;
		margin: 20px auto;
		height: 75px;
	}

	.todo-input {
		width: 100%;
		padding: 10px 18px;
		font-size: 18px;
		margin-bottom: 16px;
	}
	.todo-item {
		margin-bottom: 12px;
		display: flex;
		align-items: center;
		justify-content: space-between;
		animation-duration: 0.3s;
	}
	.remove-item {
		cursor: pointer;
		margin-left: 14px;
		&:hover {
			color: black;
		}
	}
	.todo-item-left { // later
		display: flex;
		align-items: center;
	}
	.todo-item-label {
		padding: 10px;
		border: 1px solid white;
		margin-left: 12px;
	}
	.todo-item-edit {
		font-size: 24px;
		color: #2c3e50;
		margin-left: 12px;
		width: 100%;
		padding: 10%;
		border: 1px solid #ccc; //override defaults
		font-family: 'Roboto', 'Avenir', Arial, Helvetica, sans-serif;
		&:focus {
			outline: none;
		}
	}
	.completed {
		text-decoration: line-through;
		color: grey;
	}
	.extra-container {
		display: flex;
		align-items: center;
		justify-content: space-between;
		font-size: 16px;
		border-top: 1px solid lightgrey;
		padding-top: 14px;
		margin-bottom: 14px;

		label {
			color: salmon;
		}

		input {
			margin-right: 18px;
		}
	}
	button {
		font-size: 14px;
		background-color: white;
		appearance: none;
		&:hover {
			background: salmon;
		}
		&:focus {
			outline: none;
		}
	}
	.active {
		background: salmon;
	}

</style>

<div class="container">
	<img src={'/img/svelte-logo-horizontal.svg'} alt="svelte-logo" class="logo">

	<input type="text" class="todo-input" placeholder="What needs to be done"
	bind:value={ newTodo } on:keydown={ addTodo }>

	{ #each filteredTodos as todo }
	<div class="todo-item">
		<div class="todo-item-left" transition:fly="{{ y:20, duration: 300 }}">
			<input type="checkbox" bind:checked={ todo.completed }/>
			{ #if !todo.editing }
			<div class="todo-item-label" class:completed={ todo.completed } on:dblclick={ () => editTodo(todo)}>
				{ todo.title }
			</div>
			{ :else }
			<input type="text" class="todo-item-edit" bind:value={todo.title} on:blur={()=> onBlurred(todo)} on:keydown={()=> editOnKeyDown(todo, event)} autofocus>
			{ /if }
		</div>

		<div class="remove-item" on:click={ () => deleteTodo(todo.id)}>
		&times;</div>
	</div>
	{ /each }

	<div class="extra-container">
		<div>
			<label><input type="checkbox" on:change={ checkAllTodos }>
				Check all</label>
		</div>
		<div style="color: blue;">{ todosRemaining } items left</div>
	</div>

	<div class="extra-container">
		<div>
			<button on:click={ () => updateFilter('all') } class:active={ currentFilter === 'all'}>All</button>
			<button on:click={ () => updateFilter('active') } class:active={ currentFilter === 'active'}>Active</button>
			<button on:click={ () => updateFilter('completed') } class:active={ currentFilter === 'completed'}>Completed</button>
		</div>

		<div>
			<button on:click={ clearCompleted }>Clear completed</button>
		</div>
	</div>

</div>
