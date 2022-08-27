<script type="ts">
	type teamInfo = { name: string; slug: string; location: string };

	export let selectedTeam: string;
	export let team: teamInfo;
	export let selectedColor: string;
</script>

{#if selectedTeam != team.name}
    <button on:click class=" p-2 text-black rounded-md hover:bg-green-400 w-full">{team.name}</button>

    {:else} <button class="border-2 rounded-md p-2 font-semibold text-black  w-full cursor-default"
	style='border-color: {selectedColor}'>{team.name}</button>


{/if}

