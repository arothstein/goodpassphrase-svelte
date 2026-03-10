<script lang="ts">
	import { page } from '$app/state';

	const links = [
		{ href: '/', label: 'GoodPassphrase.com' },
		{ href: '/about', label: 'About' },
		{ href: '/privacy', label: 'Privacy' }
	];

	let { closeDrawer } = $props<{ closeDrawer: () => void }>();
</script>

<div class="flex flex-col py-4 gap-1">
	{#each links as link}
		<a
			href={link.href}
			onclick={closeDrawer}
			class="px-4 py-3 text-sm transition-colors hover:bg-gray-100 dark:hover:bg-gray-800 {page.url.pathname === link.href ? 'font-medium bg-gray-50 dark:bg-gray-900 text-primary uppercase' : 'text-gray-700 dark:text-gray-300 uppercase'}"
		>
			{link.label}
		</a>
	{/each}
</div>
