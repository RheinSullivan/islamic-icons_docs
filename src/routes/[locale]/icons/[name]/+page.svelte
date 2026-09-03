<script lang="ts">
	import { page } from '$app/state';
	import { icons, pretty, pathFor, sourceForItem, chooseVariant, variantName, sourceLabel, camel, MAX } from '$lib/site';
	import { translations, type Locale } from '$lib/i18n';

	let { data } = $props();
	const locale = $derived(data.locale as Locale);
	const t = $derived(translations[locale]);

	const iconName = $derived(page.params.name);
	const item = $derived(icons.find(i => i.name === iconName) ?? null);

	let activeSource = $state<string | null>(null);
	let activeVariant = $state('fill');

	const source = $derived.by(() => {
		if (!item) return null;
		if (activeSource) return item.sources.find(s => s.id === activeSource) ?? item.sources[0];
		return sourceForItem(item);
	});
	const variant = $derived(chooseVariant(source ?? undefined, activeVariant));
	const path = $derived(item ? pathFor(item, variant, source?.id) : '');
	const vars = $derived((source?.variants ?? []).filter(v => ['fill','outline','color','original','alternate'].includes(v)));
	const imp = $derived(item ? `import { ${camel(item.name)} } from "islamic-icons/${item.category}"` : '');

	async function copyText(text: string) {
		try { await navigator.clipboard.writeText(text); } catch { /* silent */ }
	}
</script>

