<script>
  let board = [" ", " ", " ", " ", " ", " ", " ", " ", " "];
  let wins = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ]

  let allowedMoves = {
    0: [1, 3, 4],
    1: [0, 2, 4],
    2: [1, 4, 5],
    3: [0, 4, 6],
    4: [0, 1, 2, 3, 5, 6, 7, 8],
    5: [2, 4, 8],
    6: [3, 4, 7],
    7: [6, 4, 8],
    8: [7, 4, 5],
  }

  let selectedPos;
  let currentPlayer = "X";
  let can = true;
  let timesX = 0;
  let timesO = 0;

  function handleClick(e) {
    if (!can) return;

    if (timesX < 3 || timesO < 3) {
      if (e.target.textContent != "") return;

      const pos = e.target.getAttribute("pos");
      e.target.textContent = currentPlayer;
      e.target.classList.toggle("clicked");
      e.target.classList.toggle(currentPlayer);
      board[pos] = currentPlayer;
      checkWinner(board, currentPlayer);
      changePlayer(currentPlayer);
    } else {
      if (!selectedPos && e.target.textContent != currentPlayer) return alert("Selecciona tu ficha, en este caso: " + currentPlayer);
      if (!selectedPos && e.target.textContent === "") return alert("Selecciona una casilla con tu ficha, en este caso: " + currentPlayer);
      if (!selectedPos || selectedPos === e.target.getAttribute("pos")) {
        selectedPos = e.target.getAttribute("pos");
      }
      let newPos;

      if (selectedPos === e.target.getAttribute("pos")) {
        if (e.target.classList.contains("focused")) {
          e.target.classList.remove("focused");
          selectedPos = null;
        } else {
          e.target.classList.add("focused");
        }
        return;
      }

      if (e.target.getAttribute("pos") != selectedPos) {
        const boardItems = document.querySelectorAll(".board-item");
        if (e.target.textContent != "") return alert("Ya hay una ficha en ese lugar.")
        newPos = e.target.getAttribute("pos");

        if (allowedMoves[selectedPos].includes(parseInt(newPos))) {
          board[selectedPos] = " ";
          board[newPos] = currentPlayer;

          const old = Array.from(boardItems)
          .filter((x) => x.getAttribute("pos") === selectedPos)[0]
          old.textContent = "";
          old.classList.remove(currentPlayer);
          old.classList.remove("focused");
          selectedPos = null;
          const newp = Array.from(boardItems)
          .filter((x) => x.getAttribute("pos") === newPos)[0]
          newp.textContent = currentPlayer;
          newp.classList.add(currentPlayer);

          checkWinner(board, currentPlayer);
          changePlayer(currentPlayer);
        } else {
          return alert("Movimiento no permitido, tus movimientos son: " + allowedMoves[selectedPos].join(", "))
        }
      }
    }
  }

  const winner = (player, ...abc) => {
    const boardItems = Array.from(document.querySelectorAll(".board-item"));
    abc.forEach((n) => {
      let f = boardItems.filter((item) => item.getAttribute("pos") == n)[0];
      f.classList.add("winner");
    })

    const h2 = document.querySelector("h2");
    can = false;
    h2.innerHTML = "Ha ganado " + player + ", para seguir jugando pulsa el botón reiniciar.";
    alert("Ganador: " + player);
  }

  function checkWinner(b, p) {
    for (let i = 0; i < wins.length; i++) {
      let fir = wins[i][0];
      let sec = wins[i][1];
      let thr = wins[i][2];

      if (b[fir] === p && b[sec] === p && b[thr] === p) {
        winner(p, fir, sec, thr);
        break;
      }
    }
  }

  function changePlayer(c) {
    c === "X" ? timesX++ : timesO++
    return c === "X" ? currentPlayer = "O" : currentPlayer = "X";
  }

  function resetGame() {
    location.reload();
  }
</script>

