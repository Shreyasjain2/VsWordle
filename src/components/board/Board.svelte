<script lang="ts">
  import type { LetterState } from "../types"; // Or define locally

  export let words: string[] = Array(6).fill("");
  export let states: LetterState[][] = Array.from({ length: 6 }, () => Array(5).fill("🔳"));
</script>

<div class="board">
  {#each states as rowState, r}
    <div class="row">
      {#each rowState as tileState, c}
        <div class="tile {tileState}">{words[r][c] || ''}</div>
      {/each}
    </div>
  {/each}
</div>

<style>
  .board {
    display: grid;
    grid-auto-rows: 68px; /* Fixed row height to allow infinite scrolling */
    gap: 8px;
    width: 100%;
    max-width: 380px; /* Slightly larger board size */
    margin: 0 auto;
  }
  .row {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 6px;
  }
  .tile {
    width: 100%;
    height: 100%;
    display: inline-flex;
    justify-content: center;
    align-items: center;
    font-size: 2rem;
    font-weight: 700;
    text-transform: uppercase;
    border: 2px solid rgba(255,255,255,0.08);
    border-radius: 6px;
    box-sizing: border-box;
    color: #ffffff;
    transition: background-color 0.15s ease, border-color 0.15s ease;
    font-family: 'Inter', sans-serif;
  }
  
  .tile.🟩 { background-color: #4caf50; border-color: #4caf50; }
  .tile.🟨 { background-color: #c8a830; border-color: #c8a830; }
  .tile.⬛ { background-color: #2a2d31; border-color: #2a2d31; }
  .tile.🔳 { background-color: transparent; border-color: rgba(255,255,255,0.08); }
</style>