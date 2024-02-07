<script>
  import Counter from './lib/Counter.svelte'
  import Calendar from '@event-calendar/core';
  import TimeGrid from '@event-calendar/time-grid';
  import Interaction from '@event-calendar/interaction'; 
  import addEvent from '@event-calendar/core';
  import SelectedList from './lib/components/SelectedList.svelte';
  import PrePopulated from './prePopulated.svelte';
  import listSelect from './listSelect.svelte';
  import ListSelect from './listSelect.svelte';
  import people_events from './lib/scripts/prepopulatedEvents.js';
  // Parameters for Calendar Component
  // maps people to their list of events

let selected_members = ['Alex', 'John', 'Jim', 'Jill', 'Adam', 'Elena', 'Olivia'];

// automate this list
let persons = [
        {name: 'Alex', checked: false},
        {name: 'John', checked: false},
        {name: 'Jim', checked: false},
        {name: 'Jill', checked: false},
        {name: 'Adam', checked: false},
        {name: 'Elena', checked: false},
        {name: 'Olivia', checked: false}
    ];


//   let selected_members = ["Olivia", "Alex", "Elena"]; // array of selected members
  let hover_members = ["Adam"]; // array of members that are hovered over
//   let people_events = PrePopulated; // why doesn't this work
   


  let select_bool = false;
  let ec;
  let plugins = [TimeGrid, Interaction];
  // the following line is inspired by ChatGPT
  let allEvents = Object.values(people_events).reduce((acc, events) => {
                    if (selected_members.includes(events[0].resourceIds[0])) {
                        return acc.concat(events);
                    } else {
                        return acc;
                    }
                    }, []);

  function eventMouseEnterFunction(info) {
    // inspired from Claude AI
    let hoverPoint = ec.dateFromPoint(info.jsEvent.clientX, info.jsEvent.clientY);
    let eventsInTimeSlot = allEvents.filter(event => {
        // Convert dates to timestamps for easy comparison
        let hoverTimestamp = hoverPoint.date.getTime();  
        let eventStartTimestamp = event.start.getTime();
        let eventEndTimestamp = event.end.getTime();

        return hoverTimestamp >= eventStartTimestamp && 
              hoverTimestamp < eventEndTimestamp; 
        });
    hover_members = eventsInTimeSlot.map(event => event.resourceIds[0]);
    console.log("HOVER MEMBERS IS NOW " + hover_members);
  }

  let options = {
      view: 'timeGridWeek',
      allDaySlot: false,
      selectable: true,
      editable: true,
      slotEventOverlap: true, // allows events to overlap
      events: allEvents,
      selectBackgroundColor: "#a6d4ff",
      dateClick: (info) => console.log('hi'),
      select: selectFunction, 
      eventMouseEnter: eventMouseEnterFunction
  };

  function selectFunction(info) {
    ec.addEvent({start: info.start, end: info.end, 
                backgroundColor: "#a6d4ff", display: 'background'}); // do I need to add an id?, "#a6d4ff" color before
    ec.unselect();
    // ec.eventAllUpdated();

    console.log(info.start);
    // console.log(options.events);
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

  function handleMessage(event) {
		// alert(event.detail.text);
                console.log('message received');
                persons = event.detail.text
                console.log(persons[2].checked)
                selected_members = [] // reset to zero
                for (let i = 0, len = persons.length; i < len; i++) {
                        if (persons[i].checked == true) {
                                selected_members.push(persons[i].name);
                        }
                }
                console.log(persons, selected_members)
                let updatedEvents = Object.values(people_events).reduce((acc, events) => {
                    if (selected_members.includes(events[0].resourceIds[0])) {
                        return acc.concat(events);
                    } else {
                        return acc;
                    }
                    }, []);
                options.events = updatedEvents;
	} ;

</script>


{#if showForm}
  <form on:submit|preventDefault={handleSubmit}>
    <label for="name">Name:</label>
    <input type="text" id="name" bind:value={name} />
    <button type="submit">Submit</button>
  </form>
{:else}
  <SelectedList {selected_members} {hover_members}/>
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
  <div>
        <ListSelect {persons} on:event={handleMessage}></ListSelect>
        <Calendar bind:this = {ec} {plugins} {options} />
  </div>

    <!-- Counter Component -->
    <!-- <div class="card">
      <Counter />
    </div> -->

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
    /* div {
        display:

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