<main>
  <div class="game">
    <h2>Turno de <span style="font-size: 30px; font-weight: bolder;" class={currentPlayer === "X" ? "X" : "O"}>{currentPlayer}</span></h2>
    <div class="board">
      <span on:click={handleClick} pos=0 class="board-item tl"></span>
      <span on:click={handleClick} pos=1 class="board-item tb"></span>
      <span on:click={handleClick} pos=2 class="board-item tr"></span>
      <span on:click={handleClick} pos=3 class="board-item ml"></span>
      <span on:click={handleClick} pos=4 class="board-item mb"></span>
      <span on:click={handleClick} pos=5 class="board-item mr"></span>
      <span on:click={handleClick} pos=6 class="board-item bl"></span>
      <span on:click={handleClick} pos=7 class="board-item"></span>
      <span on:click={handleClick} pos=8 class="board-item br"></span>
    </div>
    <button on:click={resetGame}>Reiniciar</button>
  </div>
</main>

<style>
  *, *::after, *::before {
    padding: 0;
    margin: 0;
    box-sizing: border-box;
  }

  :global(.winner) {
    background: #27db54 !important;
    color: #106124 !important;
  }
  :global(.focused) {
    background: #ffc93c !important;
    cursor: pointer;
  }

  h2 {
    font-family: Consolas;
    font-weight: bolder;
    color: black;
    margin-bottom: 10px;
    padding: 10px;
    font-size: 20px;
    text-align: center;
  }

  button {
    margin-top: 25px;
    padding: 10px;
    border-radius: 10px;
    border: none;
    background: #ff9a3c;
    cursor: pointer;
    outline: none;
    transition: 200ms ease-in-out;
    font-family: "Consolas";
    color: white;
    box-shadow: 5px 5px 5px #00000020;
    font-weight: bolder;
  }

  button:hover {
    background: #ffc93c;
    color: #ff4f3c;
  }

  button:active {
    transform: translateY(2px);
    box-shadow: none;
    transition: none;
  }

  main {
    display: flex;
    justify-content: center;
    align-items: center;
    background: #ff4f3c;
    width: 60%;
    margin: 0 auto;
    margin-top: 20px;
    border-radius: 30px;
  }

  .game {
    flex-direction: column;
    height: 80vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .board {
    box-shadow: 5px 5px 5px #00000020;
    display: grid;
    border-radius: 30px;
    grid-template-columns: repeat(3, 1fr);
    grid-column-start: 1;
    grid-column-end: 3;
  }

  .board-item {
    width: 100px;
    height: 100px;
    font-size: 70px;
    user-select: none;
    background: #ff9a3c;
    display: flex;
    justify-content: center;
    align-items: center;
    color: white;
    font-family: "Consolas";
    font-weight: bolder;
  }

  .board-item:hover {
    background: #ffc93c;
    cursor: pointer;
  }

  .X {
    color: #cc2222;
  }

  .O {
    color: #2255aa;
  }

  .tl {
    border-right: 5px solid #ff6f3c;
    border-bottom: 5px solid #ff6f3c;
    border-top-left-radius: 20px;
  }

  .tb {
    border-bottom: 5px solid #ff6f3c;
  }

  .tr {
    border-top-right-radius: 20px;
    border-left: 5px solid #ff6f3c;
    border-bottom: 5px solid #ff6f3c
  }
  
  .ml {
    border-right: 5px solid #ff6f3c;
    border-bottom: 5px solid #ff6f3c;
  }

  .mb {
    border-bottom: 5px solid #ff6f3c;
  }

  .mr {
    border-left: 5px solid #ff6f3c;
    border-bottom: 5px solid #ff6f3c
  }

  .bl {
    border-bottom-left-radius: 20px;
    border-right: 5px solid #ff6f3c;
  }

  .br {
    border-bottom-right-radius: 20px;
    border-left: 5px solid #ff6f3c;
  }
</style>