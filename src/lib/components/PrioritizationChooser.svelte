<!--This component lets the user choose which members to prioritize, 
    choosing who's availabilities to see on the calendar -->
<script>
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let persons;

    function sendPerson() {
		dispatch('event', {
			text: persons
		});
	}
    
</script>

<div>
    Select members to prioritize:
    {#each persons as {name, checked}}
        <div>
            <label>
                <input type="checkbox" bind:checked={checked} on:change={sendPerson}/>
                {name}
            </label>
        </div>
    {/each}
</div>