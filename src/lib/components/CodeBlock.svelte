<script lang="ts">
	import { onMount } from 'svelte';
	import { getHighlighter } from 'shiki';

	interface CodeBlockProps {
		code: string;
		lang?: string;
		class?: string;
	}

	let { code, lang = 'javascript', class: className = '' }: CodeBlockProps = $props();

	let highlightedCode = $state('');
	let isLoading = $state(true);

	onMount(async () => {
		try {
			const highlighter = await getHighlighter({
				themes: ['github-dark'],
				langs: ['javascript', 'typescript', 'bash', 'shell', 'html', 'css', 'json', 'jsx', 'tsx']
			});

			highlightedCode = highlighter.codeToHtml(code, {
				lang: lang || 'javascript',
				theme: 'github-dark'
			});
		} catch (error) {
			console.error('Failed to highlight code:', error);
			highlightedCode = `<pre><code>${code}</code></pre>`;
		} finally {
			isLoading = false;
		}
	});
</script>

{#if isLoading}
	<pre class="rounded-lg border border-islamic-line bg-[#0d1117] p-4 text-[13px] {className}"><code
			>{code}</code
		></pre>
{:else}
	<div class="code-block-wrapper {className}">
		{@html highlightedCode}
	</div>
{/if}

<style>
	:global(.code-block-wrapper pre) {
		@apply rounded-lg border border-islamic-line p-4 text-[13px] overflow-x-auto;
	}

	:global(.code-block-wrapper code) {
		@apply font-mono text-[13px] leading-relaxed;
	}
</style>
