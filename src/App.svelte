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
      slotEventOverlap: true, // allows events to overlap
      events: [
          // your list of events
          {
              id: "Gym",
              resourceIds: ["John Doe"],
              allDay: false,
              start: new Date(2024, 1, 4, 13, 0), 
              end: new Date(2024, 1, 4, 19, 0), 
              title: "Gym",
              editable: false,
              startEditable: false,
              durationEditable: false,
              display: 'background',
              backgroundColor: "#a6d4ff", // Blueish color
              textColor: "#ffffff", // White color
              extendedProps: {
                  description: "I am going to the gym",
                  location: "Quincy House Gym"
              }
          }, 
          {
              id: "Gym",
              resourceIds: ["Jane Doe"],
              allDay: false,
              start: new Date(2024, 1, 4, 9, 0), 
              end: new Date(2024, 1, 4, 17, 0), 
              title: "Gym",
              editable: false,
              startEditable: false,
              durationEditable: false,
              display: 'background',
              backgroundColor: "#a6d4ff", // Blueish color
              textColor: "#ffffff", // White color
              extendedProps: {
                  description: "I am going to the gym",
                  location: "Quincy House Gym"
              }
          }, 
          {
              id: "Gym",
              resourceIds: ["Jim Doe"],
              allDay: false,
              start: new Date(2024, 1, 4, 9, 0), 
              end: new Date(2024, 1, 4, 17, 0), 
              title: "Gym",
              editable: false,
              startEditable: false,
              durationEditable: false,
              display: 'background',
              backgroundColor: "#a6d4ff", // Blueish color
              textColor: "#ffffff", // White color
              extendedProps: {
                  description: "I am going to the gym",
                  location: "Quincy House Gym"
              }
          }
      ],
      selectBackgroundColor: "#a6d4ff",
      dateClick: (info) => console.log('hi'),
      select: selectFunction
  };

  function selectFunction(info) {
    ec.addEvent({start: info.start, end: info.end, 
                backgroundColor: "#a6d4ff", display: 'background'}); // do I need to add an id?
    // console.log(info.start);
    console.log(options.events);
  }

  // Example function to update uptions
  function updateOptions() {
        options.slotDuration = '01:00';
    }

  let name = "";
  let showForm = true;
  function handleSubmit() {
    showForm = false;
  }
</script>
{#if showForm}
  <form on:submit|preventDefault={handleSubmit}>
    <label for="name">Name:</label>
    <input type="text" id="name" bind:value={name} />
    <button type="submit">Submit</button>
  </form>
{:else}
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

  {/if}