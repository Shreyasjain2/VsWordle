<script lang="ts">
  import { createEventDispatcher } from 'svelte';
  import type { LetterStates } from '../utils';

  export let letterStates: LetterStates;
  export let disabled: boolean = false;
  const dispatch = createEventDispatcher();

  const keys1 = "qwertyuiop".split('');
  const keys2 = "asdfghjkl".split('');
  const keys3 = "zxcvbnm".split('');

  
  function handleClick(key: string) {
    if (disabled) return; // Prevent dispatching if disabled
    dispatch('key', key);
  }
</script>

<div class="keyboard">
  <div class="row">{#each keys1 as key}<button class={letterStates[key]} on:click={() => handleClick(key)}>{key}</button>{/each}</div>
  <div class="row">{#each keys2 as key}<button class={letterStates[key]} on:click={() => handleClick(key)}>{key}</button>{/each}</div>
  <div class="row">
    <button class="wide" on:click={() => handleClick('enter')}>Enter</button>
    {#each keys3 as key}<button class={letterStates[key]} on:click={() => handleClick(key)}>{key}</button>{/each}
    <button class="wide" on:click={() => handleClick('backspace')}>⌫</button>
  </div>
</div>

<style>
  .keyboard {
    display: flex;
    flex-direction: column;
    gap: 6px;
    width: 100%;
    max-width: 500px;
    margin: 0 auto;
  }

  .row {
    display: flex;
    gap: 5px;
    justify-content: center;
  }

  button {
    background-color: #3a3d42;
    color: #e8eaed;
    padding: 15px 6px;
    font-weight: 600;
    font-size: 0.8rem;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    flex: 1;
    text-transform: uppercase;
    font-family: 'Inter', sans-serif;
    letter-spacing: 0.02em;
    transition: all 0.15s ease;
    max-width: 44px;
  }

  button:hover {
    background-color: #4a4d52;
    transform: translateY(-1px);
  }

  button:active {
    transform: translateY(0);
  }

  button.wide {
    flex: 1.5;
    max-width: 68px;
    font-size: 0.7rem;
    letter-spacing: 0.04em;
  }
  
  button.🟩 { background-color: #4caf50; color: #ffffff; }
  button.🟨 { background-color: #c8a830; color: #ffffff; }
  button.⬛ { background-color: #1a1d21; color: rgba(255,255,255,0.35); }

  .keyboard.disabled {
    opacity: 0.5;
    pointer-events: none;
  }
</style>