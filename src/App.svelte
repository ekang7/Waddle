<script>
  import Counter from './lib/Counter.svelte'
  import Calendar from '@event-calendar/core';
  import TimeGrid from '@event-calendar/time-grid';
  import Interaction from '@event-calendar/interaction'; 
  import addEvent from '@event-calendar/core';
  import SelectedList from './lib/components/SelectedList.svelte';
  import listSelect from './listSelect.svelte';
  import ListSelect from './listSelect.svelte';
  import people_events from './lib/scripts/prepopulatedEvents.js';
  // Parameters for Calendar Component
  // maps people to their list of events

let selected_members = ['Alex', 'John', 'Jim', 'Jill', 'Adam', 'Elena', 'Olivia'];

// automate this list
let persons = [
        {name: 'Alex', checked: true},
        {name: 'John', checked: true},
        {name: 'Jim', checked: true},
        {name: 'Jill', checked: true},
        {name: 'Adam', checked: true},
        {name: 'Elena', checked: true},
        {name: 'Olivia', checked: true}
    ];


//   let selected_members = ["Olivia", "Alex", "Elena"]; // array of selected members
  let hoverMembers = []; // array of members that are hovered over
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

//   function eventMouseEnterFunction(info) {
//     // inspired from Claude AI
//     let hoverPoint = ec.dateFromPoint(info.jsEvent.clientX, info.jsEvent.clientY);
//     let eventsInTimeSlot = allEvents.filter(event => {
//         // Convert dates to timestamps for easy comparison
//         let hoverTimestamp = hoverPoint.date.getTime();  
//         let eventStartTimestamp = event.start.getTime();
//         let eventEndTimestamp = event.end.getTime();

//         return hoverTimestamp >= eventStartTimestamp && 
//               hoverTimestamp < eventEndTimestamp; 
//         });
//     hover_members = eventsInTimeSlot.map(event => event.resourceIds[0]);
//     console.log("HOVER MEMBERS IS NOW " + hover_members);
//   }

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
      unselect: unselectFunction,
      eventMouseEnter: mouseFunction, 
      slotMinTime: '07:00:00', 
      slotMaxTime: '19:00:00', 
      buttonText: {today: 'Back to Today'}
  };

  function unselectFunction(info) {
    ec.unselect(); 
  }
  function selectFunction(info) {
    ec.addEvent({start: info.start, end: info.end, 
                backgroundColor: "#3eed44", display: 'background'}); // do I need to add an id?, "#a6d4ff" color before
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

function mouseFunction(info) {
        let hoverEvent = info.event
        // console.log(hoverEvent)
        let startTime = hoverEvent.start
        let endTime = hoverEvent.end
        let numAvailableInEvent = 0;
        hoverMembers = [];
        for (let i = 0, len_persons = persons.length; i < len_persons; i++) {
                // console.log(people_events[persons[i].name])
                for (let j = 0, len_objects_per_person = people_events[persons[i].name].length; j < len_objects_per_person; j++) {
                        if (startTime >= people_events[persons[i].name][j].start && endTime <= people_events[persons[i].name][j].end) {
                                // hoverMembers.push(persons[i].name)
                                hoverMembers = [...hoverMembers, persons[i].name]
                        }
                }
                // if (persons[i].checked == true) {
                //         selected_members.push(persons[i].name);
                // }
        }
        console.log(hoverMembers)
        // for (let i = 0, len = persons.length; i < len; i++) {
        //         if (persons[i].checked == true) {
        //                 selected_members.push(persons[i].name);
        //         }
        // }
}

</script>


{#if showForm}
  <form on:submit|preventDefault={handleSubmit}>
    <label for="name">Name:</label>
    <input type="text" id="name" bind:value={name} />
    <button type="submit">Submit</button>
  </form>
{:else}
  <!-- <SelectedList {selected_members} {hover_members}/> -->
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
  <div class="container">
      <div class="column">
        <ListSelect {persons} on:event={handleMessage}></ListSelect>
      </div>
      <div class="column">
        Add your availability below:
        <Calendar bind:this = {ec} {plugins} {options}/>
      </div>
      <div class="column">
        <h4>Available at time block:</h4>
        <SelectedList {selected_members} {hoverMembers}/>
      </div>
  </div>

    <!-- Counter Component -->
    <!-- <div class="card">
      <Counter />
    </div> -->

  </main>

<style>
  html, body {
    display: contents;
    width: 100%;
    background-color: yellow;
  }
  main {
    display: contents;
    width: 100%;
    background-color: red; 
  }
  .container {
    display: flex;
    width: 100% ;
    justify-content: center;
    gap: 20px; 
  }
  .column { 
    flex: 1; 
    padding: 10px;
    box-sizing: border-box;
  }
  .ec {
    --ec-today-bg-color: transparent;
  }
  .ec-button {
    color: #787877;
    --ec-button-text-color: #787877;
  }

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