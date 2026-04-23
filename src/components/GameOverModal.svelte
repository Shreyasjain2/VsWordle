<script lang="ts">
  import { socketService } from "../socketService";

  // Props to control the modal's content and visibility
  export let isOpen = false;
  export let winnerId = '';
  export let gameOverDetails: any = null;

  // Helper function to format time
  const formatTime = (ms) => (ms / 1000).toFixed(2);
</script>

{#if isOpen}
  <div class="modal-backdrop">
    <div class="modal-content">
      {#if winnerId === socketService.playerId}
        <div class="result-icon win">🏆</div>
        <h2>Victory!</h2>
        <p class="result-sub">You outsmarted your opponent.</p>
      {:else}
        <div class="result-icon lose">✕</div>
        <h2>Defeat</h2>
        <p class="result-sub">Better luck next time.</p>
      {/if}

      {#if gameOverDetails?.reason === 'tiebreaker'}
        <div class="tiebreaker-details">
          <div class="tb-header">
            {#if winnerId === socketService.playerId}
              Won by Tiebreaker
            {:else}
              Lost by Tiebreaker
            {/if}
          </div>
          <div class="tb-row">
            <span class="tb-label">Your Time</span>
            <span class="tb-value">{formatTime(gameOverDetails.details.times[socketService.playerId])}s</span>
          </div>
          <div class="tb-row">
            <span class="tb-label">Opponent's Time</span>
            <span class="tb-value">{formatTime(gameOverDetails.details.times[Object.keys(gameOverDetails.details.times).find(id => id !== socketService.playerId)])}s</span>
          </div>
        </div>
      {/if}

      <button class="play-again" on:click={() => window.location.reload()}>Play Again</button>
    </div>
  </div>
{/if}

<style>
  .modal-backdrop {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.7);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 100;
    animation: fadeIn 0.3s ease;
  }

  .modal-content {
    background: rgba(18, 20, 24, 0.95);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: 20px;
    padding: 40px 48px;
    text-align: center;
    box-shadow: 0 24px 48px rgba(0, 0, 0, 0.4);
    animation: slideIn 0.35s ease-out;
    max-width: 380px;
    width: 90%;
  }

  .result-icon {
    width: 64px;
    height: 64px;
    border-radius: 50%;
    display: inline-flex;
    justify-content: center;
    align-items: center;
    font-size: 1.8rem;
    margin-bottom: 16px;
  }

  .result-icon.win {
    background: rgba(76, 175, 80, 0.12);
    border: 1px solid rgba(76, 175, 80, 0.25);
  }

  .result-icon.lose {
    background: rgba(229, 69, 69, 0.12);
    border: 1px solid rgba(229, 69, 69, 0.25);
    color: #e54545;
    font-weight: 700;
    font-size: 1.5rem;
  }

  h2 {
    margin-top: 0;
    font-size: 1.6rem;
    font-weight: 700;
    color: #ffffff;
    letter-spacing: 0.04em;
  }

  .result-sub {
    font-size: 0.85rem;
    color: rgba(255,255,255,0.35);
    margin-bottom: 8px;
  }

  .tiebreaker-details {
    background: rgba(255,255,255,0.03);
    border: 1px solid rgba(255,255,255,0.08);
    border-radius: 12px;
    padding: 16px 20px;
    margin: 20px auto;
    max-width: 280px;
  }

  .tb-header {
    font-size: 0.75rem;
    font-weight: 600;
    text-transform: uppercase;
    letter-spacing: 0.12em;
    color: rgba(255,255,255,0.4);
    margin-bottom: 12px;
    padding-bottom: 8px;
    border-bottom: 1px solid rgba(255,255,255,0.06);
  }
  
  .tb-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 4px 0;
  }

  .tb-label {
    font-size: 0.8rem;
    color: rgba(255,255,255,0.45);
  }

  .tb-value {
    font-size: 0.9rem;
    font-weight: 600;
    color: #ffffff;
    font-variant-numeric: tabular-nums;
  }

  .play-again {
    padding: 14px 32px;
    font-size: 0.9rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    margin-top: 24px;
    background: #ffffff;
    color: #0c0d10;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s ease;
    font-family: 'Inter', sans-serif;
  }

  .play-again:hover {
    background: rgba(255,255,255,0.88);
    transform: translateY(-1px);
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  @keyframes slideIn {
    from { transform: translateY(-20px) scale(0.96); opacity: 0; }
    to { transform: translateY(0) scale(1); opacity: 1; }
  }
</style>