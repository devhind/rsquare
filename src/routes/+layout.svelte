<script lang="ts">
	import './layout.css';
	import favicon from '$lib/assets/favicon.svg';

	import Header from '$lib/components/Header.svelte';
	import Footer from '$lib/components/Footer.svelte';
	import BackToTop from '$lib/components/BackToTop.svelte'; // ✅ ADD THIS

	import { afterNavigate } from '$app/navigation';
	import { browser } from '$app/environment';

	let { children } = $props();

	// ✅ Smooth scroll after navigation
	afterNavigate(() => {
		if (browser && location.hash) {
			const el = document.querySelector(location.hash);
			if (el) {
				el.scrollIntoView({ behavior: 'smooth' });
			}
		}
	});
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<!-- HEADER -->
<Header />

<!-- MAIN -->
<main>
	{@render children()}
</main>

<!-- FOOTER -->
<Footer />

<!-- 🔥 GLOBAL BUTTON (VISIBLE ON ALL PAGES) -->
<BackToTop />