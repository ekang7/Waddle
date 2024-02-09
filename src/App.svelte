<script>
  import Calendar from '@event-calendar/core';
  import TimeGrid from '@event-calendar/time-grid';
  import Interaction from '@event-calendar/interaction'; 
  import SelectedList from './lib/components/SelectedList.svelte';
  import PrioritizationChooser from './lib/components/PrioritizationChooser.svelte';
  import people_events from './lib/scripts/prepopulatedEvents.js';
  // Parameters for Calendar Component
  // maps people to their list of events

let selected_members = ['Alex', 'John', 'Jim', 'Jill', 'Adam', 'Elena', 'Olivia'];

let persons = [
        {name: 'Alex', checked: true},
        {name: 'John', checked: true},
        {name: 'Jim', checked: true},
        {name: 'Jill', checked: true},
        {name: 'Adam', checked: true},
        {name: 'Elena', checked: true},
        {name: 'Olivia', checked: true}
    ];


  let hoverMembers = []; // array of members that are hovered over
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
                backgroundColor: "#3eed44", display: 'background'}); 
    ec.unselect();
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
    persons = event.detail.text
    selected_members = [] // reset to zero
    for (let i = 0, len = persons.length; i < len; i++) {
            if (persons[i].checked == true) {
              selected_members.push(persons[i].name);
            }
    }
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
  let startTime = hoverEvent.start
  let endTime = hoverEvent.end
  let numAvailableInEvent = 0;
  hoverMembers = [];
  for (let i = 0, len_persons = persons.length; i < len_persons; i++) {
    for (let j = 0, len_objects_per_person = people_events[persons[i].name].length; 
                                                  j < len_objects_per_person; j++) {
      if (startTime >= people_events[persons[i].name][j].start && 
          endTime <= people_events[persons[i].name][j].end) {
        hoverMembers = [...hoverMembers, persons[i].name]
      }
    }
  }   
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

  <!-- Imported Calendar Component -->
  <div class="container">
      <div class="column">
        <PrioritizationChooser {persons} on:event={handleMessage}></PrioritizationChooser>
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
    .ec-button.ec-today {
      color: #787877;
      --ec-button-text-color: #787877;
    }
    /* from ChatGPT */
    .container .column:nth-child(2) { /* Specifically targets the second (center) column */
      flex: 2; /* Gives the center column twice the flex-grow factor of the others */
    }
  </style>
{/if}
