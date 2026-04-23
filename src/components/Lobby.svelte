<script lang="ts">
  import { socketService } from '../socketService';
  import { gameState, roomId, errorMessage } from '../store';

  let joinRoomId = '';

  function handleCreateRoom() {
    socketService.createRoom();
  }

  function handleJoinRoom() {
    if (joinRoomId.trim()) {
      socketService.joinRoom(joinRoomId.trim());
    }
  }

  // When a room is created, the server sends back the ID
  socketService.on('roomCreated', (data) => {
    roomId.set(data.roomId);
  });

  // When game starts (2 players have joined)
  socketService.on('gameStart', (data) => {
    roomId.set(data.roomId);
    gameState.set('settingWord');
  });

  // Handle errors from the server
  socketService.on('error', (data) => {
    errorMessage.set(data.message);
    setTimeout(() => errorMessage.set(null), 3000); // Clear error after 3s
  });
</script>

<div class="lobby-container">
  <div class="lobby-card">
    <div class="logo-section">
      <h1>VS WORDLE</h1>
      <p class="subtitle">Challenge a friend. Outsmart your opponent.</p>
    </div>
    
    {#if $roomId && $gameState === 'lobby'}
      <div class="room-info">
        <p class="label">Room Created — Share this code:</p>
        <div class="room-code-wrapper">
          <strong class="room-code">{$roomId}</strong>
        </div>
        <div class="waiting">
          <div class="pulse-dot"></div>
          <p>Waiting for an opponent to join...</p>
        </div>
      </div>
    {:else if !$roomId}
      <div class="actions">
        <button class="btn-primary" on:click={handleCreateRoom}>Create Game</button>
        <div class="divider">
          <span>or</span>
        </div>
        <div class="join-game">
          <input type="text" bind:value={joinRoomId} placeholder="Enter room code" />
          <button class="btn-secondary" on:click={handleJoinRoom}>Join</button>
        </div>
      </div>
    {/if}

    {#if $errorMessage}
      <p class="error">{$errorMessage}</p>
    {/if}
  </div>
</div>

<style>
  .lobby-container {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
  }

  .lobby-card {
    text-align: center;
    width: 100%;
    max-width: 420px;
    padding: 48px 40px;
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255, 255, 255, 0.06);
    border-radius: 20px;
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
  }

  .logo-section {
    margin-bottom: 40px;
  }

  .logo-section h1 {
    font-size: 2.2rem;
    font-weight: 800;
    letter-spacing: 0.4rem;
    color: #ffffff;
    margin-bottom: 8px;
  }

  .subtitle {
    font-size: 0.85rem;
    color: rgba(255,255,255,0.35);
    letter-spacing: 0.04em;
    font-weight: 400;
  }

  .actions {
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  .btn-primary {
    width: 100%;
    padding: 14px 20px;
    font-size: 0.9rem;
    font-weight: 600;
    letter-spacing: 0.08em;
    text-transform: uppercase;
    background: #ffffff;
    color: #0c0d10;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s ease;
    font-family: 'Inter', sans-serif;
  }

  .btn-primary:hover {
    background: rgba(255,255,255,0.88);
    transform: translateY(-1px);
  }

  .divider {
    display: flex;
    align-items: center;
    margin: 20px 0;
    gap: 12px;
  }

  .divider::before,
  .divider::after {
    content: '';
    flex: 1;
    height: 1px;
    background: rgba(255,255,255,0.08);
  }

  .divider span {
    font-size: 0.75rem;
    color: rgba(255,255,255,0.25);
    text-transform: uppercase;
    letter-spacing: 0.1em;
    font-weight: 500;
  }

  .join-game {
    display: flex;
    gap: 8px;
  }

  input {
    flex: 1;
    padding: 12px 16px;
    font-size: 0.9rem;
    background: rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 8px;
    color: #e8eaed;
    outline: none;
    font-family: 'Inter', sans-serif;
    letter-spacing: 0.05em;
    transition: border-color 0.2s ease;
  }

  input::placeholder {
    color: rgba(255,255,255,0.2);
  }

  input:focus {
    border-color: rgba(255,255,255,0.25);
  }

  .btn-secondary {
    padding: 12px 24px;
    font-size: 0.85rem;
    font-weight: 600;
    letter-spacing: 0.06em;
    text-transform: uppercase;
    background: transparent;
    color: #ffffff;
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 8px;
    cursor: pointer;
    transition: all 0.2s ease;
    font-family: 'Inter', sans-serif;
    white-space: nowrap;
  }

  .btn-secondary:hover {
    background: rgba(255,255,255,0.05);
    border-color: rgba(255,255,255,0.3);
  }

  .room-info {
    margin-top: 0;
  }

  .room-info .label {
    font-size: 0.8rem;
    color: rgba(255,255,255,0.4);
    margin-bottom: 12px;
    letter-spacing: 0.02em;
  }

  .room-code-wrapper {
    margin-bottom: 20px;
  }

  .room-code {
    font-size: 1.8rem;
    font-weight: 700;
    color: #ffffff;
    letter-spacing: 0.3em;
    padding: 12px 24px;
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 10px;
    display: inline-block;
    font-family: 'Inter', monospace;
  }

  .waiting {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
  }

  .waiting p {
    font-size: 0.8rem;
    color: rgba(255,255,255,0.35);
    letter-spacing: 0.02em;
  }

  .pulse-dot {
    width: 6px;
    height: 6px;
    background: #4caf50;
    border-radius: 50%;
    animation: pulse 1.5s ease-in-out infinite;
  }

  @keyframes pulse {
    0%, 100% { opacity: 0.4; transform: scale(1); }
    50% { opacity: 1; transform: scale(1.3); }
  }

  .error {
    color: #e54545;
    margin-top: 20px;
    font-size: 0.85rem;
    font-weight: 500;
    padding: 10px 16px;
    background: rgba(229, 69, 69, 0.08);
    border: 1px solid rgba(229, 69, 69, 0.15);
    border-radius: 8px;
  }
</style>