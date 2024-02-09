<!--This component displays a list of members separated by whether they 
  are prioritized or not when the user hovers over a particular time block -->

<script>
  export let selected_members; // array of selected members
  export let hoverMembers; // array of members being hovered over
</script>

<style>
    .selected {
        font-weight: bold; 
        text-decoration: underline;
    }    
</style>

<div>
  Prioritized Members
  {#each hoverMembers as member}
  {#if selected_members.includes(member)}
      <ol class = "selected">{member}</ol>
  {/if}
{/each}
<br/>
  Others
  {#each hoverMembers as member}
  {#if !selected_members.includes(member)}
      <ol>{member}</ol>
  {/if}
{/each}
</div>
