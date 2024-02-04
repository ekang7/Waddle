<script>
  import svelteLogo from './assets/svelte.svg'
  import viteLogo from '/vite.svg'
  import Counter from './lib/Counter.svelte'
  import Calendar from '@event-calendar/core';
  import TimeGrid from '@event-calendar/time-grid';
  import Interaction from '@event-calendar/interaction'; 
  import addEvent from '@event-calendar/core';

  // Parameters for Calendar Component

  let ec;
  let plugins = [TimeGrid, Interaction];
  let options = {
      view: 'timeGridWeek',
      selectable: true,
      editable: true,
      events: [
          // your list of events
      ],
      dateClick: (info) => console.log('hi')
  };

  // Example function to update uptions
  function updateOptions() {
        options.slotDuration = '01:00';
    }

  // trying to test console.log
  console.log("hello")
  
</script>

<main>

<!-- Toggle Day or Week View -->
  {#if options.view == 'timeGridWeek'}
    <button on:click= {() => {options.view = 'timeGridDay'}} >Day View</button>
  {:else}
    <button on:click= {() => {options.view = 'timeGridWeek'}} >Week View</button>
  {/if}

  <!-- Attempting to add a new event to calendar -->
  <!-- <button on:click={addEvent(
    {
      id: 10, 
      start: new Date(2024, 2, 4, 11:00),z
      end: new Date(2024, 02, 04, 11:30)
    }
    )}>Change slot duration</button> -->

  <!-- Imported Calendar Component -->
  <Calendar bind:this = {ec} {plugins} {options} />

  <!-- Counter Component -->
  <div class="card">
    <Counter />
  </div>

</main>

<style>
  /* .logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
  }
  .logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
  }
  .logo.svelte:hover {
    filter: drop-shadow(0 0 2em #ff3e00aa);
  }
  .read-the-docs {
    color: #888;
  } */
</style>



<!-- BOILER PLATE in MAIN
  <div>
    <a href="https://vitejs.dev" target="_blank" rel="noreferrer">
      <img src={viteLogo} class="logo" alt="Vite Logo" />
    </a>
    <a href="https://svelte.dev" target="_blank" rel="noreferrer">
      <img src={svelteLogo} class="logo svelte" alt="Svelte Logo" />
    </a>
  </div>
  <h1>Vite + Svelte</h1> -->

  <!-- <p>
    Check out <a href="https://github.com/sveltejs/kit#readme" target="_blank" rel="noreferrer">SvelteKit</a>, the official Svelte app framework powered by Vite!
  </p> -->

  <!-- <p class="read-the-docs">
    Click on the Vite and Svelte logos to learn more
  </p> -->