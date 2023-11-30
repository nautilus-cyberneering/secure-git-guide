<script lang="ts">
	import * as config from '$lib/config';
	import Toggle from './toggle.svelte';
</script>

<nav>
	<a href="/" class="title">
		<strong>{config.title}</strong>
	</a>

	<ul class="links">
		<li>
			<a href="/curated-resources">Resources</a>
		</li>
		<li>
			<a href="/#articles">Articles</a>
		</li>
		<Toggle />
	</ul>
</nav>

<style>
	nav {
		display: flex;
		justify-content: space-between;
		padding-block: var(--size-7);
	}

	.links {
		display: flex;
		gap: var(--size-7);
		list-style: none;
		padding: 0;
	}

	a {
		color: inherit;
		text-decoration: none;
		font-size: 1.2rem;
	}

	.links a:hover {
		color: var(--surface-9);
	}

	@media (min-width: 768px) {
		nav {
			margin-inline: var(--size-7);
		}

		.links {
			margin-block: 0;
		}
	}
</style>
