<script lang="ts">
  import { socketService } from "../socketService";
  import { activePlayerId } from "../store";

  let timeLeft = 0;
  let interval;

  socketService.on('turnChange', (data) => {
    activePlayerId.set(data.currentPlayerId);
    timeLeft = Math.ceil(data.duration / 1000);

    clearInterval(interval);
    interval = setInterval(() => {
      timeLeft--;
      if (timeLeft <= 0) {
        clearInterval(interval);
      }
    }, 1000);
  });
</script>

<div class="timer-container">
  {#if $activePlayerId}
    {#if $activePlayerId === socketService.playerId}
      <div class="turn-pill my-turn">
        <div class="dot"></div>
        <span>Your Turn</span>
        <span class="time">{timeLeft}s</span>
      </div>
    {:else}
      <div class="turn-pill opponent-turn">
        <div class="dot"></div>
        <span>Opponent's Turn</span>
        <span class="time">{timeLeft}s</span>
      </div>
    {/if}
  {/if}
</div>

<style>
  .timer-container {
    text-align: center;
    margin-bottom: 0.5rem;
    height: 36px;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .turn-pill {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 6px 18px;
    border-radius: 20px;
    font-size: 0.8rem;
    font-weight: 500;
    letter-spacing: 0.03em;
    transition: all 0.3s ease;
  }

  .dot {
    width: 6px;
    height: 6px;
    border-radius: 50%;
    flex-shrink: 0;
  }

  .time {
    font-weight: 700;
    font-variant-numeric: tabular-nums;
    min-width: 24px;
    text-align: right;
  }

  .my-turn {
    background: rgba(76, 175, 80, 0.1);
    border: 1px solid rgba(76, 175, 80, 0.2);
    color: #4caf50;
  }

  .my-turn .dot {
    background: #4caf50;
    animation: breathe 1.5s ease-in-out infinite;
  }

  .opponent-turn {
    background: rgba(200, 168, 48, 0.08);
    border: 1px solid rgba(200, 168, 48, 0.15);
    color: #c8a830;
  }

  .opponent-turn .dot {
    background: #c8a830;
    animation: breathe 1.5s ease-in-out infinite;
  }

  @keyframes breathe {
    0%, 100% { opacity: 0.5; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.3); }
  }
</style>