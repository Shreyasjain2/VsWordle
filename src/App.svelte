<script lang="ts">
  import { onMount } from 'svelte';

  import { gameState, gameOverDetails , roomId, wordToGuess, opponentGuesses, winnerId,mySecretWord } from './store';
  import { socketService } from './socketService';
  import { activePlayerId } from './store'; // Add this import
  
  // Import from YOUR existing utils.ts file
  import { words, LetterStates, evaluateGuess } from './utils';
  import type { LetterState } from './types'; // Assuming you have a types.ts, or define it here
  
  // Import Components
  import GameOverModal from './components/GameOverModal.svelte';

  import Lobby from './components/Lobby.svelte';
  import WordInput from './components/WordInput.svelte';
  import Board from './components/board/Board.svelte';
  import Keyboard from './components/keyboard/Keyboard.svelte';
  import Timer from './components/Timer.svelte'; // Add this import


  // --- Local Game State ---
  let currentGuess: string = '';
  let boardWords: string[] = Array(6).fill("");
  let boardStates: LetterState[][] = Array.from({ length: 6 }, () => Array(5).fill("🔳"));
  let currentRow = 0;
  let letterStates = new LetterStates();
  let canvasEl: HTMLCanvasElement;

  // --- Opponent State ---
  // We'll structure the opponent's board similarly for consistency
  let opponentBoardStates: LetterState[][] = [];
  $: opponentBoardWords = $opponentGuesses.map(g => g.word);
  $: opponentBoardStates = $opponentGuesses.map(g => g.states);
  let finalChanceMessage = '';


  // --- Socket Event Listeners ---
  socketService.on('opponentWordReady', (data) => wordToGuess.set(data.wordToGuess.toLowerCase()));
  
  socketService.on('opponentGuessed', (data) => {
    // data.guesses from the server will be an array of {word: string, states: LetterState[]}
    opponentGuesses.set(data.guesses);
  });
  
  socketService.on('turnChange', (data) => {
    activePlayerId.set(data.currentPlayerId);
  });

  socketService.on('gameOver', (data) => {
    winnerId.set(data.winnerId);
    gameOverDetails.set(data); // Store all the details
    gameState.set('gameOver');
  });

  socketService.on('finalChance', (data) => {
    if (data.challengerId === socketService.playerId) {
      finalChanceMessage = "Opponent finished! This is your final chance to tie!";
    } else {
      finalChanceMessage = "You finished! Waiting for opponent's final guess...";
    }
    // Clear the message after a short delay
    setTimeout(() => finalChanceMessage = '', 8000);
  });
  
  socketService.on('opponentLeft', () => {
    alert('Your opponent has disconnected.');
    window.location.reload();
  });

