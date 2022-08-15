<script type="ts">
	type teamInfo = { name: string; slug: string; location: string };

	export let selectedTeam: string;
	export let team: teamInfo;
</script>

{#if selectedTeam != team.name}
    <button on:click class="border-black border-2 p-2 rounded-md hover:bg-yellow-300 w-full">{team.name}</button>

    {:else} <button class="border border-black rounded-md p-2 font-semibold bg-red-500 w-full cursor-default">{team.name}</button>


{/if}