<svelte:head>
	{#if item}
		<title>{item.title} {locale === 'en' ? 'Icon' : 'Ikon'} - Islamic Icons</title>
		<meta name="description" content="{item.title} - {pretty(item.category)} {locale === 'en' ? 'icon available in fill, outline and color styles from Islamic Icons.' : 'ikon tersedia dalam gaya fill, outline, dan color dari Islamic Icons.'}" />
		<meta property="og:title" content="{item.title} Icon - Islamic Icons" />
		<meta property="og:description" content="Modern {item.title} SVG icon from Islamic Icons." />
		<link rel="canonical" href="https://islamic-icons.dev/{locale}/icons/{item.name}" />
	{:else}
		<title>Icon Not Found - Islamic Icons</title>
	{/if}
</svelte:head>

{#if item}
<!-- Full screen layout with fixed right sidebar -->
<div class="flex min-h-screen">
	<!-- Main content - takes remaining space -->
	<div class="flex-1 lg:mr-[360px]">
		<div class="{MAX} pt-32 pb-16 sm:pt-36 sm:pb-24">
			<!-- Breadcrumb -->
			<nav class="mb-8 text-[10px] text-islamic-dim" aria-label="Breadcrumb">
				<ol class="flex items-center gap-1">
					<li><a href="/{locale}/icons" class="hover:text-islamic-text">{t.nav.icons}</a></li>
					<li aria-hidden="true"><span class="px-1">/</span></li>
					<li aria-current="page" class="text-islamic-text">{item.title}</li>
				</ol>
			</nav>

			<!-- MAXIMIZE icon preview -->
			<div class="relative grid aspect-[21/9] w-full place-items-center overflow-hidden rounded-2xl border border-islamic-line bg-islamic-panel">
				<img class="max-h-[45%] max-w-[35%] object-contain" src={path} alt={item.title} />
				<div class="absolute inset-x-4 bottom-4 flex items-center justify-between">
					<span class="rounded-full border border-islamic-line bg-islamic-bg/90 px-3 py-1.5 text-[9px] text-islamic-dim backdrop-blur">{sourceLabel(source?.id || '')} · {variantName(variant)}</span>
					<button type="button" onclick={() => copyText(path)} class="cursor-pointer rounded-full border border-islamic-line bg-islamic-bg/90 px-3 py-1.5 text-[9px] text-islamic-green backdrop-blur hover:border-islamic-green/30">Copy SVG</button>
				</div>
			</div>

			<!-- Related icons -->
			{#each [icons.filter(i => i.category === item.category && i.name !== item.name).slice(0, 8)] as related (0)}
				{#if related.length > 0}
					<section class="mt-16 border-t border-islamic-line pt-12">
						<h2 class="font-display text-2xl tracking-[-.03em]">More in {pretty(item.category)}</h2>
						<div class="mt-8 grid grid-cols-2 gap-x-4 gap-y-9 sm:grid-cols-4 lg:grid-cols-6">
							{#each related as rel (rel.name)}
								<a href="/{locale}/icons/{rel.name}" class="gsap-on-scroll group text-center" aria-label="{rel.title} icon">
									<span class="relative block aspect-square overflow-hidden rounded-2xl border border-islamic-line bg-islamic-panel transition duration-300 group-hover:-translate-y-1 group-hover:border-islamic-line-strong">
										<img class="size-full object-contain p-8 transition duration-500 group-hover:scale-105" src={pathFor(rel, 'fill')} alt={rel.title} loading="lazy" />
									</span>
									<span class="mt-2 block text-[11px] font-medium text-islamic-muted group-hover:text-islamic-text">{rel.title}</span>
								</a>
							{/each}
						</div>
					</section>
				{/if}
			{/each}
		</div>
	</div>

	<!-- Fixed right sidebar - EXACTLY like Lucide -->
	<aside class="fixed right-0 top-0 hidden h-screen w-[360px] overflow-y-auto border-l border-islamic-line bg-islamic-bg lg:block">
		<div class="p-7">
			<!-- Icon title -->
			<div class="mb-6 border-b border-islamic-line pb-6">
				<span class="text-[9px] font-semibold uppercase tracking-[.18em] text-islamic-green">{pretty(item.category)}</span>
				<h1 class="mt-2 font-display text-2xl font-medium tracking-[-.04em]">{item.title}</h1>
				{#if item.aliases?.length}
					<div class="mt-3 flex flex-wrap gap-2">
						{#each item.aliases as alias (alias)}
							<span class="rounded-full border border-islamic-line px-2 py-1 text-[9px] text-islamic-dim">{alias}</span>
						{/each}
					</div>
				{/if}
			</div>

			<!-- Variants -->
			{#if vars.length > 1}
				<div class="mb-6 border-b border-islamic-line pb-6">
					<span class="mb-3 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">VARIANT</span>
					<div class="flex flex-wrap gap-2">
						{#each vars as v (v)}
							<button type="button" onclick={() => activeVariant = v} aria-pressed={v === variant} class="cursor-pointer rounded-lg border px-3 py-1.5 text-[10px] transition {v === variant ? 'border-islamic-green bg-islamic-green/10 text-islamic-green' : 'border-islamic-line text-islamic-muted hover:text-islamic-text'}">{variantName(v)}</button>
						{/each}
					</div>
				</div>
			{/if}

			<!-- Sources -->
			{#if item.sources.length > 1}
				<div class="mb-6 border-b border-islamic-line pb-6">
					<span class="mb-3 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">SOURCE</span>
					<div class="flex flex-col gap-2">
						{#each item.sources as s (s.id)}
							<button type="button" onclick={() => activeSource = s.id} aria-pressed={s.id === source?.id} class="cursor-pointer rounded-lg border px-3 py-2 text-left text-[10px] transition {s.id === source?.id ? 'border-islamic-green bg-islamic-green/10 text-islamic-green' : 'border-islamic-line text-islamic-muted hover:text-islamic-text'}">{s.label}</button>
						{/each}
					</div>
				</div>
			{/if}

			<!-- Import -->
			<div class="mb-6 border-b border-islamic-line pb-6">
				<span class="mb-2 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">IMPORT</span>
				<div class="overflow-hidden rounded-lg border border-islamic-line bg-black/30">
					<code class="block overflow-x-auto p-3 text-[10px] leading-5 text-islamic-muted">{imp}</code>
				</div>
				<button type="button" onclick={() => copyText(imp)} class="mt-2 w-full cursor-pointer rounded-lg bg-islamic-green px-3 py-2 text-[10px] font-semibold text-islamic-bg transition hover:bg-islamic-green/90">Copy</button>
			</div>

			<!-- Asset path -->
			<div class="mb-6 border-b border-islamic-line pb-6">
				<span class="mb-2 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">ASSET PATH</span>
				<div class="overflow-hidden rounded-lg border border-islamic-line bg-black/30">
					<code class="block overflow-x-auto p-3 text-[10px] leading-5 text-islamic-muted">{path}</code>
				</div>
				<button type="button" onclick={() => copyText(path)} class="mt-2 w-full cursor-pointer rounded-lg border border-islamic-line px-3 py-2 text-[10px] text-islamic-muted transition hover:border-islamic-line-strong hover:text-islamic-text">Copy</button>
			</div>

			<!-- Stats -->
			<div class="mb-6 grid grid-cols-3 gap-2 border-b border-islamic-line pb-6">
				<div class="rounded-lg border border-islamic-line p-3 text-center">
					<span class="block text-[8px] uppercase tracking-[.12em] text-islamic-dim">Category</span>
					<b class="mt-1 block text-[11px]">{pretty(item.category)}</b>
				</div>
				<div class="rounded-lg border border-islamic-line p-3 text-center">
					<span class="block text-[8px] uppercase tracking-[.12em] text-islamic-dim">Sources</span>
					<b class="mt-1 block text-[11px]">{item.sources.length}</b>
				</div>
				<div class="rounded-lg border border-islamic-line p-3 text-center">
					<span class="block text-[8px] uppercase tracking-[.12em] text-islamic-dim">Variants</span>
					<b class="mt-1 block text-[11px]">{vars.length}</b>
				</div>
			</div>

			<!-- HTML usage -->
			<div class="mb-6">
				<span class="mb-2 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">HTML</span>
				<div class="overflow-hidden rounded-lg border border-islamic-line bg-black/30">
					<pre class="overflow-x-auto p-3 text-[9px] leading-5 text-islamic-muted">&lt;img src="{path}" alt="{item.title}" width="24" height="24" /&gt;</pre>
				</div>
			</div>

			<!-- Quick links -->
			<div class="flex flex-col gap-2 text-[10px]">
				<a href="/{locale}/icons" class="flex items-center gap-2 text-islamic-green hover:underline">
					<svg viewBox="0 0 24 24" class="size-3 fill-none stroke-current stroke-2"><path d="M19 12H5M12 19l-7-7 7-7"/></svg>
					Back to icons
				</a>
				<a href="/{locale}/docs/usage" class="text-islamic-muted hover:text-islamic-text">Usage guide</a>
				<a href="/{locale}/sources" class="text-islamic-muted hover:text-islamic-text">Source policy</a>
			</div>
		</div>
	</aside>

	<!-- Mobile sidebar (bottom sheet) -->
	<div class="border-t border-islamic-line bg-islamic-bg lg:hidden">
		<div class="p-6">
			<div class="mb-6">
				<span class="text-[9px] font-semibold uppercase tracking-[.18em] text-islamic-green">{pretty(item.category)}</span>
				<h1 class="mt-2 font-display text-2xl font-medium tracking-[-.04em]">{item.title}</h1>
			</div>
			
			{#if vars.length > 1}
				<div class="mb-6">
					<span class="mb-3 block text-[9px] font-semibold uppercase tracking-[.15em] text-islamic-dim">VARIANT</span>
					<div class="flex flex-wrap gap-2">
						{#each vars as v (v)}
							<button type="button" onclick={() => activeVariant = v} class="cursor-pointer rounded-lg border px-3 py-1.5 text-[10px] {v === variant ? 'border-islamic-green bg-islamic-green/10 text-islamic-green' : 'border-islamic-line text-islamic-muted'}">{variantName(v)}</button>
						{/each}
					</div>
				</div>
			{/if}
		</div>
	</div>
</div>
{:else}
<div class="{MAX} pt-36 pb-16 sm:pt-44 sm:pb-24 lg:pt-32">
	<div class="max-w-lg">
		<span class="text-[10px] font-semibold uppercase tracking-[.18em] text-islamic-green">404</span>
		<h1 class="mt-3 font-display text-5xl tracking-[-.06em]">Icon not found</h1>
		<p class="mt-5 text-base leading-8 text-islamic-muted">No icon named <code class="text-islamic-green">{iconName}</code> exists in the catalog.</p>
		<a href="/{locale}/icons" class="mt-8 inline-flex h-11 items-center gap-2 rounded-xl bg-islamic-green px-5 text-[11px] font-bold text-islamic-bg">
			Browse all icons
			<svg viewBox="0 0 24 24" aria-hidden="true" class="size-4 fill-none stroke-current stroke-[1.8]"><path d="M5 12h13M13 6l6 6-6 6"/></svg>
		</a>
	</div>
</div>
{/if}
