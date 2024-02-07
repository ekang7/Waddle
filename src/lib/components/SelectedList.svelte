<script>
  export let selected_members; // array of selected members
  export let hover_members; // array of members being hovered over
</script>

<style>
  /* Add your component styles here */
    .selected {
        font-weight: bold; 
        text-decoration: underline;
    }
</style>

{#each selected_members as member}
  <ol class="selected">{member}</ol>
{/each}

{#each hover_members as member}
  <ol>{member}</ol>
{/each}