socketService.on('guessProcessed', (data) => {
    const { guesserId, guesses } = data; // `guesses` is an array of {word, states}

    if (guesserId === socketService.playerId) {
      // It's me! Update MY board.
      // Ensure we have at least 6 rows, plus enough for all guesses AND the current typing row
      const targetLen = Math.max(6, guesses.length + 1);
      const newWords = Array(targetLen).fill("");
      const newStates = Array.from({ length: targetLen }, () => Array(5).fill("🔳"));
      
      guesses.forEach((g, i) => {
          newWords[i] = g.word;
          newStates[i] = g.states;
      });
      
      boardWords = newWords;
      boardStates = newStates;
      currentRow = guesses.length;
      currentGuess = ""; // Clear the input row

      // Update keyboard state from the most recent guess
      const lastGuess = guesses[guesses.length - 1];
      if (lastGuess) {
        letterStates.update(lastGuess.states, lastGuess.word);
        letterStates = letterStates; // Trigger Svelte reactivity
      }

    } else {
      // It's the opponent. Update THEIR board data.
      opponentGuesses.set(guesses);
    }
  });

  // --- Game Logic ---
  function handleKeyPress(event: CustomEvent<string>) {
    const key = event.detail;

    if (key === 'enter') {
      submitGuess();
    } else if (key === 'backspace') {
      currentGuess = currentGuess.slice(0, -1);
      boardWords[currentRow] = currentGuess;
    } else if (currentGuess.length < 5 && /^[a-zA-Z]$/.test(key)) {
      currentGuess += key.toLowerCase();
      // Dynamically add a new row if we exceeded the board
      if (currentRow >= boardWords.length) {
        boardWords = [...boardWords, ""];
        boardStates = [...boardStates, Array(5).fill("🔳")];
      }
      boardWords[currentRow] = currentGuess;
    }
  }

  function submitGuess() {
    if (currentGuess.length !== 5) {
      alert('Not enough letters');
      return;
    }
    if (!words.contains(currentGuess)) {
      alert('Not in word list');
      return;
    }

    // The ONLY job of this function now is to send the word to the server.
    // No more local state updates here!
    socketService.makeGuess($roomId, currentGuess);
  }

  // Allow keyboard input from the physical keyboard
  onMount(() => {
    const handleKeyDown = (event: KeyboardEvent) => {
      if ($gameState !== 'playing') return;
      
      const key = event.key.toLowerCase();
      if (key === 'enter') handleKeyPress({ detail: 'enter' } as CustomEvent<string>);
      else if (key === 'backspace') handleKeyPress({ detail: 'backspace' } as CustomEvent<string>);
      else if (key.length === 1 && key >= 'a' && key <= 'z') handleKeyPress({ detail: key } as CustomEvent<string>);
    };
    
    window.addEventListener('keydown', handleKeyDown);

    // --- Particle background ---
    if (canvasEl) {
      const ctx = canvasEl.getContext('2d');
      let w: number, h: number;
      const particles: {x:number,y:number,vx:number,vy:number,r:number,o:number}[] = [];
      const mouse = { x: -9999, y: -9999 };
      let animId: number;

      function resize() {
        w = canvasEl.width = window.innerWidth;
        h = canvasEl.height = window.innerHeight;
      }
      resize();
      window.addEventListener('resize', resize);

      const onMouseMove = (e: MouseEvent) => { mouse.x = e.clientX; mouse.y = e.clientY; };
      document.addEventListener('mousemove', onMouseMove);

      for (let i = 0; i < 60; i++) {
        particles.push({
          x: Math.random() * w, y: Math.random() * h,
          vx: (Math.random() - 0.5) * 0.3, vy: (Math.random() - 0.5) * 0.3,
          r: Math.random() * 2 + 0.5, o: Math.random() * 0.3 + 0.05
        });
      }

      function draw() {
        ctx.clearRect(0, 0, w, h);
        particles.forEach(p => {
          const dx = p.x - mouse.x;
          const dy = p.y - mouse.y;
          const dist = Math.sqrt(dx * dx + dy * dy);
          if (dist < 120) {
            const force = (120 - dist) / 120 * 0.8;
            p.x += (dx / dist) * force;
            p.y += (dy / dist) * force;
          }
          p.x += p.vx; p.y += p.vy;
          if (p.x < 0) p.x = w; if (p.x > w) p.x = 0;
          if (p.y < 0) p.y = h; if (p.y > h) p.y = 0;
          ctx.beginPath();
          ctx.arc(p.x, p.y, p.r, 0, Math.PI * 2);
          ctx.fillStyle = 'rgba(255,255,255,' + p.o + ')';
          ctx.fill();
        });
        for (let i = 0; i < particles.length; i++) {
          for (let j = i + 1; j < particles.length; j++) {
            const dx2 = particles[i].x - particles[j].x;
            const dy2 = particles[i].y - particles[j].y;
            const d = Math.sqrt(dx2 * dx2 + dy2 * dy2);
            if (d < 150) {
              ctx.beginPath();
              ctx.moveTo(particles[i].x, particles[i].y);
              ctx.lineTo(particles[j].x, particles[j].y);
              ctx.strokeStyle = 'rgba(255,255,255,' + (0.04 * (1 - d / 150)) + ')';
              ctx.lineWidth = 0.5;
              ctx.stroke();
            }
          }
        }
        animId = requestAnimationFrame(draw);
      }
      draw();

      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('resize', resize);
        document.removeEventListener('mousemove', onMouseMove);
        cancelAnimationFrame(animId);
      };
    }

    return () => window.removeEventListener('keydown', handleKeyDown);
  });

  const formatTime = (ms) => (ms / 1000).toFixed(2);

