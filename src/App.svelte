<script>
  let name = "";
  import Test from "./test.svelte";
  let showForm = true;
  function handleSubmit() {
    showForm = false;
  }
</script>

<style>
</style>

{#if showForm}
  <form on:submit|preventDefault={handleSubmit}>
    <label for="name">Name:</label>
    <input type="text" id="name" bind:value={name} />
    <button type="submit">Submit</button>
  </form>
{:else}
  <Test />
{/if}
