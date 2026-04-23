<script lang="ts">
  import { socketService } from '../socketService';
  import { roomId, gameState , mySecretWord } from '../store';
    import { words } from '../utils'; // <--- IMPORT YOUR WORDS UTILITY

  
  let word = '';
  let wordSubmitted = false;
  let validationError = '';


  function handleSubmit() {
    validationError = '';

    // Basic validation
    if (word.length !== 5 || !/^[a-zA-Z]+$/.test(word)) {
      alert('Please enter a valid 5-letter word.');
      return;
    }
    if (!words.contains(word.toLowerCase())) {
      validationError = 'Word not found in dictionary.';
      return;
    }
    mySecretWord.set(word);
    socketService.setWord($roomId, word);
    wordSubmitted = true;
  }

  // Listen for when the opponent's word is ready so we can start the game
  socketService.on('opponentWordReady', (data) => {
    // The main App component will handle setting the wordToGuess store
    gameState.set('playing');
  });
</script>

<div class="word-input-container">
  <div class="word-input-card">
    {#if !wordSubmitted}
      <div class="icon-circle">⚔</div>
      <h2>Set the Word</h2>
      <p class="description">Choose a 5-letter word for your opponent to guess.</p>
      <form on:submit|preventDefault={handleSubmit}>
        <input 
          type="text" 
          bind:value={word} 
          maxlength="5" 
          placeholder="_ _ _ _ _"
          autocomplete="off"
          spellcheck="false"
        />
        <button type="submit">Confirm Word</button>
      </form>
      {#if validationError}
        <p class="error">{validationError}</p>
      {/if}
    {:else}
      <div class="icon-circle success">✓</div>
      <h2>Word Set!</h2>
      <div class="waiting">
        <div class="pulse-dot"></div>
        <p>Waiting for your opponent to set their word...</p>
      </div>
    {/if}
  </div>
</div>

<style>
  .word-input-container {
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    padding: 20px;
  }

  .word-input-card {
    text-align: center;
    width: 100%;
    max-width: 400px;
    padding: 48px 40px;
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255, 255, 255, 0.06);
    border-radius: 20px;
    backdrop-filter: blur(16px);
    -webkit-backdrop-filter: blur(16px);
  }

  .icon-circle {
    width: 56px;
    height: 56px;
    border-radius: 50%;
    background: rgba(255,255,255,0.05);
    border: 1px solid rgba(255,255,255,0.1);
    display: inline-flex;
    justify-content: center;
    align-items: center;
    font-size: 1.5rem;
    margin-bottom: 20px;
  }

  .icon-circle.success {
    background: rgba(76, 175, 80, 0.1);
    border-color: rgba(76, 175, 80, 0.25);
    color: #4caf50;
    font-weight: 700;
    font-size: 1.3rem;
  }

  h2 {
    font-size: 1.4rem;
    font-weight: 700;
    color: #ffffff;
    letter-spacing: 0.04em;
    margin-bottom: 8px;
  }

  .description {
    font-size: 0.85rem;
    color: rgba(255,255,255,0.35);
    margin-bottom: 28px;
    letter-spacing: 0.02em;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 12px;
  }

  input {
    padding: 14px 16px;
    font-size: 1.6rem;
    font-weight: 700;
    text-align: center;
    text-transform: uppercase;
    letter-spacing: 0.5em;
    background: rgba(255,255,255,0.04);
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 10px;
    color: #ffffff;
    outline: none;
    font-family: 'Inter', sans-serif;
    transition: border-color 0.2s ease;
    width: 100%;
    box-sizing: border-box;
  }

  input::placeholder {
    color: rgba(255,255,255,0.15);
    letter-spacing: 0.8em;
    font-weight: 400;
  }

  input:focus {
    border-color: rgba(255,255,255,0.25);
  }

  button {
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

  button:hover {
    background: rgba(255,255,255,0.88);
    transform: translateY(-1px);
  }

  .waiting {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 20px;
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
    margin-top: 12px;
    font-size: 0.85rem;
    font-weight: 500;
    padding: 10px 16px;
    background: rgba(229, 69, 69, 0.08);
    border: 1px solid rgba(229, 69, 69, 0.15);
    border-radius: 8px;
  }
</style>
