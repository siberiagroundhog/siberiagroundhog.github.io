# 2048

<div class="game-2048" data-game-2048>
  <section class="game-2048__top">
    <div>
      <p>Number Puzzle</p>
      <h1>2048</h1>
    </div>
    <div class="game-2048__scores">
      <span>分数 <b data-score>0</b></span>
      <span>最佳 <b data-best>0</b></span>
    </div>
  </section>

  <section class="game-2048__board" data-board aria-label="2048 游戏棋盘"></section>

  <section class="game-2048__actions">
    <button type="button" data-new>新游戏</button>
    <div class="game-2048__pad" aria-label="移动控制">
      <button type="button" data-move="up">上</button>
      <button type="button" data-move="left">左</button>
      <button type="button" data-move="down">下</button>
      <button type="button" data-move="right">右</button>
    </div>
  </section>

  <p class="game-2048__hint">使用方向键 / WASD / 下方按钮移动方块。合并相同数字，冲到 2048。</p>
  <div class="game-2048__message" data-message hidden></div>
</div>

<style>
  .md-content__inner:has(.game-2048) {
    max-width: 900px;
  }

  .game-2048 {
    --g2048-bg: #21160e;
    --g2048-panel: #bbada0;
    --g2048-cell: rgba(238, 228, 218, 0.35);
    display: grid;
    gap: 18px;
    margin: 20px 0 40px;
    padding: clamp(16px, 4vw, 28px);
    border-radius: 26px;
    color: #fff7ed;
    background:
      radial-gradient(circle at 16% 10%, rgba(251, 146, 60, 0.25), transparent 28%),
      radial-gradient(circle at 84% 2%, rgba(250, 204, 21, 0.2), transparent 24%),
      linear-gradient(135deg, #1c1109, #43240e 58%, #140b06);
    box-shadow: 0 28px 90px rgba(67, 36, 14, 0.28);
  }

  .game-2048__top {
    display: flex;
    align-items: end;
    justify-content: space-between;
    gap: 18px;
  }

  .game-2048__top p {
    margin: 0 0 4px;
    color: #fed7aa;
    font-weight: 900;
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  .game-2048__top h1 {
    margin: 0;
    color: #fff;
    font-size: clamp(48px, 9vw, 92px);
    line-height: 0.9;
  }

  .game-2048__scores {
    display: flex;
    gap: 10px;
  }

  .game-2048__scores span {
    min-width: 108px;
    padding: 12px;
    border-radius: 14px;
    background: rgba(255, 255, 255, 0.13);
    text-align: center;
    color: rgba(255, 247, 237, 0.72);
  }

  .game-2048__scores b {
    display: block;
    color: #fff;
    font-size: 25px;
  }

  .game-2048__board {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: clamp(8px, 2vw, 14px);
    width: min(100%, 560px);
    aspect-ratio: 1;
    margin: 0 auto;
    padding: clamp(8px, 2vw, 14px);
    border-radius: 22px;
    background: var(--g2048-panel);
    box-shadow: inset 0 0 0 2px rgba(255, 255, 255, 0.16), 0 20px 58px rgba(0, 0, 0, 0.26);
  }

  .game-2048__cell {
    display: grid;
    place-items: center;
    border-radius: 16px;
    background: var(--g2048-cell);
    color: #776e65;
    font-size: clamp(28px, 8vw, 58px);
    font-weight: 900;
    line-height: 1;
    transition: transform 120ms ease, background 120ms ease;
  }

  .game-2048__cell.is-new {
    animation: tile-pop 180ms ease;
  }

  .game-2048__cell[data-value="2"] { background: #eee4da; }
  .game-2048__cell[data-value="4"] { background: #ede0c8; }
  .game-2048__cell[data-value="8"] { background: #f2b179; color: #fff; }
  .game-2048__cell[data-value="16"] { background: #f59563; color: #fff; }
  .game-2048__cell[data-value="32"] { background: #f67c5f; color: #fff; }
  .game-2048__cell[data-value="64"] { background: #f65e3b; color: #fff; }
  .game-2048__cell[data-value="128"] { background: #edcf72; color: #fff; font-size: clamp(24px, 6.8vw, 48px); }
  .game-2048__cell[data-value="256"] { background: #edcc61; color: #fff; font-size: clamp(24px, 6.8vw, 48px); }
  .game-2048__cell[data-value="512"] { background: #edc850; color: #fff; font-size: clamp(24px, 6.8vw, 48px); }
  .game-2048__cell[data-value="1024"] { background: #edc53f; color: #fff; font-size: clamp(20px, 5.8vw, 40px); }
  .game-2048__cell[data-value="2048"] { background: #edc22e; color: #fff; font-size: clamp(20px, 5.8vw, 40px); box-shadow: 0 0 24px rgba(237, 194, 46, 0.55); }

  .game-2048__actions {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
    gap: 12px;
  }

  .game-2048 button {
    border: 0;
    border-radius: 999px;
    padding: 11px 16px;
    color: #3a220f;
    background: #fed7aa;
    font-weight: 900;
    cursor: pointer;
    transition: transform 160ms ease, filter 160ms ease;
  }

  .game-2048 button:hover {
    transform: translateY(-2px);
    filter: brightness(1.06);
  }

  .game-2048__pad {
    display: flex;
    gap: 8px;
    flex-wrap: wrap;
    justify-content: center;
  }

  .game-2048__hint {
    margin: 0;
    color: rgba(255, 247, 237, 0.76);
    text-align: center;
  }

  .game-2048__message {
    padding: 14px 16px;
    border-radius: 14px;
    background: rgba(255, 255, 255, 0.13);
    color: #fff;
    text-align: center;
    font-weight: 900;
  }

  @keyframes tile-pop {
    from {
      transform: scale(0.72);
    }
    to {
      transform: scale(1);
    }
  }

  @media (max-width: 720px) {
    .game-2048__top {
      align-items: stretch;
      flex-direction: column;
    }

    .game-2048__scores span {
      flex: 1;
    }
  }
</style>

<script>
  (() => {
    const root = document.querySelector("[data-game-2048]");
    if (!root) return;

    const boardEl = root.querySelector("[data-board]");
    const scoreEl = root.querySelector("[data-score]");
    const bestEl = root.querySelector("[data-best]");
    const messageEl = root.querySelector("[data-message]");
    let board = [];
    let score = 0;
    let best = Number(localStorage.getItem("fy-2048-best") || 0);
    let won = false;

    bestEl.textContent = best;

    function newGame() {
      board = Array.from({ length: 4 }, () => Array(4).fill(0));
      score = 0;
      won = false;
      messageEl.hidden = true;
      addRandomTile();
      addRandomTile();
      render();
    }

    function addRandomTile() {
      const empty = [];
      for (let y = 0; y < 4; y++) {
        for (let x = 0; x < 4; x++) {
          if (!board[y][x]) empty.push({ x, y });
        }
      }
      if (!empty.length) return;
      const pick = empty[Math.floor(Math.random() * empty.length)];
      board[pick.y][pick.x] = Math.random() < 0.9 ? 2 : 4;
    }

    function render() {
      boardEl.innerHTML = "";
      scoreEl.textContent = score;
      if (score > best) {
        best = score;
        bestEl.textContent = best;
        localStorage.setItem("fy-2048-best", String(best));
      }

      for (let y = 0; y < 4; y++) {
        for (let x = 0; x < 4; x++) {
          const value = board[y][x];
          const cell = document.createElement("div");
          cell.className = "game-2048__cell";
          if (value) {
            cell.textContent = value;
            cell.dataset.value = value > 2048 ? 2048 : value;
          }
          boardEl.appendChild(cell);
        }
      }
    }

    function slide(row) {
      const filtered = row.filter(Boolean);
      const merged = [];
      for (let i = 0; i < filtered.length; i++) {
        if (filtered[i] === filtered[i + 1]) {
          const value = filtered[i] * 2;
          merged.push(value);
          score += value;
          if (value === 2048 && !won) {
            won = true;
            showMessage("漂亮，已经合成 2048！");
          }
          i++;
        } else {
          merged.push(filtered[i]);
        }
      }
      while (merged.length < 4) merged.push(0);
      return merged;
    }

    function move(direction) {
      const before = JSON.stringify(board);

      if (direction === "left") {
        board = board.map(slide);
      }

      if (direction === "right") {
        board = board.map((row) => slide([...row].reverse()).reverse());
      }

      if (direction === "up" || direction === "down") {
        const next = Array.from({ length: 4 }, () => Array(4).fill(0));
        for (let x = 0; x < 4; x++) {
          let column = [board[0][x], board[1][x], board[2][x], board[3][x]];
          if (direction === "down") column.reverse();
          column = slide(column);
          if (direction === "down") column.reverse();
          for (let y = 0; y < 4; y++) next[y][x] = column[y];
        }
        board = next;
      }

      if (before !== JSON.stringify(board)) {
        addRandomTile();
        render();
        if (!canMove()) showMessage("没有可移动的格子了，本局结束。");
      }
    }

    function canMove() {
      for (let y = 0; y < 4; y++) {
        for (let x = 0; x < 4; x++) {
          if (!board[y][x]) return true;
          if (x < 3 && board[y][x] === board[y][x + 1]) return true;
          if (y < 3 && board[y][x] === board[y + 1][x]) return true;
        }
      }
      return false;
    }

    function showMessage(text) {
      messageEl.textContent = text;
      messageEl.hidden = false;
    }

    root.querySelector("[data-new]").addEventListener("click", newGame);
    root.querySelectorAll("[data-move]").forEach((button) => {
      button.addEventListener("click", () => move(button.dataset.move));
    });

    window.addEventListener("keydown", (event) => {
      const keyMap = {
        ArrowUp: "up",
        w: "up",
        W: "up",
        ArrowDown: "down",
        s: "down",
        S: "down",
        ArrowLeft: "left",
        a: "left",
        A: "left",
        ArrowRight: "right",
        d: "right",
        D: "right"
      };
      const direction = keyMap[event.key];
      if (direction) {
        event.preventDefault();
        move(direction);
      }
    });

    let touchStart = null;
    boardEl.addEventListener("touchstart", (event) => {
      const touch = event.changedTouches[0];
      touchStart = { x: touch.clientX, y: touch.clientY };
    }, { passive: true });

    boardEl.addEventListener("touchend", (event) => {
      if (!touchStart) return;
      const touch = event.changedTouches[0];
      const dx = touch.clientX - touchStart.x;
      const dy = touch.clientY - touchStart.y;
      if (Math.max(Math.abs(dx), Math.abs(dy)) < 24) return;
      move(Math.abs(dx) > Math.abs(dy) ? (dx > 0 ? "right" : "left") : (dy > 0 ? "down" : "up"));
      touchStart = null;
    }, { passive: true });

    newGame();
  })();
</script>
