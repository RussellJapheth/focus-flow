<script lang="ts">
	import { onMount } from 'svelte';

	let selectedPreset = $state('60/25');

	onMount(() => {
		const preset = localStorage.getItem('focus-preset');
		if (preset) {
			selectedPreset = preset;
		}
	});

	function selectPreset(preset: string) {
		selectedPreset = preset;
		localStorage.setItem('focus-preset', preset);
	}
</script>

<div class="relative flex min-h-screen w-full flex-col overflow-x-hidden bg-background-light">
	<!-- TopAppBar -->
	<div class="flex items-center justify-between bg-background-light p-4 pb-2">
		<a href="/" class="flex size-12 shrink-0 items-center justify-start text-slate-900">
			<span class="material-symbols-outlined cursor-pointer">arrow_back_ios</span>
		</a>
		<h2
			class="flex-1 text-center text-lg leading-tight font-bold tracking-[-0.015em] text-slate-900"
		>
			Focus Presets
		</h2>
	</div>
	<div class="mx-auto flex w-full max-w-md flex-1 flex-col items-center justify-around">
		<div>
			<!-- HeadlineText -->
			<h2
				class="tracking-light px-4 pt-5 pb-3 text-center text-[28px] leading-tight font-bold text-slate-900"
			>
				Choose your flow
			</h2>
			<!-- BodyText -->
			<p class="px-8 pt-1 pb-6 text-center text-base leading-normal font-normal text-slate-600">
				Select a work/rest cycle to start your session. Deep work requires consistent intervals.
			</p>
		</div>
		<div class="mx-auto flex w-full max-w-md flex-1 flex-col items-center">
			<!-- SegmentedButtons (Presets) -->
			<div class="w-full px-4 py-3">
				<div class="flex h-14 w-full items-center justify-center rounded-xl bg-slate-200 p-1.5">
					{#each ['25/5', '30/5', '50/10', '60/25', '90/30'] as preset}
						<label
							class="flex h-full grow cursor-pointer items-center justify-center overflow-hidden rounded-lg px-2 text-sm leading-normal font-semibold transition-all
                                {selectedPreset === preset
								? 'bg-white text-primary shadow-sm'
								: 'text-slate-500'}"
						>
							<span class="truncate">{preset}</span>
							<input
								class="invisible w-0"
								name="focus-preset"
								type="radio"
								value={preset}
								checked={selectedPreset === preset}
								onchange={() => selectPreset(preset)}
							/>
						</label>
					{/each}
				</div>
			</div>
			<!-- Focus Visualization (Custom Component) -->
			<div
				class="relative mt-8 mb-4 flex h-48 w-48 items-center justify-center rounded-full border-4 border-primary/20"
			>
				<div class="absolute inset-0 animate-pulse rounded-full border-t-4 border-primary"></div>
				<div class="text-center">
					<span class="text-4xl font-bold text-slate-900">{selectedPreset.split('/')[0]}:00</span>
					<p class="mt-1 text-xs tracking-widest text-slate-500 uppercase">Focus Period</p>
				</div>
			</div>
		</div>
		<div>&nbsp;</div>
	</div>
	<!-- Tab Bar (iOS Style) -->
	<div
		class="fixed bottom-0 left-0 mx-auto mt-auto flex w-full max-w-107.5 justify-around border-t border-slate-200 bg-background-light/80 p-3 pb-8 backdrop-blur-md"
	>
		<a href="/" class="flex flex-col items-center text-slate-400">
			<span class="material-symbols-outlined">timer</span>
			<span class="mt-1 text-[10px] font-medium">Focus</span>
		</a>
		<a href="/tasks" class="flex flex-col items-center text-slate-400">
			<span class="material-symbols-outlined">check_circle</span>
			<span class="mt-1 text-[10px] font-medium">Tasks</span>
		</a>
		<a href="/stats" class="flex flex-col items-center text-slate-400">
			<span class="material-symbols-outlined">bar_chart</span>
			<span class="mt-1 text-[10px] font-medium">Stats</span>
		</a>
	</div>
</div>
