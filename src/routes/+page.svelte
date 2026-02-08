<script lang="ts">
	import { PUBLIC_TASKS_STORAGE_KEY } from '$env/static/public';
	import { page } from '$app/state';
	import {
		PUBLIC_DEFAULT_MINUTES,
		PUBLIC_DEFAULT_SECONDS,
		PUBLIC_STORAGE_KEY
	} from '$env/static/public';
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';

	let isSilentMode = $derived(
		(() => {
			if (!browser) return false;
			const stored = localStorage.getItem('silent-mode');
			return stored === 'true';
		})()
	);

	$effect(() => {
		localStorage.setItem('silent-mode', isSilentMode ? 'true' : 'false');
	});

	// Preload audio files for later use
	let notificationAudio: HTMLAudioElement | null = null;
	let tickAudio: HTMLAudioElement | null = null;

	onMount(() => {
		try {
			notificationAudio = new Audio('/notification.mp3');
			if (isSilentMode) {
				notificationAudio.volume = 0;
			} else {
				notificationAudio.volume = 0.7;
			}
		} catch (error) {}

		try {
			tickAudio = new Audio('/tick.mp3');
			if (isSilentMode) {
				tickAudio.volume = 0;
			} else {
				tickAudio.volume = 0.7;
			}
		} catch (error) {}
	});

	type MiniTask = {
		id: number;
		text: string;
		completed: boolean;
		timestamp: number;
	};

	let miniTasks: MiniTask[] = $state([]);

	function loadMiniTasks() {
		const data = localStorage.getItem(PUBLIC_TASKS_STORAGE_KEY);
		if (data) {
			try {
				const allTasks: MiniTask[] = JSON.parse(data);
				miniTasks = allTasks
					.filter((t) => !t.completed)
					.sort((a, b) => a.timestamp - b.timestamp)
					.slice(0, 3);
			} catch {}
		}
	}

	let focusMinutes = $state(Number(PUBLIC_DEFAULT_MINUTES));
	let breakMinutes = $state(5); // default break, will update from preset

	let minutes = $derived(focusMinutes);
	let seconds = $state(Number(PUBLIC_DEFAULT_SECONDS));

	let timer: ReturnType<typeof setInterval> | null = $state(null);

	let running = $state(false);
	let isFocus = $state(true);
	let isPaused = $state(false);
	let phaseLabel = $state('Focusing');

	// Read preset from localStorage (set by setup page)
	function loadPreset() {
		const preset = localStorage.getItem('focus-preset');
		if (preset) {
			const match = preset.match(/(\d+)\/(\d+)/);
			if (match) {
				focusMinutes = Number(match[1]);
				breakMinutes = Number(match[2]);
			}
		}
	}

	function saveTimer() {
		const timestamp = Date.now();
		localStorage.setItem(
			PUBLIC_STORAGE_KEY,
			JSON.stringify({ minutes, seconds, running, isFocus, timestamp, isPaused })
		);
	}

	function loadTimer() {
		const data = localStorage.getItem(PUBLIC_STORAGE_KEY);
		if (data) {
			try {
				const parsed = JSON.parse(data);
				// Check timestamp
				if (parsed.timestamp && parsed.running) {
					const elapsed = (Date.now() - parsed.timestamp) / 1000;
					const totalSeconds =
						(parsed.minutes ?? (parsed.isFocus ? focusMinutes : breakMinutes)) * 60 +
						(parsed.seconds ?? Number(PUBLIC_DEFAULT_SECONDS));

					if (elapsed < totalSeconds) {
						const remainingSeconds = totalSeconds - elapsed;
						minutes = Math.floor(remainingSeconds / 60);
						seconds = Math.floor(remainingSeconds % 60);
						running = parsed.running ?? false;
						isFocus = parsed.isFocus ?? true;
						phaseLabel = isFocus ? 'Focusing' : 'Break';
						running = false;
						isPaused = true;
						return;
					}
				} else if (parsed.running || parsed.isPaused) {
					minutes = parsed.minutes ?? (parsed.isFocus ? focusMinutes : breakMinutes);
					seconds = parsed.seconds ?? Number(PUBLIC_DEFAULT_SECONDS);
					running = parsed.running ?? false;
					isFocus = parsed.isFocus ?? true;
					phaseLabel = isFocus ? 'Focusing' : 'Break';
				}
			} catch {}
		}
	}

	function tick() {
		if (seconds === 0) {
			if (minutes === 0) {
				// Switch phase
				if (isFocus) {
					// Start break
					isFocus = false;
					phaseLabel = 'Break';
					minutes = breakMinutes;
					seconds = 0;
				} else {
					// Start focus
					isFocus = true;
					phaseLabel = 'Focusing';
					minutes = focusMinutes;
					seconds = 0;
				}
				saveTimer();
				return;
			}
			minutes--;
			seconds = 59;
		} else {
			seconds--;
		}
		saveTimer();
	}

	function startTimer() {
		if (timer) {
			clearInterval(timer);
			timer = null;
		}

		try {
			// play tick sound in loop
			if (tickAudio) {
				tickAudio.currentTime = 0;
				tickAudio.play();
				// tickAudio.loop = true;

				// if (tickAudio) {
				tickAudio.addEventListener('ended', () => {
					tickAudio!.currentTime = 0;
					tickAudio!.play();
				});
				// }
			}
		} catch (error) {
			console.log('Error playing tick sound:', error);
		}
		running = true;
		isPaused = false;
		timer = setInterval(tick, 1000);

		saveTimer();
	}

	function stopTimer() {
		if (timer) {
			clearInterval(timer);
			timer = null;
		}

		running = false;

		try {
			// stop tick sound
			if (tickAudio) {
				tickAudio.pause();
				tickAudio.currentTime = 0;
			}
			// play notification sound
			if (notificationAudio) {
				notificationAudio.currentTime = 0;
				notificationAudio.play();
			}
		} catch (error) {}

		saveTimer();
	}

	function stopAndResetTimer() {
		stopTimer();

		minutes = focusMinutes;
		seconds = Number(PUBLIC_DEFAULT_SECONDS);

		localStorage.removeItem(PUBLIC_STORAGE_KEY);

		phaseLabel = 'Focusing';
	}

	function toggleTimer() {
		if (running) {
			isPaused = true;
			stopTimer();
		} else {
			startTimer();
		}
	}

	// On mount, load preset and timer
	onMount(() => {
		loadMiniTasks();
		loadPreset();
		loadTimer();
		phaseLabel = isFocus ? 'Focusing' : 'Break';
		if (running) {
			startTimer();
		}
	});
