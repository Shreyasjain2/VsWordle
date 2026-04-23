# VS Wordle ⚔️

![VS Wordle Header](https://github.com/Shreyasjain2/VsWordle/blob/main/VSWordle.png)

A **premium, multiplayer competitive twist on the classic Wordle game**. Challenge your friends in a battle of wits where you set the secret word for your opponent and compete to solve it first in real-time.

<div align="center">
  <a href="https://vswordle.onrender.com/"><img src="https://img.shields.io/badge/Live-Demo-success" alt="Live Demo"/></a>
  <a href="https://github.com/Shreyasjain2/VsWordle"><img src="https://img.shields.io/badge/Status-Active-blue" alt="Project Status"/></a>
  <img src="https://img.shields.io/badge/Mode-Real--time%20PvP-orange" alt="Game Mode" />
  <img src="https://img.shields.io/badge/License-GPL--3.0-lightgrey" alt="License" />
</div>

---

## ✨ New Features & Redesign
This version features a complete UI/UX overhaul focusing on a minimal, professional aesthetic:

- **Premium Dark UI**: A sleek, near-black aesthetic with polished glassmorphism effects.
- **Interactive Background**: High-performance canvas-based particle system that reacts to your mouse movement.
- **Real-Time Multiplayer**: Powered by Socket.io for instant turn syncing and game state management.
- **Dynamic Wordle Board**: Unlike the original game, the board now dynamically scales! If you need more than 6 guesses, the UI generates new rows and lets you scroll back through the history.
- **Breathe-Animation Timer**: Interactive pill-shaped turn indicators with breathing animations to highlight the active player.
- **Strategic Word Setting**: You choose the word your opponent has to guess. Mind games are part of the play.

---

## 🚀 Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) (v16 or higher)
- npm or yarn

### Local Installation
1. **Clone the repository**
   ```bash
   git clone https://github.com/Shreyasjain2/VsWordle.git
   cd VsWordle
   ```

2. **Setup Frontend**
   ```bash
   npm install
   npm run dev
   ```

3. **Setup Backend Server**
   ```bash
   cd server
   npm install
   node server.js
   ```

4. **Play**
   Open [http://localhost:5173](http://localhost:5173) in two separate browser window/tabs to test the multiplayer logic!

---

## 🛠 Tech Stack
- **Frontend**: [Svelte](https://svelte.dev/) + [Vite](https://vitejs.dev/)
- **Backend**: [Node.js](https://nodejs.org/) + [Express](https://expressjs.com/)
- **Real-time**: [Socket.io](https://socket.io/)
- **Styling**: Vanilla CSS (Modern Design Tokens)

---

## 🌐 Deployment (Render)
This project is configured for easy deployment on **Render** using the included `render.yaml`.

1. Connect your GitHub repository to Render.
2. Create a **New Web Service**.
3. Use the following **Build Command**:
   ```bash
   npm install && npm run build && cd server && npm install
   ```
4. Use the following **Start Command**:
   ```bash
   node server/server.js
   ```
5. Set `NODE_ENV` to `production` in the Environment variables.

---

## 📜 License
This project is licensed under the GPL-3.0 License. Feel free to fork and build upon it!

---

<div align="center">
  Developed by <a href="https://github.com/Shreyasjain2">Shreyas Jain</a>
</div>
