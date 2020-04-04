<script>
  import { onMount, createEventDispatcher } from "svelte";

  export let desc;
  export let completed;
  export let id;

  const dispatch = createEventDispatcher();

  let editing = false;
  let input;

  function toggleStatus() {
    editing = !editing;
  }

  function handleChange(event) {
    if (event.keyCode === 27) {
      input.blur();
    }
  }

  $: if (editing && input) {
    input.focus();
  }
</script>

<li class:completed class:editing>
  <div class="view">
    <input class="toggle" type="checkbox" bind:checked={completed} />
    <label on:dblclick={toggleStatus}>{desc}</label>
    <button on:click={() => dispatch('remove', id)} class="destroy" />
  </div>
  {#if editing}
    <input
      class="edit"
      bind:this={input}
      bind:value={desc}
      on:keydown={handleChange}
      on:blur={toggleStatus} />
  {/if}
</li>
