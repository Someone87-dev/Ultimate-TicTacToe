<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Ultimate Tic Tac Toe</title>
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.5.1/dist/confetti.browser.min.js"></script>
  <style>
    :root {
      --primary: #2ed573;
      --secondary: #ff4757;
      --bg: #1a1a1a;
      --text: #ffffff;
      --accent: #ffa502;
    }

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Poppins', sans-serif;
    }

    body {
      background: var(--bg);
      color: var(--text);
      min-height: 100vh;
      overflow-x: hidden;
    }

    .container {
      max-width: 1200px;
      margin: 0 auto;
      padding: 2rem;
    }

    /* Homepage Styles */
    .homepage {
      text-align: center;
      animation: fadeIn 1s;
    }

    .hero {
      padding: 4rem 0;
      background: linear-gradient(45deg, var(--primary), var(--secondary));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .sticker {
      position: fixed;
      opacity: 0.1;
      pointer-events: none;
      font-size: 2rem;
    }

    @keyframes float {
      0% { transform: translateY(0) rotate(0deg); }
      50% { transform: translateY(-20px) rotate(180deg); }
      100% { transform: translateY(0) rotate(360deg); }
    }

    /* Game Modes */
    .mode-select {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 2rem;
      margin: 3rem 0;
    }

    .mode-card {
      background: rgba(255, 255, 255, 0.05);
      padding: 2rem;
      border-radius: 20px;
      transition: all 0.3s ease;
      cursor: pointer;
      border: 2px solid transparent;
    }

    .mode-card:hover {
      transform: translateY(-10px);
      border-color: var(--primary);
      box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    }

    /* Game Board */
    .board {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
      max-width: 500px;
      margin: 2rem auto;
      background: rgba(255, 255, 255, 0.05);
      padding: 20px;
      border-radius: 20px;
    }

    .cell {
      width: 100px;
      height: 100px;
      background: rgba(255, 255, 255, 0.1);
      border-radius: 15px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 3em;
      cursor: pointer;
      transition: all 0.3s ease;
      position: relative;
      overflow: hidden;
    }

    .cell::before {
      content: '';
      position: absolute;
      inset: 0;
      background: linear-gradient(45deg, var(--primary), var(--secondary));
      opacity: 0;
      transition: opacity 0.3s ease;
    }

    .cell:hover::before {
      opacity: 0.1;
    }

    /* Beautiful X and O */
    .cell.x {
      color: #ff4757;
      text-shadow: 0 0 20px #ff4757cc;
      animation: popIn 0.3s ease;
    }

    .cell.o {
      color: #2ed573;
      text-shadow: 0 0 20px #2ed573cc;
      animation: popIn 0.3s ease;
    }

    @keyframes popIn {
      0% { transform: scale(0); }
      80% { transform: scale(1.2); }
      100% { transform: scale(1); }
    }

    /* Buttons */
    .btn {
      padding: 12px 30px;
      font-size: 1.1em;
      background: linear-gradient(45deg, var(--primary), var(--secondary));
      border: none;
      border-radius: 25px;
      color: white;
      cursor: pointer;
      transition: all 0.3s ease;
      margin: 0 10px;
      box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
    }

    .btn:hover {
      transform: translateY(-3px);
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
    }

    .btn.secondary {
      background: rgba(255, 255, 255, 0.1);
      border: 2px solid var(--primary);
    }

    .btn.secondary:hover {
      background: rgba(255, 255, 255, 0.2);
    }

    /* Theme Switcher */
    .theme-switcher {
      position: fixed;
      top: 20px;
      right: 20px;
      background: rgba(255, 255, 255, 0.1);
      padding: 10px;
      border-radius: 50%;
      cursor: pointer;
      transition: all 0.3s ease;
    }

    .theme-switcher:hover {
      transform: rotate(180deg);
    }

    /* How to Play Modal */
    .modal {
      display: none;
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.8);
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }

    .modal-content {
      background: var(--bg);
      padding: 2rem;
      border-radius: 20px;
      max-width: 500px;
      position: relative;
      animation: slideIn 0.5s ease;
    }

    @keyframes slideIn {
      0% { transform: translateY(-50px); opacity: 0; }
      100% { transform: translateY(0); opacity: 1; }
    }

    .modal-content h2 {
      background: linear-gradient(45deg, var(--primary), var(--secondary));
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }

    .modal-content ol {
      margin: 1rem 0;
      padding-left: 1.5rem;
    }

    .modal-content ol li {
      margin: 0.5rem 0;
    }

    /* Animations */
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(20px); }
      to { opacity: 1; transform: translateY(0); }
    }

    @keyframes confettiFall {
      0% { transform: translateY(-100%) rotate(0deg); }
      100% { transform: translateY(100vh) rotate(360deg); }
    }
  </style>
