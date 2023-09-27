<script lang="ts">
	export let file: string
	export let styles: string
</script>

{#if file}
	<section class={styles} data-markdown={file} />
{:else}
	<section data-markdown>
		<div data-template>
			<slot />
		</div>
	</section>
{/if}
