<script lang="ts">
</script>

<!-- Top Navigation Bar -->
<header
	class="/80 sticky top-0 z-10 flex items-center justify-between border-b border-slate-200 bg-background-light/80 px-4 py-4 backdrop-blur-md"
>
	<a
		href="/"
		class="flex items-center justify-center rounded-full p-2 transition-colors hover:bg-slate-200"
	>
		<span class="material-symbols-outlined text-2xl">chevron_left</span>
	</a>
	<h1 class="text-lg font-semibold tracking-tight">Statistics</h1>
	<div></div>
</header>
<main class="mx-auto w-full max-w-md flex-1 space-y-12 px-6 py-8">
	<!-- Today's Session Progress -->
	<section class="space-y-4">
		<div class="flex items-end justify-between">
			<div class="space-y-1">
				<p class="text-sm font-medium tracking-wider text-slate-500 uppercase">Today's Progress</p>
				<h2 class="text-2xl font-bold">4 of 6 Sessions</h2>
			</div>
			<span class="text-lg font-bold text-primary">66%</span>
		</div>
		<!-- Minimalist Session Dots -->
		<div class="flex items-center gap-3 py-2">
			<div class="h-3 flex-1 rounded-full bg-primary shadow-[0_0_12px_rgba(25,127,230,0.4)]"></div>
			<div class="h-3 flex-1 rounded-full bg-primary shadow-[0_0_12px_rgba(25,127,230,0.4)]"></div>
			<div class="h-3 flex-1 rounded-full bg-primary shadow-[0_0_12px_rgba(25,127,230,0.4)]"></div>
			<div class="h-3 flex-1 rounded-full bg-primary shadow-[0_0_12px_rgba(25,127,230,0.4)]"></div>
			<div class="h-3 flex-1 rounded-full bg-slate-200"></div>
			<div class="h-3 flex-1 rounded-full bg-slate-200"></div>
		</div>
		<p class="text-sm text-slate-500">
			Next cycle starts in <span class="font-medium text-slate-900">12 minutes</span>
		</p>
	</section>
	<!-- Hero Metrics -->
	<section class="grid grid-cols-1 gap-6">
		<div class="/50 flex flex-col gap-2 rounded-xl border border-slate-100 bg-white p-6">
			<p class="text-sm font-medium text-slate-500">Total Focus Time</p>
			<div class="flex items-baseline gap-1">
				<span class="text-5xl font-bold tracking-tight text-primary">05</span>
				<span class="text-2xl font-semibold text-slate-400">h</span>
				<span class="text-5xl font-bold tracking-tight text-primary">20</span>
				<span class="text-2xl font-semibold text-slate-400">m</span>
			</div>
		</div>
		<div class="/50 flex flex-col gap-2 rounded-xl border border-slate-100 bg-white p-6">
			<p class="text-sm font-medium text-slate-500">Cycles Completed</p>
			<div class="flex items-baseline gap-2">
				<span class="text-5xl font-bold tracking-tight">08</span>
				<span class="flex items-center gap-1 text-sm font-medium text-emerald-500">
					<span class="material-symbols-outlined text-sm">trending_up</span>
					+2 today
				</span>
			</div>
		</div>
	</section>
	<!-- Weekly Activity Graph -->
	<section class="space-y-6">
		<div class="flex items-center justify-between">
			<h3 class="text-lg font-bold">Weekly Activity</h3>
			<div class="rounded-full bg-slate-100 px-3 py-1 text-xs font-medium text-slate-500">
				Last 7 Days
			</div>
		</div>
		<div class="relative mt-4 h-48 w-full">
			<!-- SVG Graph -->
			<svg class="h-full w-full" preserveaspectratio="none" viewbox="0 0 400 150">
				<defs>
					<lineargradient id="lineGradient" x1="0%" x2="0%" y1="0%" y2="100%">
						<stop offset="0%" stop-color="#197fe6" stop-opacity="0.2"></stop>
						<stop offset="100%" stop-color="#197fe6" stop-opacity="0"></stop>
					</lineargradient>
				</defs>
				<!-- Area fill -->
				<path
					d="M0,120 C40,110 60,140 100,80 C140,20 180,60 220,50 C260,40 300,90 340,30 C380,-30 400,10 400,10 L400,150 L0,150 Z"
					fill="url(#lineGradient)"
				></path>
				<!-- Line -->
				<path
					d="M0,120 C40,110 60,140 100,80 C140,20 180,60 220,50 C260,40 300,90 340,30 C380,-30 400,10 400,10"
					fill="none"
					stroke="#197fe6"
					stroke-linecap="round"
					stroke-width="3"
				></path>
			</svg>
			<!-- Weekday Labels -->
			<div class="mt-4 flex justify-between px-1">
				<span class="text-[10px] font-bold text-slate-400 uppercase">Mon</span>
				<span class="text-[10px] font-bold text-slate-400 uppercase">Tue</span>
				<span class="text-[10px] font-bold text-slate-400 uppercase">Wed</span>
				<span class="text-[10px] font-bold text-primary uppercase">Thu</span>
				<span class="text-[10px] font-bold text-slate-400 uppercase">Fri</span>
				<span class="text-[10px] font-bold text-slate-400 uppercase">Sat</span>
				<span class="text-[10px] font-bold text-slate-400 uppercase">Sun</span>
			</div>
		</div>
		<div class="/20 flex items-center gap-4 rounded-xl bg-primary/10 p-4">
			<div class="flex size-10 items-center justify-center rounded-full bg-primary text-white">
				<span class="material-symbols-outlined text-xl">auto_awesome</span>
			</div>
			<div class="flex-1">
				<p class="text-sm font-semibold">Keep it up!</p>
				<p class="text-xs text-slate-600">You're 12% more focused than last week.</p>
			</div>
		</div>
	</section>
</main>
<!-- Bottom Navigation Bar (iOS style) -->
<nav
	class="fixed bottom-0 left-0 mx-auto mt-auto flex w-full max-w-107.5 items-center justify-between border-t border-slate-200 bg-background-light/90 px-6 pt-2 pb-8 backdrop-blur-lg"
>
	<a href="/" class="flex flex-col items-center gap-1 text-slate-400">
		<span class="material-symbols-outlined">timer</span>
		<span class="text-[10px] font-medium">Timer</span>
	</a>
	<a href="/tasks" class="flex flex-col items-center gap-1 text-slate-400">
		<span class="material-symbols-outlined">list_alt</span>
		<span class="text-[10px] font-medium">Tasks</span>
	</a>
	<a href="/stats" class="flex flex-col items-center gap-1 text-primary">
		<span class="material-symbols-outlined fill-[1]">bar_chart</span>
		<span class="text-[10px] font-bold">Stats</span>
	</a>
</nav>

<style>
	.graph-gradient {
		background: linear-gradient(180deg, rgba(25, 127, 230, 0.15) 0%, rgba(25, 127, 230, 0) 100%);
	}
</style>