</script>
<canvas id="particle-canvas" bind:this={canvasEl}></canvas>
<main>
 <GameOverModal 
    isOpen={$gameState === 'gameOver'}
    winnerId={$winnerId}
    gameOverDetails={$gameOverDetails}
  />
  {#if $gameState === 'lobby'}
    <Lobby />
  {:else if $gameState === 'settingWord'}
    <WordInput />
  {:else if $gameState === 'playing' || $gameState=='gameOver'}
    <div class="game-view-container">
      <div class="main-game-area">
        <header>
          <h1>VS WORDLE</h1>
          <div class="header-accent"></div>
        </header>
        
        {#if $gameState === 'playing'}
          <div class="game-status">
            <Timer />
            {#if finalChanceMessage}
              <div class="status-message">{finalChanceMessage}</div>
            {/if}
          </div>
        {/if}

        
        
        <div class="board-wrapper">
            <Board words={boardWords} states={boardStates} />
        </div>
        
        <div class="keyboard-wrapper">
          <Keyboard 
            on:key={handleKeyPress} 
            {letterStates}
            disabled={$activePlayerId !== socketService.playerId || $gameState === 'gameOver'}
          />
        </div>
      </div>

     <div class="opponent-sidebar">
  <div class="sidebar-label">OPPONENT</div>
  <h3>Their Board</h3>
  <div class="secret-word">
    {#each $mySecretWord.toUpperCase().padEnd(5).split('') as char}
      <div class="secret-word-tile">{char}</div>
    {/each}
  </div>
  <div class="opponent-board-wrapper">
    <Board words={opponentBoardWords} states={opponentBoardStates} />
  </div>
</div>

    </div>
  {/if}
</main>



<style>
  :global(#particle-canvas) {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 0;
    pointer-events: none;
  }

  :global(body) {
    font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
    background-color: #0c0d10;
    color: #e8eaed;
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: flex-start;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
  }

  main {
    width: 100%;
    height: 100vh;
    position: relative;
    z-index: 1;
  }

  .game-view-container {
    display: flex;
    justify-content: center;
    gap: 48px;
    padding: 24px;
    max-width: 1200px;
    margin: 0 auto;
  }

  .main-game-area {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 100%;
    max-width: 500px;
  }

  header {
    width: 100%;
    text-align: center;
    margin-bottom: 16px;
    position: relative;
  }

  .header-accent {
    width: 40px;
    height: 2px;
    background: linear-gradient(90deg, transparent, rgba(255,255,255,0.4), transparent);
    margin: 0 auto;
  }

  header h1 {
    margin: 0.3em 0 0.4em;
    font-size: 1.8rem;
    letter-spacing: 0.35rem;
    font-weight: 800;
    color: #ffffff;
    text-transform: uppercase;
  }
  
  .game-status {
    height: 52px;
    margin-bottom: 12px;
  }

  .status-message {
    text-align: center;
    color: #c8a830;
    font-weight: 600;
    font-size: 0.85rem;
    letter-spacing: 0.02em;
    padding: 6px 16px;
    background: rgba(200, 168, 48, 0.08);
    border: 1px solid rgba(200, 168, 48, 0.15);
    border-radius: 6px;
    margin-top: 4px;
  }

  .board-wrapper {
    margin-bottom: 24px;
    max-height: 450px;
    width: 100%;
    overflow-y: auto;
    overflow-x: hidden;
    padding: 4px; /* Give room for focus/borders */
  }
  
  /* Custom Scrollbar for wrappers */
  .board-wrapper::-webkit-scrollbar, .opponent-board-wrapper::-webkit-scrollbar {
    width: 6px;
  }
  .board-wrapper::-webkit-scrollbar-track, .opponent-board-wrapper::-webkit-scrollbar-track {
    background: rgba(255, 255, 255, 0.05);
    border-radius: 4px;
  }
  .board-wrapper::-webkit-scrollbar-thumb, .opponent-board-wrapper::-webkit-scrollbar-thumb {
    background: rgba(255, 255, 255, 0.15);
    border-radius: 4px;
  }
  .board-wrapper::-webkit-scrollbar-thumb:hover, .opponent-board-wrapper::-webkit-scrollbar-thumb:hover {
    background: rgba(255, 255, 255, 0.25);
  }

  .keyboard-wrapper {
    width: 100%;
  }

  /* Opponent sidebar with glass effect */
  .opponent-sidebar {
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px 24px;
    margin-left: 0;
    width: 300px;
    flex-shrink: 0;
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255, 255, 255, 0.06);
    border-radius: 16px;
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
  }

  .sidebar-label {
    font-size: 0.65rem;
    letter-spacing: 0.25em;
    color: rgba(255,255,255,0.25);
    text-transform: uppercase;
    font-weight: 600;
    margin-bottom: 4px;
  }

  .opponent-sidebar h3 {
    text-align: center;
    margin-top: 0;
    margin-bottom: 12px;
    font-weight: 500;
    letter-spacing: 0.08em;
    font-size: 0.9rem;
    color: rgba(255,255,255,0.45);
    text-transform: uppercase;
  }
  
  .secret-word {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
    gap: 4px;
    margin-bottom: 12px;
    width: 240px;
  }

  .secret-word-tile {
    display: inline-flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    height: 40px;
    font-size: 1.3rem;
    font-weight: 700;
    color: #fff;
    text-transform: uppercase;
    background-color: #4caf50;
    border: none;
    box-sizing: border-box;
    border-radius: 4px;
  }

  .opponent-board-wrapper {
    transform: scale(0.78);
    transform-origin: top center;
    max-height: 600px;
    overflow-y: auto;
    overflow-x: hidden;
    padding: 4px;
  }

  .opponent-board-wrapper :global(.board) {
    width: 240px;
    height: 290px;
    gap: 4px;
  }

  .opponent-board-wrapper :global(.tile) {
    font-size: 1.3rem;
  }

  /* Responsive Design */
  @media (max-width: 900px) {
    .game-view-container {
      flex-direction: column;
      align-items: center;
      gap: 24px;
    }
    .opponent-sidebar {
      width: 100%;
      max-width: 500px;
      padding: 20px 16px;
    }
    .opponent-board-wrapper {
      transform: scale(1);
    }
  }
</style>