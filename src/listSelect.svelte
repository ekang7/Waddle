<script>
	// import { fade } from 'svelte/transition';
	// let visible = true;
    import { createEventDispatcher } from 'svelte';

    const dispatch = createEventDispatcher();

    export let persons;
    // = [
    //     {name: 'John', checked: false},
    //     {name: 'Jill', checked: false},
    //     {name: 'Adam', checked: false},
    // ]

    // dispatch('message', {
	// 		text: '!persons[0].checked'
	// 	}) ;
    function sendPerson() {
		dispatch('event', {
			text: persons
		});
	}
    
</script>

<div>
    {#each persons as {name, checked}}
        <div>
            <label>
                <input type="checkbox" bind:checked={checked} on:change={sendPerson}/>
                {name}
            </label>
        </div>

        <!-- {#if !person.checked}
        <p transition:fade>Priority</p>
        {/if} -->
    {/each}
</div>

<style>
    /* div {
        display: contents
    } */
</style>



<!-- <script>
	import Checkbox from './Checkbox.svelte'
	let selected = [];

	let items = [
		'Jill',
		'Joe',
		'Adam'
	];

</script>

------ Responded -----<br>
{#each items as item}
		<Checkbox bind:group={selected} value={item}/> {item}<br>
{/each}
------ Direct -----<br>
{#each items as item}
		<input
			type="checkbox"
			bind:group={selected}
			value={item}
		/> {item}<br>
{/each}
------ Selected items -----<br>
{selected && selected.join(', ')} -->