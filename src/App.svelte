<script>
  // Importing components
  import Calendar from '@event-calendar/core';
  import TimeGrid from '@event-calendar/time-grid';
  import Interaction from '@event-calendar/interaction'; 
  import SelectedList from './lib/components/SelectedList.svelte';
  import PrioritizationChooser from './lib/components/PrioritizationChooser.svelte';
  import people_events from './lib/scripts/prepopulatedEvents.js';

  // Initializes list of selected members to all
  let selected_members = ['Alex', 'John', 'Jim', 'Jill', 'Adam', 'Elena', 'Olivia'];

  // List storing if persons are checked by user
  let persons = [
          {name: 'Alex', checked: true},
          {name: 'John', checked: true},
          {name: 'Jim', checked: true},
          {name: 'Jill', checked: true},
          {name: 'Adam', checked: true},
          {name: 'Elena', checked: true},
          {name: 'Olivia', checked: true}
      ];

  // Array of members that are hovered over
  let hoverMembers = []; 
  // Will bind calendar to this variable later. Will pass in to calendar component
  let ec;
  let plugins = [TimeGrid, Interaction];
  // The following line is inspired by ChatGPT. Parses list of prepoulated events into list of events
  let allEvents = Object.values(people_events).reduce((acc, events) => {
                    if (selected_members.includes(events[0].resourceIds[0])) {
                        return acc.concat(events);
                    } else {
                        return acc;
                    }
                    }, []);

  // Options paramter to pass into calendar component
  let options = {
      view: 'timeGridWeek',
      allDaySlot: false,
      selectable: true,
      editable: true,
      slotEventOverlap: true,
      events: allEvents,
      selectBackgroundColor: "#a6d4ff",
      // dateClick: (info) => console.log('date clicked'), for testing
      select: selectFunction, 
      // unselect: unselectFunction, for testing
      eventMouseEnter: mouseFunction, 
      slotMinTime: '07:00:00', 
      slotMaxTime: '19:00:00', 
      buttonText: {today: 'Back to Today'}
  };

  // Function for testing purposes
  // function unselectFunction(info) {
  //   console.log('unselect')
  //   // ec.unselect(); 
  // };

  // Triggered upon select, which occurs when mouseclick is released while creating an event.
  // Adds the event to the calendar to be rendered in green, and calls unselect to
  // prevent an event bubble being displayed until another mouseclick
  function selectFunction(info) {
    ec.addEvent({start: info.start, end: info.end, 
                backgroundColor: "#3eed44", display: 'background'}); 
    ec.unselect();
  };

  // Example function to update uptions, in case we wish to implement
  // function updateOptions() {
  //       options.slotDuration = '01:00';
  //   }

  // Variables and logic for form 'screen'
  let name = "";
  let showForm = true;
  function handleSubmit() {
    showForm = false;
  };

  // Function that handles event dispatched when prioritized member is checked
  // or unchecked. Updates selected members and the events to visualize
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
	};

  // Function that handles when mouse hovers over an event. Updates list of
  // members that are available at the hovered over time
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
  };

</script>

<!-- Logic for form screen or main screen with calendar -->
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

  <div class="container">
      <!-- Prioritization Checklist to select persons -->
      <div class="column">
        <PrioritizationChooser {persons} on:event={handleMessage}></PrioritizationChooser>
      </div>
      <!-- Imported Calendar Component -->
      <div class="column">
        Add your availability below:
        <Calendar bind:this = {ec} {plugins} {options}/>
      </div>
      <!-- Component displaying members available at hovered time -->
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
