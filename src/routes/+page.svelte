<script lang="ts">
  import type { Todo } from '../types/todo';
  import { onMount } from 'svelte';

  let todos = $state([] as Todo[]);
  let newTodo: string = $state('');
  let isInitialized: boolean = $state(false);

  onMount(() => {
    if (typeof window !== 'undefined') {
      try {
        const savedTodos = localStorage.getItem('todos');
        if (savedTodos) {
          todos = JSON.parse(savedTodos);
        }
      } catch (e) {
        console.error('Failed to load todos from localStorage:', e);
      } finally {
        isInitialized = true;
      }
    }
  });

  $effect(() => {
    if (isInitialized && typeof window !== 'undefined') {
      try {
        localStorage.setItem('todos', JSON.stringify(todos));
      } catch (e) {
        console.error('Failed to save todos to localStorage:', e);
      }
    }
  });

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