</script>

<!-- Top Bar -->
<div class="flex items-center justify-between p-4 pb-2">
	<a href="/setup" class="flex size-12 shrink-0 items-center justify-start text-gray-600">
		<span class="material-symbols-outlined cursor-pointer">settings</span>
	</a>
	<h2 class="flex-1 text-center text-lg leading-tight font-bold tracking-tight text-gray-900">
		Focus
	</h2>
	<div class="flex w-12 items-center justify-end">
		<!-- silent mode toggle button -->
		<button
			class="flex items-center justify-center rounded-full {isSilentMode
				? 'text-gray-400'
				: 'text-primary'} p-2 transition-colors hover:bg-slate-200"
			onclick={() => {
				isSilentMode = !isSilentMode;

				if (notificationAudio) {
					notificationAudio.volume = isSilentMode ? 0 : 0.7;
				}

				if (tickAudio) {
					tickAudio.volume = isSilentMode ? 0 : 0.7;
				}
			}}
		>
			<span class="material-symbols-outlined text-xl"
				>{isSilentMode ? 'volume_off' : 'volume_up'}</span
			>
		</button>
	</div>
</div>
<div class="flex flex-1 flex-col items-center justify-center px-8">
	<!-- Phase Label -->
	<div class="mb-2">
		<p class="text-center text-sm font-semibold tracking-widest text-primary uppercase">
			{phaseLabel}
		</p>
	</div>
	<!-- Timer Circle -->
	<div class="relative mb-12 flex h-72 w-72 items-center justify-center">
		<!-- Progress Ring -->
		<svg class="absolute h-full w-full">
			<circle
				class="text-gray-200"
				cx="144"
				cy="144"
				fill="transparent"
				r="135"
				stroke="currentColor"
				stroke-width="4"
			></circle>
			<circle
				class="progress-ring-circle text-primary"
				cx="144"
				cy="144"
				fill="transparent"
				r="135"
				stroke="currentColor"
				stroke-dasharray="848"
				stroke-dashoffset={running
					? 848 - ((minutes * 60 + seconds) / (focusMinutes * 60)) * 848
					: 848}
				stroke-linecap="round"
				stroke-width="4"
			></circle>
		</svg>
		<!-- {#key }
            
        {/key} -->
		<!-- Countdown Display -->
		<div class="flex gap-2">
			<div class="flex flex-col items-center">
				<p class="text-7xl font-light tracking-tighter text-gray-900">{minutes}</p>
				<p class="mt-1 text-xs font-medium text-gray-500 uppercase">Min</p>
			</div>
			<p class="mt-1 text-6xl font-thin text-gray-400">:</p>
			<div class="flex flex-col items-center">
				<p class="text-7xl font-light tracking-tighter text-gray-900">
					{seconds.toString().padStart(2, '0')}
				</p>
				<p class="mt-1 text-xs font-medium text-gray-500 uppercase">Sec</p>
			</div>
		</div>
	</div>
	<!-- ProgressBar (Inline style) -->
	<div class="mb-12 w-full max-w-70">
		<div class="flex flex-col gap-3">
			<div class="flex justify-between gap-6">
				<p class="text-xs font-medium text-gray-500 uppercase">{phaseLabel} Progress</p>
				<p class="text-xs font-bold text-gray-900 uppercase">
					{running ? 100 - Math.round(((minutes * 60 + seconds) / (focusMinutes * 60)) * 100) : 0}%
				</p>
			</div>
			<div class="h-1.5 overflow-hidden rounded-full bg-gray-200">
				<div
					class="h-full rounded-full bg-primary"
					style="width: {running
						? 100 - ((minutes * 60 + seconds) / (focusMinutes * 60)) * 100
						: 0}%"
				></div>
			</div>
		</div>
	</div>
	<!-- Main Action Button -->
	<div class="flex w-full justify-center gap-4 px-4 py-3">
		<button
			class="flex h-16 min-w-50 cursor-pointer items-center justify-center overflow-hidden rounded-full bg-primary px-10 text-lg font-bold text-white shadow-lg shadow-primary/20 transition-transform hover:scale-105"
			onclick={toggleTimer}
		>
			<span class="material-symbols-outlined mr-2">{running ? 'pause' : 'play_arrow'}</span>
			<span>{running ? 'Pause' : 'Start'}</span>
		</button>
		{#if running}
			<button
				class="flex h-16 min-w-30 cursor-pointer items-center justify-center overflow-hidden rounded-full bg-gray-300 px-6 text-lg font-bold text-gray-700 shadow-lg shadow-gray-300/20 transition-transform hover:scale-105"
				onclick={stopAndResetTimer}
			>
				<span class="material-symbols-outlined mr-2">stop_circle</span>
				<span>Stop</span>
			</button>
		{/if}
	</div>
</div>
<!-- Mini Todo List Section -->
<div class="px-6 pb-10">
	<div class="mb-4 flex items-center justify-between">
		<h3 class="text-sm font-bold tracking-wider text-gray-900 uppercase">Next Task</h3>
		<span class="material-symbols-outlined cursor-pointer text-xl text-gray-400">list</span>
	</div>
	{#each miniTasks as task}
		<a href="/tasks" class="mb-3 flex items-start gap-3 rounded-xl bg-gray-200 p-4">
			<div
				class="flex size-6 items-center justify-center rounded-full border-2 {task.completed
					? 'border-green-500'
					: 'border-gray-300'}"
			>
				<div class="size-3 rounded-full {task.completed ? 'bg-green-500' : 'bg-transparent'}"></div>
			</div>
			<div class="flex-1">
				<p
					class="text-sm {task.completed
						? 'text-gray-400 line-through'
						: 'font-medium text-gray-900'}"
				>
					{task.text}
				</p>
				<p class="mt-1 text-xs text-gray-500">
					{new Date(task.timestamp).toLocaleString()}
				</p>
			</div>
		</a>
	{/each}
</div>
<!-- Footer Navigation -->
<div
	class="/50 mt-auto flex justify-around border-t border-gray-100 bg-white/50 py-4 backdrop-blur-md"
>
	<a
		href="/"
		class="flex flex-col items-center gap-1 {page.url.pathname === '/'
			? 'text-primary'
			: 'text-gray-400'}"
	>
		<span class="material-symbols-outlined">timer</span>
		<span class="text-[10px] font-bold">Timer</span>
	</a>
	<a
		href="/tasks"
		class="flex flex-col items-center gap-1 {page.url.pathname.startsWith('/tasks')
			? 'text-primary'
			: 'text-gray-400'}"
	>
		<span class="material-symbols-outlined">check_circle</span>
		<span class="text-[10px] font-bold">Tasks</span>
	</a>
	<a
		href="/stats"
		class="flex flex-col items-center gap-1 {page.url.pathname.startsWith('/stats')
			? 'text-primary'
			: 'text-gray-400'}"
	>
		<span class="material-symbols-outlined">analytics</span>
		<span class="text-[10px] font-bold">Stats</span>
	</a>
</div>
