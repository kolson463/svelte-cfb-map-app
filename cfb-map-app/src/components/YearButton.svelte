<script type="ts">
	export let year: number;
	export let selectedYear: number;
	export let selectedColor: string;
</script>

{#if selectedYear != year}
	<button on:click class="  bg-white  p-2 text-black rounded-md hover:bg-green-400 w-full"
		>{year} </button
	>
{:else}
	<button class="border-2  text-black bg-white rounded-md p-2 font-semibold  w-full cursor-default"
	style='border-color: {selectedColor}'
		>{year} </button
	>
{/if}