</head>
<body>
  <!-- Homepage -->
  <div class="homepage container">
    <div class="hero">
      <h1>🎮 Ultimate Tic Tac Toe 🌟</h1>
      <p class="tagline">The Classic Game Reimagined</p>
    </div>

    <div class="sticker" style="top: 20%; left: 10%">❄️</div>
    <div class="sticker" style="top: 60%; right: 15%">🎉</div>

    <div class="mode-select">
      <div class="mode-card" onclick="startGame('pvp')">
        <h2>👥 Player vs Player</h2>
        <p>Challenge a friend in local multiplayer</p>
      </div>
      <div class="mode-card" onclick="startGame('ai')">
        <h2>🤖 Player vs AI</h2>
        <p>Test your skills against our smart AI</p>
      </div>
    </div>

    <button class="btn" onclick="showModal('how-to-play')">📖 How to Play</button>
    <div class="theme-switcher" onclick="toggleTheme()">🎨</div>
  </div>

  <!-- Game Container -->
  <div class="game-container container" style="display: none;">
    <div class="header">
      <h1>⚔️ Game On! ⚔️</h1>
      <div class="status" id="status">Player X's turn</div>
    </div>

    <div class="board" id="board">
      <!-- Cells will be generated here -->
    </div>

    <div class="controls">
      <button class="btn" onclick="resetGame()">🔄 Restart</button>
      <button class="btn secondary" onclick="showHomepage()">🏠 Main Menu</button>
    </div>
  </div>

  <!-- How to Play Modal -->
  <div id="how-to-play" class="modal">
    <div class="modal-content">
      <h2>How to Play 📚</h2>
      <ol>
        <li>The game is played on a 3x3 grid</li>
        <li>Players take turns placing their marks (X or O)</li>
        <li>First player to get 3 in a row wins!</li>
        <li>Lines can be horizontal, vertical, or diagonal</li>
        <li>If all cells are filled with no winner, it's a draw!</li>
      </ol>
      <button class="btn" onclick="closeModal()">Got it! 👍</button>
    </div>
  </div>

  <script>
    // Game Logic and Features
    let currentPlayer = 'X';
    let gameBoard = Array(9).fill('');
    let gameActive = true;
    let gameMode = null;
    let currentTheme = 'dark';

    // Theme Configuration
    const themes = {
      dark: { bg: '#1a1a1a', text: '#ffffff', primary: '#2ed573', secondary: '#ff4757' },
      light: { bg: '#f5f5f5', text: '#333333', primary: '#1e90ff', secondary: '#ff6b81' },
      neon: { bg: '#000000', text: '#00ff9d', primary: '#ff00ff', secondary: '#00ffff' }
    };

    // Theme Switcher Function
    function toggleTheme() {
      const themeNames = Object.keys(themes);
      currentTheme = themeNames[(themeNames.indexOf(currentTheme) + 1) % themeNames.length];
      applyTheme(themes[currentTheme]);
    }

    // Apply Theme Function
    function applyTheme(theme) {
      document.documentElement.style.setProperty('--bg', theme.bg);
      document.documentElement.style.setProperty('--text', theme.text);
      document.documentElement.style.setProperty('--primary', theme.primary);
      document.documentElement.style.setProperty('--secondary', theme.secondary);
    }

    // Show Modal Function
    function showModal(id) {
      const modal = document.getElementById(id);
      modal.style.display = 'flex';
    }

    // Close Modal Function
    function closeModal() {
      const modal = document.getElementById('how-to-play');
      modal.style.display = 'none';
    }

    // Start Game Function
    function startGame(mode) {
      gameMode = mode; // Set the game mode (pvp or ai)
      document.querySelector('.homepage').style.display = 'none'; // Hide homepage
      document.querySelector('.game-container').style.display = 'block'; // Show game container
      resetGame(); // Reset the game state
    }

    // Create the game board
    function createBoard() {
      const board = document.getElementById('board');
      board.innerHTML = '';
      for (let i = 0; i < 9; i++) {
        const cell = document.createElement('div');
        cell.className = 'cell';
        cell.setAttribute('data-index', i);
        cell.addEventListener('click', () => handleMove(i));
        board.appendChild(cell);
      }
    }

    // Handle cell clicks
    function handleMove(index) {
      if (!gameActive || gameBoard[index] !== '') return;
      if (gameMode === 'ai' && currentPlayer === 'O') return; // Prevent AI from clicking

      makeMove(index);
      checkGameResult();

      if (gameMode === 'ai' && gameActive) {
        setTimeout(aiMove, 500); // AI makes a move after a delay
      }
    }

    // Make a move
    function makeMove(index) {
      gameBoard[index] = currentPlayer;
      const cell = document.querySelector(`[data-index="${index}"]`);
      cell.textContent = currentPlayer;
      cell.classList.add(currentPlayer.toLowerCase());
    }

    // Check for win or draw
    function checkGameResult() {
      const winner = checkWin();
      if (winner) {
        gameActive = false;
        document.getElementById('status').textContent = `Player ${winner} wins!`;
        confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
        autoRestart();
        return;
      }

      if (!gameBoard.includes('')) {
        gameActive = false;
        document.getElementById('status').textContent = "Game Draw!";
        autoRestart();
        return;
      }

      currentPlayer = currentPlayer === 'X' ? 'O' : 'X';
      document.getElementById('status').textContent = `Player ${currentPlayer}'s turn`;
    }

    // Check for winning combinations
    function checkWin() {
      const winCombos = [
        [0, 1, 2], [3, 4, 5], [6, 7, 8], // Rows
        [0, 3, 6], [1, 4, 7], [2, 5, 8], // Columns
        [0, 4, 8], [2, 4, 6]             // Diagonals
      ];

      for (const combo of winCombos) {
        const [a, b, c] = combo;
        if (gameBoard[a] && gameBoard[a] === gameBoard[b] && gameBoard[a] === gameBoard[c]) {
          return gameBoard[a]; // Return the winning player (X or O)
        }
      }
      return null; // No winner
    }

    // Reset the game
    function resetGame() {
      gameBoard = Array(9).fill('');
      gameActive = true;
      currentPlayer = 'X';
      document.querySelectorAll('.cell').forEach(cell => {
        cell.textContent = '';
        cell.classList.remove('x', 'o');
      });
      document.getElementById('status').textContent = `Player ${currentPlayer}'s turn`;
    }

    // Smarter AI Move Logic using Minimax with Alpha-Beta Pruning
    function aiMove() {
      let bestScore = -Infinity;
      let bestMove;
      for (let i = 0; i < 9; i++) {
        if (gameBoard[i] === '') {
          gameBoard[i] = 'O';
          let score = minimax(gameBoard, 0, false, -Infinity, Infinity);
          gameBoard[i] = '';
          if (score > bestScore) {
            bestScore = score;
            bestMove = i;
          }
        }
      }
      makeMove(bestMove);
      checkGameResult();
    }

    // Minimax Check Win Function for a given board state
    function minimaxCheckWin(board) {
      const winCombos = [
        [0, 1, 2], [3, 4, 5], [6, 7, 8],
        [0, 3, 6], [1, 4, 7], [2, 5, 8],
        [0, 4, 8], [2, 4, 6]
      ];
      for (const combo of winCombos) {
        const [a, b, c] = combo;
        if (board[a] && board[a] === board[b] && board[a] === board[c]) {
          return board[a];
        }
      }
      return null;
    }

    // Minimax Algorithm with Alpha-Beta Pruning
    function minimax(board, depth, isMax, alpha, beta) {
      const winner = minimaxCheckWin(board);
      if (winner === 'O') return 10 - depth;
      if (winner === 'X') return depth - 10;
      if (!board.includes('')) return 0; // Draw

      if (isMax) {
        let maxEval = -Infinity;
        for (let i = 0; i < 9; i++) {
          if (board[i] === '') {
            board[i] = 'O';
            let eval = minimax(board, depth + 1, false, alpha, beta);
            board[i] = '';
            maxEval = Math.max(eval, maxEval);
            alpha = Math.max(alpha, eval);
            if (beta <= alpha) break;
          }
        }
        return maxEval;
      } else {
        let minEval = Infinity;
        for (let i = 0; i < 9; i++) {
          if (board[i] === '') {
            board[i] = 'X';
            let eval = minimax(board, depth + 1, true, alpha, beta);
            board[i] = '';
            minEval = Math.min(eval, minEval);
            beta = Math.min(beta, eval);
            if (beta <= alpha) break;
          }
        }
        return minEval;
      }
    }

    // Auto-restart after win or draw
    function autoRestart() {
      setTimeout(() => {
        resetGame();
        if (gameMode === 'ai' && currentPlayer === 'O') {
          setTimeout(aiMove, 500); // AI makes the first move in the next game
        }
      }, 3000);
    }

    // Show homepage
    function showHomepage() {
      document.querySelector('.homepage').style.display = 'block';
      document.querySelector('.game-container').style.display = 'none';
      gameActive = false;
    }

    // Initialize the game on page load
    createBoard();
  </script>
</body>
</html>
