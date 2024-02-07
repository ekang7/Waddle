<script>
  export let selected_members; // array of selected members
  export let hoverMembers; // array of members being hovered over
</script>

<style>
  /* Add your component styles here */ 
    /* .selected {
        font-weight: bold; 
        text-decoration: underline;
    } */
</style>

<div>
  <!-- {#each selected_members as member}
  <ol class="selected">{member}</ol>
  {/each} -->

  {#each hoverMembers as member}
    <!-- <ol>{member}</ol> -->
    <div>{member}</div>
  {/each}
</div>

<!-- {#each hover_members as member}
    {#if selected_members.includes(member)}
        <ol class = "selected">{member}</ol>
    {:else}
        <ol>{member}</ol>
    {/if}
{/each} -->

