<script>
  import { onMount } from "svelte";
  import TodoItem from "./todo-item.svelte";

  let mounted = false;
  let status = "all";
  let inputValue = "";
  let todos = [];

  function toggleAll(event) {
    todos = todos.map(item => ({ ...item, completed: event.target.checked }));
  }

  function clearCompleted() {
    todos = todos.filter(item => !item.completed);
  }

  function remove(event) {
    todos = todos.filter(item => item.id !== event.detail);
  }

  function onkeyDown(event) {
    if (event.keyCode === 13 && inputValue) {
      todos = [
        {
          id: Date.now().toString(),
          desc: inputValue,
          completed: false
        },
        ...todos
      ];
      inputValue = "";
    }
  }

  function update() {
    if (window.location.hash === "#/active") {
      status = "active";
    } else if (window.location.hash === "#/completed") {
      status = "completed";
    } else {
      status = "all";
    }
  }

  $: filtered =
    status === "all"
      ? todos
      : todos.filter(item => item.completed === (status === "completed"));

  $: allCompleted = todos.length > 0 && todos.every(item => item.completed);
  $: completedCount = todos.filter(item => !item.completed).length;
  $: uncompletedCount = todos.length - completedCount;
  $: mounted && window.localStorage.setItem("todos", JSON.stringify(todos));

  onMount(() => {
    mounted = true;
    try {
      const cache = JSON.parse(window.localStorage.getItem("todos"));
      if (Array.isArray(cache)) {
        todos = cache;
      }
    } catch {
      todos = [];
    }

    window.addEventListener("hashchange", update);

    update();
  });
</script>

<section class="todoapp">
  <header class="header">
    <h1>todos</h1>
    <input
      class="new-todo"
      on:keydown={onkeyDown}
      placeholder="What needs to be done?"
      bind:value={inputValue} />
  </header>

  <section class="main">
    <input
      id="toggle-all"
      class="toggle-all"
      type="checkbox"
      on:change={toggleAll}
      checked={allCompleted} />
    <label for="toggle-all">Mark all as complete</label>

    <ul class="todo-list">
      {#each filtered as item}
        <TodoItem
          id={item.id}
          on:remove={remove}
          bind:desc={item.desc}
          bind:completed={item.completed} />
      {/each}
    </ul>

  </section>

  <footer class="footer">
    <span class="todo-count">
      <strong>{completedCount}</strong>
      item left
    </span>
    <ul class="filters">
      <li>
        <a class:selected={status === 'all'} href="#/">All</a>
      </li>
      <li>
        <a class:selected={status === 'active'} href="#/active">Active</a>
      </li>
      <li>
        <a class:selected={status === 'completed'} href="#/completed">
          Completed
        </a>
      </li>
    </ul>
    {#if uncompletedCount > 0}
      <button class="clear-completed" on:click={clearCompleted}>
        Clear completed
      </button>
    {/if}
  </footer>

</section>
