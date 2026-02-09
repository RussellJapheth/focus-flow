<script lang="ts">
	import { PUBLIC_TASKS_STORAGE_KEY } from '$env/static/public';
	import { onMount } from 'svelte';

	type Task = {
		id: number;
		text: string;
		completed: boolean;
		timestamp: number;
	};

	let tasks: Task[] = $state([]);
	let newTask = $state('');
	let nextId = $state(1);

	function saveTasks() {
		localStorage.setItem(PUBLIC_TASKS_STORAGE_KEY, JSON.stringify(tasks));
	}

	function loadTasks() {
		const data = localStorage.getItem(PUBLIC_TASKS_STORAGE_KEY);
		if (data) {
			try {
				tasks = JSON.parse(data);
				if (tasks.length > 0) {
					nextId = Math.max(...tasks.map((t) => t.id)) + 1;
				}
			} catch {}
		}
	}

	function addTask() {
		const text = newTask.trim();
		if (!text) return;
		tasks = [{ id: nextId++, text, completed: false, timestamp: Date.now() }, ...tasks];
		newTask = '';
		saveTasks();
	}

	function toggleTask(id: number) {
		tasks = tasks.map((t) => (t.id === id ? { ...t, completed: !t.completed } : t));
		saveTasks();
	}

	function removeTask(id: number) {
		tasks = tasks.filter((t) => t.id !== id);
		saveTasks();
	}

	onMount(() => {
		loadTasks();
	});
</script>

<!-- Top Navigation Bar -->
<header
	class="/80 sticky top-0 z-10 border-b border-slate-200 bg-background-light/80 backdrop-blur-md"
>
	<div class="flex h-16 items-center justify-between px-4">
		<a href="/" class="flex items-center gap-2">
			<span class="material-symbols-outlined text-primary">arrow_back_ios</span>
			<span class="font-medium text-primary">Focus</span>
		</a>
		<h1 class="text-lg font-bold tracking-tight">Tasks</h1>
		<div></div>
	</div>
</header>

<main class="flex-1 space-y-1 overflow-y-auto px-4 py-6">
	{#each tasks
		.slice()
		.sort((a, b) => Number(a.completed) - Number(b.completed) || b.timestamp - a.timestamp) as task (task.id)}
		<div class="/50 group flex min-h-14 items-center gap-4 border-t border-slate-100 py-3">
			<div class="flex size-7 items-center justify-center">
				<input
					class="custom-checkbox h-6 w-6 rounded-full border-2 {task.completed
						? 'border-primary bg-primary text-white'
						: 'border-slate-300 bg-transparent text-primary'} focus:ring-0 focus:ring-offset-0"
					type="checkbox"
					checked={task.completed}
					onchange={() => toggleTask(task.id)}
				/>
			</div>
			<div class="flex-1">
				<p
					class="truncate text-base {task.completed
						? 'font-normal line-through opacity-40'
						: 'font-medium'}"
				>
					{task.text}
				</p>
				<p class="mt-1 text-xs text-slate-400">
					{new Date(task.timestamp).toLocaleString()}
				</p>
			</div>
			<button
				class="ml-2 text-slate-400 opacity-0 transition-opacity group-hover:opacity-100 hover:text-red-500"
				title="Delete"
				onclick={() => removeTask(task.id)}
			>
				<span class="material-symbols-outlined">delete</span>
			</button>
		</div>
	{/each}
</main>
<!-- Footer Input Field -->
<footer class="bg-background-light p-4 pb-10">
	<div class="flex items-center gap-3">
		<div class="flex flex-1 items-stretch">
			<div class="relative w-full">
				<form
					class="relative w-full"
					onsubmit={(e) => {
						e.preventDefault();
						addTask();
					}}
					autocomplete="off"
				>
					<input
						class="/50 h-14 w-full rounded-2xl border-none bg-slate-200/50 pr-4 pl-12 text-base font-normal placeholder:text-slate-500 focus:ring-2 focus:ring-primary/50"
						placeholder="Add a task"
						type="text"
						bind:value={newTask}
						autocomplete="off"
					/>
					<button type="submit" class="absolute top-1/2 left-4 -translate-y-1/2 text-primary">
						<span class="material-symbols-outlined">add</span>
					</button>
				</form>
			</div>
		</div>
	</div>
	<!-- Quick Stats / Session Info -->
	<div class="mt-8 flex flex-row items-center justify-evenly gap-8">
		<div class="flex flex-row gap-3">
			<div class="text-center">
				<p class="text-md font-semibold">
					{tasks.length} <span class="text-md font-normal opacity-50">Tasks</span>
				</p>
			</div>
		</div>
		<!-- button to clear completed tasks -->
		{#if tasks.some((t) => t.completed)}
			<div class="h-8 w-px bg-slate-200"></div>
			<button
				class=" flex h-max min-w-30 cursor-pointer items-center justify-center overflow-hidden rounded-full bg-gray-300 p-3 text-xs font-bold text-gray-700 shadow-lg shadow-gray-300/20 transition-transform hover:scale-105"
				onclick={() => {
					tasks = tasks.filter((t) => !t.completed);
					saveTasks();
				}}
			>
				<span class="material-symbols-outlined mr-2 text-lg">delete_sweep</span>
				<span>Clear Completed</span>
			</button>
		{/if}
	</div>
</footer>
<div class="min-h-24">&nbsp;</div>

<!-- Tab Bar (iOS Style) -->
<div
	class="fixed bottom-0 left-0 mx-auto mt-auto flex w-full max-w-107.5 justify-around border-t border-slate-200 bg-background-light/80 p-3 pb-8 backdrop-blur-md"
>
	<a href="/" class="flex flex-col items-center text-slate-400">
		<span class="material-symbols-outlined">timer</span>
		<span class="mt-1 text-[10px] font-medium">Focus</span>
	</a>
	<a href="/tasks" class="flex flex-col items-center text-primary">
		<span class="material-symbols-outlined">check_circle</span>
		<span class="mt-1 text-[10px] font-medium">Tasks</span>
	</a>
	<a href="/stats" class="flex flex-col items-center text-slate-400">
		<span class="material-symbols-outlined">bar_chart</span>
		<span class="mt-1 text-[10px] font-medium">Stats</span>
	</a>
</div>
