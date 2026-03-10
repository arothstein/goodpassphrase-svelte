<script lang="ts">
	import "../app.css";
	import LeftDrawer from "$lib/components/LeftDrawer.svelte";
	import { Menu, Github } from "lucide-svelte";

	let { children } = $props();
	let leftDrawerOpen = $state(false);

	function toggleDrawer() {
		leftDrawerOpen = !leftDrawerOpen;
	}

	function closeDrawer() {
		leftDrawerOpen = false;
	}
</script>

<div class="min-h-screen bg-gray-50 flex flex-col">
	<!-- Header -->
	<header class="bg-primary text-white shadow-md sticky top-0 z-50">
		<div class="flex items-center h-16 px-4">
			<button
				class="p-2 rounded-full hover:bg-white/10 transition-colors mr-4"
				onclick={toggleDrawer}
				aria-label="Toggle Menu"
			>
				<Menu size={24} />
			</button>

			<div class="text-xl font-medium tracking-wide flex-1">
				GoodPassphrase.com
			</div>

			<a
				href="https://github.com/arothstein/goodpassphrase"
				target="_blank"
				rel="noreferrer"
				class="p-2 rounded-full hover:bg-white/10 transition-colors"
				aria-label="GitHub Repository"
			>
				<Github size={24} />
			</a>
		</div>
	</header>

	<div class="flex flex-1 overflow-hidden relative">
		<!-- Sidebar Desktop + Mobile Overlay -->
		<!-- Backdrop for mobile -->
		{#if leftDrawerOpen}
			<!-- svelte-ignore a11y_click_events_have_key_events -->
			<!-- svelte-ignore a11y_no_static_element_interactions -->
			<div
				class="fixed inset-0 bg-black/50 z-40 lg:hidden"
				onclick={closeDrawer}
			></div>
		{/if}

		<!-- Drawer Panel -->
		<aside
			class="fixed inset-y-0 left-0 bg-white border-r border-gray-200 w-64 z-50 transform transition-transform duration-300 ease-in-out lg:static lg:translate-x-0 {leftDrawerOpen
				? 'translate-x-0'
				: '-translate-x-full'}"
			style="top: 64px;"
		>
			<LeftDrawer {closeDrawer} />
		</aside>

		<!-- Main Content -->
		<main class="flex-1 overflow-y-auto p-4 lg:p-6 w-full relative z-0">
			{@render children()}
		</main>
	</div>
</div>
