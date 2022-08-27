<script type="ts">
	export let conference: string;
	export let selectedConference: string;
	export let selectedColor: string;
</script>

{#if selectedConference != conference}
	<button on:click class=" p-2 text-black rounded-md font-normal hover:bg-green-400 w-full bg-white"
		>{conference} </button
	>
{:else}
	<button class="border-2  text-black rounded-md p-2 font-semibold  w-full cursor-default bg-white"
	style="border-color: {selectedColor}"
		>{conference} </button
	>
{/if}
