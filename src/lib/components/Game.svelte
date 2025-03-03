<script lang="ts">
    import Cell from "./Cell.svelte";
    import { CellState } from "$lib/types";
    import { tick } from "svelte";

    let board: CellState[] = Array(9).fill(CellState.Empty);
    let currentTurn: CellState = CellState.Cross;
    let winner: CellState | null = null;

    let crossMoves: number[] = [];
    let circleMoves: number[] = [];

    function handleClick(index: number) {
        if (winner != null) return;
        if (board[index] !== CellState.Empty) return; // Prevent overriding
        board[index] = currentTurn;

        // Track moves
        if (currentTurn === CellState.Cross) {
            crossMoves.push(index);
            if (crossMoves.length > 3) {
                let oldest = crossMoves.shift(); // Remove oldest
                if (oldest !== undefined) board[oldest] = CellState.Empty;
            }
        } else {
            circleMoves.push(index);
            if (circleMoves.length > 3) {
                let oldest = circleMoves.shift();
                if (oldest !== undefined) board[oldest] = CellState.Empty;
            }
        }

        winner = checkWinner();
        if (winner) {
            console.log(
                `Winner: ${winner === CellState.Cross ? "✖ Cross" : "⭘ Circle"}`,
            );
            return;
        }

        // Toggle turn
        currentTurn =
            currentTurn === CellState.Cross
                ? CellState.Circle
                : CellState.Cross;
    }

    function checkWinner(): CellState | null {
        const winningCombos = [
            [0, 1, 2],
            [3, 4, 5],
            [6, 7, 8], // Rows
            [0, 3, 6],
            [1, 4, 7],
            [2, 5, 8], // Columns
            [0, 4, 8],
            [2, 4, 6], // Diagonals
        ];

        for (const [a, b, c] of winningCombos) {
            if (
                board[a] !== CellState.Empty &&
                board[a] === board[b] &&
                board[a] === board[c]
            ) {
                return board[a]; // Return the winner (Cross or Circle)
            }
        }

        return null; // No winner yet
    }

    function reset() {
        board = Array(9).fill(CellState.Empty);
        winner = null;
        crossMoves = [];
        circleMoves = [];
        currentTurn = CellState.Cross;
    }
</script>

<h1 class="game-title">Tic Tac Three!</h1>


<div class="board-container">
    <div class="board">
        {#each board as cell, index}
            <Cell state={cell} on:click={() => handleClick(index)} />
        {/each}
    </div>
</div>

{#if winner !== null}
    <div class="winner-text">
        {winner === CellState.Cross ? "✖ Cross Wins!" : "⭘ Circle Wins!"}
    </div>
{/if}

<button class="reset-btn" on:click={() => reset()}>Reset</button>

<style>
.game-title {
    font-family: 'Orbitron', sans-serif; /* Cyberpunk Font */
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    color: #b300ff; /* Neon purple */
    text-shadow: 0 0 2px #b300ff, 0 0 2px #8000ff, 0 0 2px #4b0082;
    font-size: 2rem;
    font-weight: bold;
    text-align: center;
}


    /* Cyberpunk Background */
body {
  background: radial-gradient(circle at center, #0d0d0d, #000000);
  color: #0ff;
  font-family: 'Orbitron', sans-serif;
  text-transform: uppercase;
  letter-spacing: 2px;
  overflow: hidden;
}

/* Centering the board */
.board-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

/* Current Turn Indicator */
h2 {
  margin-bottom: 15px;
  font-size: 1.5rem;
  text-align: center;
  color: #ff00ff;
  text-shadow: 0 0 5px #ff00ff, 0 0 10px #ff00ff, 0 0 20px #ff00ff;
}

/* Tic-Tac-Toe Board */
.board {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
  width: min(90vw, 320px);
  padding: 10px;
  background: rgba(0, 255, 255, 0.1);
  border-radius: 10px;
  box-shadow: 0 0 10px #0ff, 0 0 40px #0ff, 0 0 80px #0ff;
}

/* Tic-Tac-Toe Cells */
.board div {
  width: 100px;
  height: 100px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 3rem;
  font-weight: bold;
  color: #0ff;
  background: rgba(0, 0, 0, 0.8);
  border: 2px solid #0ff;
  text-shadow: 0 0 5px #0ff, 0 0 10px #0ff;
  cursor: pointer;
  transition: 0.3s;
}

.board div:hover {
  background: rgba(0, 255, 255, 0.2);
  box-shadow: 0 0 10px #0ff;
}

/* Neon Animation for Clicked Cells */
@keyframes flicker {
  0% { text-shadow: 0 0 5px #0ff, 0 0 10px #0ff; }
  50% { text-shadow: 0 0 10px #0ff, 0 0 20px #0ff, 0 0 40px #0ff; }
  100% { text-shadow: 0 0 5px #0ff, 0 0 10px #0ff; }
}

.board div:active {
  animation: flicker 0.2s alternate infinite;
}

/* Winner Message */
.winner-text {
    font-family: 'Orbitron', sans-serif; /* Cyberpunk Font */

  position: absolute;
  bottom: 50%;
  left: 10%;
  transform: translateX(-50%);
  font-size: 2rem;
  font-weight: bold;
  background: rgba(255, 0, 255, 0.5);
  color: #ffff66; /* Neon yellow */
    text-shadow: 0 0 5px #ffff66, 0 0 10px #ffea00, 0 0 20px #ffd700;
  padding: 10px 20px;
  border-radius: 5px;
  animation: glitch 0.5s infinite alternate;
}

/* Cyberpunk Glitch Effect */
@keyframes glitch {
  0% { transform: translateX(0); }
  20% { transform: translateX(-2px); }
  40% { transform: translateX(2px); }
  60% { transform: translateX(-1px); }
  80% { transform: translateX(1px); }
  100% { transform: translateX(0); }
}

/* Reset Button */
.reset-btn {
    font-family: 'Orbitron', sans-serif; /* Cyberpunk Font */

  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  padding: 15px;
  font-size: 1.2rem;
  font-weight: bold;
  background: #ff00ff;
  color: black;
  border: none;
  cursor: pointer;
  text-shadow: 0 0 1px black, 0 0 1px black;
  box-shadow: 0 0 5px #ff00ff, 0 0 10px #ff00ff, 0 0 20px #ff00ff;
  transition: 0.3s;
}

.reset-btn:hover {
  background: #ff1493;
  box-shadow: 0 0 20px #ff00ff, 0 0 40px #ff00ff;
}

.reset-btn:active {
  background: #ff007f;
}

.board div {
    user-select: none;
    outline: none;
}

.board div:focus {
    outline: none;
}

.board div:active {
    background: inherit;
    box-shadow: none;
}
</style>
