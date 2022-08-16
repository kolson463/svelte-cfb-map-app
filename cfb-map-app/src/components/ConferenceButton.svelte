<script type="ts">
	export let conference: string;
	export let selectedConference: string;
</script>

{#if selectedConference != conference}
	<button on:click class="border-black border-2 p-2 text-black rounded-md hover:bg-yellow-300 w-full"
		>{conference}</button
	>
{:else}
	<button class="border border-black text-black rounded-md p-2 font-semibold bg-red-500 w-full cursor-default"
		>{conference}</button
	>
{/if}
