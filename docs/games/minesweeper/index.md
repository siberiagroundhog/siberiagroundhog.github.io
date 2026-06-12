# 扫雷

<div class="mine-game" data-mine-game>
  <section class="mine-game__top">
    <div>
      <p>Logic Puzzle</p>
      <h1>扫雷</h1>
    </div>
    <div class="mine-game__stats">
      <span>雷数 <b data-mines-left>12</b></span>
      <span>时间 <b data-time>0</b></span>
      <span>状态 <b data-status>待开始</b></span>
    </div>
  </section>

  <section class="mine-game__toolbar">
    <button type="button" data-new>新游戏</button>
    <button type="button" data-size="easy">简单 9x9</button>
    <button type="button" data-size="normal">标准 12x12</button>
    <button type="button" data-size="hard">困难 16x16</button>
  </section>

  <section class="mine-game__board" data-board aria-label="扫雷棋盘"></section>
  <p class="mine-game__hint">左键翻开格子，右键插旗。第一下不会踩雷。</p>
</div>

<style>
  .md-content__inner:has(.mine-game) {
    max-width: 1100px;
  }

  .mine-game {
    --mine-blue: #38bdf8;
    --mine-dark: #0f172a;
    --mine-panel: rgba(15, 23, 42, 0.72);
    display: grid;
    gap: 18px;
    margin: 20px 0 40px;
    padding: clamp(16px, 4vw, 28px);
    border-radius: 26px;
    color: #eff6ff;
    background:
      radial-gradient(circle at 14% 8%, rgba(56, 189, 248, 0.24), transparent 28%),
      radial-gradient(circle at 84% 0%, rgba(129, 140, 248, 0.2), transparent 28%),
      linear-gradient(135deg, #020617, #0f172a 56%, #082f49);
    box-shadow: 0 28px 90px rgba(15, 23, 42, 0.3);
  }

  .mine-game__top {
    display: flex;
    justify-content: space-between;
    align-items: end;
    gap: 18px;
  }

  .mine-game__top p {
    margin: 0 0 4px;
    color: var(--mine-blue);
    font-weight: 900;
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  .mine-game__top h1 {
    margin: 0;
    color: #fff;
    font-size: clamp(44px, 8vw, 82px);
    line-height: 0.96;
  }

  .mine-game__stats {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: flex-end;
  }

  .mine-game__stats span {
    min-width: 98px;
    padding: 10px 12px;
    border: 1px solid rgba(125, 211, 252, 0.22);
    border-radius: 14px;
    background: rgba(255, 255, 255, 0.08);
    color: rgba(239, 246, 255, 0.74);
    text-align: center;
  }

  .mine-game__stats b {
    display: block;
    color: #fff;
    font-size: 22px;
  }

  .mine-game__toolbar {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
  }

  .mine-game button {
    border: 0;
    border-radius: 999px;
    padding: 10px 14px;
    color: #082f49;
    background: #bae6fd;
    font-weight: 900;
    cursor: pointer;
    transition: transform 160ms ease, filter 160ms ease;
  }

  .mine-game button:hover {
    transform: translateY(-2px);
    filter: brightness(1.06);
  }

  .mine-game__board {
    display: grid;
    gap: 6px;
    width: min(100%, 720px);
    margin: 0 auto;
    padding: clamp(10px, 2vw, 16px);
    border: 1px solid rgba(125, 211, 252, 0.22);
    border-radius: 22px;
    background: rgba(2, 6, 23, 0.48);
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.08), 0 20px 64px rgba(0, 0, 0, 0.28);
  }

  .mine-game__cell {
    display: grid;
    place-items: center;
    aspect-ratio: 1;
    min-width: 0;
    border: 0;
    border-radius: 8px;
    color: #e0f2fe;
    background: linear-gradient(145deg, rgba(56, 189, 248, 0.36), rgba(37, 99, 235, 0.46));
    box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.18);
    font-size: clamp(12px, 2.6vw, 20px);
    font-weight: 900;
    cursor: pointer;
  }

  .mine-game__cell:hover {
    filter: brightness(1.16);
  }

  .mine-game__cell[data-state="revealed"] {
    color: #0f172a;
    background: rgba(226, 232, 240, 0.92);
    cursor: default;
  }

  .mine-game__cell[data-state="flagged"] {
    background: linear-gradient(145deg, #facc15, #f97316);
    color: #111827;
  }

  .mine-game__cell[data-mine="true"] {
    background: linear-gradient(145deg, #fb7185, #be123c);
    color: #fff;
  }

  .mine-game__cell[data-near="1"] { color: #2563eb; }
  .mine-game__cell[data-near="2"] { color: #16a34a; }
  .mine-game__cell[data-near="3"] { color: #dc2626; }
  .mine-game__cell[data-near="4"] { color: #7c3aed; }
  .mine-game__cell[data-near="5"] { color: #b45309; }
  .mine-game__cell[data-near="6"] { color: #0891b2; }
  .mine-game__cell[data-near="7"] { color: #111827; }
  .mine-game__cell[data-near="8"] { color: #475569; }

  .mine-game__hint {
    margin: 0;
    color: rgba(239, 246, 255, 0.76);
    text-align: center;
  }

  @media (max-width: 760px) {
    .mine-game__top {
      align-items: stretch;
      flex-direction: column;
    }

    .mine-game__stats span {
      flex: 1;
    }

    .mine-game__board {
      gap: 4px;
    }
  }
</style>

<script>
  (() => {
    const root = document.querySelector("[data-mine-game]");
    if (!root) return;

    const boardEl = root.querySelector("[data-board]");
    const minesLeftEl = root.querySelector("[data-mines-left]");
    const timeEl = root.querySelector("[data-time]");
    const statusEl = root.querySelector("[data-status]");
    const presets = {
      easy: { rows: 9, cols: 9, mines: 10 },
      normal: { rows: 12, cols: 12, mines: 20 },
      hard: { rows: 16, cols: 16, mines: 40 }
    };
    let config = presets.easy;
    let cells = [];
    let started = false;
    let finished = false;
    let flags = 0;
    let revealed = 0;
    let seconds = 0;
    let timer = null;

    function newGame(nextConfig = config) {
      config = nextConfig;
      cells = Array.from({ length: config.rows * config.cols }, (_, index) => ({
        index,
        row: Math.floor(index / config.cols),
        col: index % config.cols,
        mine: false,
        near: 0,
        revealed: false,
        flagged: false
      }));
      started = false;
      finished = false;
      flags = 0;
      revealed = 0;
      seconds = 0;
      clearInterval(timer);
      timer = null;
      statusEl.textContent = "待开始";
      updateHud();
      render();
    }

    function startTimer() {
      if (timer) return;
      timer = setInterval(() => {
        seconds++;
        timeEl.textContent = seconds;
      }, 1000);
    }

    function placeMines(safeIndex) {
      const safe = new Set([safeIndex, ...neighbors(safeIndex).map((cell) => cell.index)]);
      const choices = cells.filter((cell) => !safe.has(cell.index));
      for (let i = choices.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [choices[i], choices[j]] = [choices[j], choices[i]];
      }
      choices.slice(0, config.mines).forEach((cell) => {
        cell.mine = true;
      });
      cells.forEach((cell) => {
        cell.near = neighbors(cell.index).filter((item) => item.mine).length;
      });
    }

    function neighbors(index) {
      const cell = cells[index];
      const list = [];
      for (let dr = -1; dr <= 1; dr++) {
        for (let dc = -1; dc <= 1; dc++) {
          if (!dr && !dc) continue;
          const row = cell.row + dr;
          const col = cell.col + dc;
          if (row >= 0 && row < config.rows && col >= 0 && col < config.cols) {
            list.push(cells[row * config.cols + col]);
          }
        }
      }
      return list;
    }

    function reveal(index) {
      if (finished) return;
      const cell = cells[index];
      if (cell.revealed || cell.flagged) return;

      if (!started) {
        placeMines(index);
        started = true;
        statusEl.textContent = "进行中";
        startTimer();
      }

      cell.revealed = true;
      revealed++;

      if (cell.mine) {
        lose();
        return;
      }

      if (cell.near === 0) {
        neighbors(index).forEach((item) => reveal(item.index));
      }

      checkWin();
      render();
    }

    function toggleFlag(index) {
      if (finished) return;
      const cell = cells[index];
      if (cell.revealed) return;
      cell.flagged = !cell.flagged;
      flags += cell.flagged ? 1 : -1;
      updateHud();
      render();
    }

    function lose() {
      finished = true;
      clearInterval(timer);
      statusEl.textContent = "踩雷";
      cells.forEach((cell) => {
        if (cell.mine) cell.revealed = true;
      });
      render();
    }

    function checkWin() {
      if (revealed === config.rows * config.cols - config.mines) {
        finished = true;
        clearInterval(timer);
        statusEl.textContent = "胜利";
        cells.forEach((cell) => {
          if (cell.mine) cell.flagged = true;
        });
        flags = config.mines;
      }
      updateHud();
    }

    function updateHud() {
      minesLeftEl.textContent = Math.max(0, config.mines - flags);
      timeEl.textContent = seconds;
    }

    function render() {
      boardEl.style.gridTemplateColumns = "repeat(" + config.cols + ", minmax(0, 1fr))";
      boardEl.innerHTML = "";
      cells.forEach((cell) => {
        const button = document.createElement("button");
        button.type = "button";
        button.className = "mine-game__cell";
        button.dataset.index = cell.index;
        if (cell.revealed) {
          button.dataset.state = "revealed";
          if (cell.mine) {
            button.dataset.mine = "true";
            button.textContent = "雷";
          } else if (cell.near) {
            button.dataset.near = cell.near;
            button.textContent = cell.near;
          }
        } else if (cell.flagged) {
          button.dataset.state = "flagged";
          button.textContent = "旗";
        }
        button.addEventListener("click", () => reveal(cell.index));
        button.addEventListener("contextmenu", (event) => {
          event.preventDefault();
          toggleFlag(cell.index);
        });
        boardEl.appendChild(button);
      });
    }

    root.querySelector("[data-new]").addEventListener("click", () => newGame());
    root.querySelectorAll("[data-size]").forEach((button) => {
      button.addEventListener("click", () => newGame(presets[button.dataset.size]));
    });

    newGame();
  })();
</script>
