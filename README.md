# VS Wordle ⚔️

![VS Wordle Header](https://github.com/Shreyasjain2/VsWordle/blob/main/VSWordle.png)

A **premium, multiplayer competitive twist on the classic Wordle game**. Challenge your friends in a battle of wits where you set the secret word for your opponent and compete to solve it first in real-time.

<div align="center">
  <a href="https://vswordle.onrender.com/">
    <img src="https://img.shields.io/badge/PLAY-NOW-success?style=for-the-badge&logo=google-chrome&logoColor=white" alt="Play Now" height="40"/>
  </a>
</div>

<div align="center">
  <img src="https://img.shields.io/badge/Mode-Real--time%20PvP-orange" alt="Game Mode" />
  <a href="https://github.com/Shreyasjain2/VsWordle"><img src="https://img.shields.io/badge/Status-Live-blue" alt="Project Status"/></a>
  <img src="https://img.shields.io/badge/License-GPL--3.0-lightgrey" alt="License" />
</div>

---

## 🎮 Play Here
You can play the live version of the game here:  
👉 **[vswordle.onrender.com](https://vswordle.onrender.com/)**

---

## ✨ Features & Redesign
This version features a complete UI/UX overhaul focusing on a minimal, professional aesthetic:

- **Premium Dark UI**: A sleek, near-black aesthetic with polished glassmorphism effects.
- **Interactive Background**: High-performance canvas-based particle system that reacts to your mouse movement.
- **Real-Time Multiplayer**: Powered by Socket.io for instant turn syncing and game state management.
- **Dynamic Wordle Board**: Unlike the original game, the board now dynamically scales! If you need more than 6 guesses, the UI generates new rows and lets you scroll back through the history.
- **Breathe-Animation Timer**: Interactive pill-shaped turn indicators with breathing animations to highlight the active player.
- **Strategic Word Setting**: You choose the word your opponent has to guess. Mind games are part of the play.

---

## 🚀 Local Setup
If you want to run the project locally:

1. **Clone & Install**
   ```bash
   git clone https://github.com/Shreyasjain2/VsWordle.git
   cd VsWordle
   npm install
   ```
2. **Start Development Server**
   ```bash
   npm run dev
   ```
3. **Start Backend**
   ```bash
   node server/server.js
   ```

---

## 🛠 Tech Stack
- **Frontend**: Svelte + Vite
- **Backend**: Node.js + Socket.io
- **Styling**: Vanilla CSS (Modern Design Tokens)

---

## 📜 License
This project is licensed under the GPL-3.0 License.

---

<div align="center">
  Developed by <a href="https://github.com/Shreyasjain2">Shreyas Jain</a>
</div>
