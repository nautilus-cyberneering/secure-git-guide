<script lang="ts">
	import * as config from '$lib/config';
	export let data;
	import Hero from '$lib/components/Hero.svelte';
	import Contribute from '$lib/components/Contribute.svelte';
	import Contact from '$lib/components/Contact.svelte';
</script>

<svelte:head>
	<title>{config.title}</title>
</svelte:head>

<main>
	<Hero />
	<Contribute />
	<section class="articles-section">
		<div class="articles-image">
			<img
				src="/images/brooke-cagle--uHVRvDr7pg-unsplash.jpg"
				class="top-img"
				alt="group of people talking"
			/>
			<img
				src="/images/annie-spratt-QckxruozjRg-unsplash.jpg"
				class="btm-img"
				alt="group of people talking with laptops"
			/>
		</div>
		<div class="articles-section-articles" id="articles">
			<h3 class="articles-heading">Articles</h3>
			<ul class="posts">
				{#each data.posts as post}
					<li class="post">
						<a href={post.slug} class="title">{post.title}</a>
					</li>
				{/each}
			</ul>
		</div>
	</section>
	<Contact />
</main>

<style>
	.articles-section {
		margin-top: var(--size-7);
	}

	.articles-section-articles {
		margin-top: 1.5rem;
	}

	.posts {
		display: grid;
		gap: var(--size-2);
		margin-top: var(--size-2);
		list-style: none;
	}

	.post {
		max-inline-size: var(--size-content-2);
	}

	.post:not(:last-child) {
		border-bottom: 1px solid var(--border);
		padding-bottom: var(--size-1);
	}

	.articles-image {
		flex: 1 1 0px;
	}

	.articles-image img {
		border-radius: 0px;
	}

	.btm-img {
		display: none;
	}

	.title {
		font-size: var(--font-size-1);
		text-transform: capitalize;
	}

	@media (min-width: 768px) {
		.articles-section {
			display: flex;
			gap: var(--size-4);
		}

		.articles-image {
			flex: 1 1 0px;
		}

		.articles-image img {
			max-height: 100%;
			max-width: 100%;
		}

		.btm-img {
			display: block;
			margin-top: 2rem;
		}

		.articles-section-articles {
			flex: 1 1 0px;
			margin-top: 0rem;
		}

		.title {
			font-size: var(--font-size-3);
		}
	}
</style>
