<script lang="ts">
  import type { Todo } from '../types/todo';

  let todos: Todo[] = $state([
    { text: 'Apple', done: false },
    { text: 'パン', done: false },
    { text: '牛乳', done: true },
    { text: 'Coffee', done: false }
  ]);
  let newTodo: string = $state('');

  function resetInput() {
    newTodo = '';
  }

  function addTodo() {
    if (newTodo.trim()) {
      todos.push({ text: newTodo, done: false });
      resetInput();
    }
  }

  function deleteTodo(index: number) {
    todos.splice(index, 1);
  }
</script>

<main>
  <h1>Todo App</h1>
  <input
    type="text"
    bind:value={newTodo}
    placeholder="New task..."
    onkeydown={(e) => {
      if (e.key === 'Enter' && !e.isComposing) {
        addTodo();
      }
    }}
  />
  <button onclick={addTodo} disabled={!newTodo.trim()}>Add</button>

  <ul>
    {#each todos as todo, i}
      <li>
        <input type="checkbox" bind:checked={todo.done} />
        <span class={todo.done ? 'done' : ''}>{todo.text}</span>
        <button onclick={() => deleteTodo(i)}>Delete</button>
      </li>
    {/each}
  </ul>
</main>

<style>
  span.done {
    text-decoration: line-through;
  }

  button[disabled] {
    opacity: 0.5;
    cursor: not-allowed;
  }
</style>
