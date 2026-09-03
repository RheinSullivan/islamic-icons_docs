<script lang="ts">
	import { MAX } from '$lib/site';
	
	interface Props {
		title: string;
		label: string;
		children: any;
	}
	
	let { title, label, children }: Props = $props();
</script>

<main class="{MAX} pt-36 pb-16 sm:pt-44 sm:pb-24 lg:pt-32">
	<div class="mx-auto max-w-3xl">
		<div class="gsap-container mb-10">
			<span class="text-[9px] font-semibold uppercase tracking-[.2em] text-islamic-green">{label}</span>
			<h1 class="mt-4 font-display text-4xl font-bold tracking-tight sm:text-5xl">{title}</h1>
		</div>
		
		<div class="resource-content">
			{@render children()}
		</div>
	</div>
</main>

<style>
	:global(.resource-content) {
		color: rgb(164 174 168 / 0.7);
	}
	:global(.resource-content h2) {
		margin-top: 3rem;
		margin-bottom: 1.5rem;
		font-family: var(--font-display);
		font-size: 1.5rem;
		font-weight: 600;
		letter-spacing: -0.02em;
		color: rgb(228 234 230);
	}
	:global(.resource-content h3) {
		margin-top: 2rem;
		margin-bottom: 1rem;
		font-family: var(--font-display);
		font-size: 1.25rem;
		font-weight: 600;
		letter-spacing: -0.02em;
		color: rgb(228 234 230);
	}
	:global(.resource-content p) {
		margin-bottom: 1.5rem;
		font-size: 0.9375rem;
		line-height: 1.75;
	}
	:global(.resource-content ul) {
		margin-top: 1.5rem;
		margin-bottom: 1.5rem;
		padding-left: 1.5rem;
		list-style-type: disc;
	}
	:global(.resource-content li) {
		margin-bottom: 0.75rem;
		font-size: 0.9375rem;
		line-height: 1.75;
	}
	:global(.resource-content a) {
		color: rgb(115 224 174);
		text-decoration: underline;
		text-decoration-color: rgb(115 224 174 / 0.3);
		text-underline-offset: 4px;
		transition: text-decoration-color 0.2s;
	}
	:global(.resource-content a:hover) {
		text-decoration-color: rgb(115 224 174);
	}
	:global(.resource-content code) {
		border-radius: 0.375rem;
		background-color: rgb(14 22 17);
		padding: 0.125rem 0.375rem;
		font-size: 0.8125rem;
		color: rgb(164 174 168);
	}
	:global(.resource-content pre) {
		margin-top: 1.5rem;
		margin-bottom: 1.5rem;
		overflow-x: auto;
		border-radius: 0.75rem;
		border: 1px solid rgb(50 132 96 / 0.15);
		background-color: rgb(0 0 0 / 0.3);
		padding: 1.25rem;
		font-size: 0.8125rem;
		line-height: 1.5;
	}
	:global(.resource-content strong) {
		font-weight: 600;
		color: rgb(228 234 230);
	}
	:global(.resource-content blockquote) {
		margin-top: 1.5rem;
		margin-bottom: 1.5rem;
		border-left: 2px solid rgb(115 224 174);
		padding-left: 1.5rem;
		font-style: italic;
		color: rgb(164 174 168);
	}
	:global(.resource-card) {
		margin-bottom: 1.5rem;
		border-radius: 0.75rem;
		border: 1px solid rgb(50 132 96 / 0.15);
		background-color: rgb(14 22 17);
		padding: 2rem;
	}
	:global(.resource-grid) {
		margin-top: 2rem;
		margin-bottom: 2rem;
		display: grid;
		gap: 1rem;
	}
	@media (min-width: 640px) {
		:global(.resource-grid) {
			grid-template-columns: repeat(2, 1fr);
		}
	}
	:global(.resource-box) {
		border-radius: 0.5rem;
		border: 1px solid rgb(50 132 96 / 0.15);
		background-color: rgb(0 0 0 / 0.2);
		padding: 1.25rem;
	}
</style>
