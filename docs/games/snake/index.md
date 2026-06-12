# 贪吃蛇

<div class="snake-game" data-snake-game>
  <section class="snake-game__top">
    <div>
      <p>Classic Arcade</p>
      <h1>贪吃蛇</h1>
    </div>
    <div class="snake-game__stats">
      <span>分数 <b data-score>0</b></span>
      <span>最佳 <b data-best>0</b></span>
      <span>速度 <b data-speed>1</b></span>
    </div>
  </section>

  <section class="snake-game__stage">
    <canvas width="480" height="480" aria-label="贪吃蛇游戏区域"></canvas>
    <div class="snake-game__overlay" data-overlay>
      <strong>准备开始</strong>
      <span>方向键 / WASD 控制，空格暂停</span>
      <button type="button" data-start>开始游戏</button>
    </div>
  </section>

  <section class="snake-game__controls" aria-label="移动控制">
    <button type="button" data-dir="up">上</button>
    <div>
      <button type="button" data-dir="left">左</button>
      <button type="button" data-dir="down">下</button>
      <button type="button" data-dir="right">右</button>
    </div>
    <button type="button" data-pause>暂停 / 继续</button>
  </section>
</div>

<style>
  .md-content__inner:has(.snake-game) {
    max-width: 980px;
  }

  .snake-game {
    --snake-bg: #06130e;
    --snake-panel: #10231b;
    --snake-green: #47f37b;
    --snake-yellow: #facc15;
    --snake-red: #fb7185;
    display: grid;
    gap: 18px;
    margin: 20px 0 40px;
    padding: clamp(16px, 4vw, 28px);
    border-radius: 24px;
    color: #f3fff7;
    background:
      radial-gradient(circle at 20% 12%, rgba(71, 243, 123, 0.22), transparent 28%),
      radial-gradient(circle at 82% 4%, rgba(250, 204, 21, 0.18), transparent 24%),
      linear-gradient(135deg, #06130e, #071b16 58%, #030807);
    box-shadow: 0 28px 90px rgba(0, 0, 0, 0.28);
  }

  .snake-game__top {
    display: flex;
    align-items: end;
    justify-content: space-between;
    gap: 18px;
  }

  .snake-game__top p {
    margin: 0 0 4px;
    color: var(--snake-green);
    font-weight: 800;
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  .snake-game__top h1 {
    margin: 0;
    color: #fff;
    font-size: clamp(38px, 7vw, 72px);
    line-height: 1;
  }

  .snake-game__stats {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: flex-end;
  }

  .snake-game__stats span {
    min-width: 92px;
    padding: 10px 12px;
    border: 1px solid rgba(71, 243, 123, 0.22);
    border-radius: 14px;
    background: rgba(255, 255, 255, 0.08);
    color: rgba(243, 255, 247, 0.72);
    text-align: center;
  }

  .snake-game__stats b {
    display: block;
    color: #fff;
    font-size: 24px;
  }

  .snake-game__stage {
    position: relative;
    display: grid;
    place-items: center;
    padding: clamp(10px, 3vw, 22px);
    border: 1px solid rgba(71, 243, 123, 0.22);
    border-radius: 20px;
    background:
      linear-gradient(rgba(71, 243, 123, 0.08) 1px, transparent 1px),
      linear-gradient(90deg, rgba(71, 243, 123, 0.08) 1px, transparent 1px),
      rgba(0, 0, 0, 0.24);
    background-size: 24px 24px;
  }

  .snake-game canvas {
    width: min(100%, 480px);
    aspect-ratio: 1;
    height: auto;
    border-radius: 18px;
    background: #07110d;
    box-shadow: inset 0 0 0 2px rgba(255, 255, 255, 0.08), 0 16px 42px rgba(0, 0, 0, 0.3);
  }

  .snake-game__overlay {
    position: absolute;
    inset: clamp(22px, 7vw, 70px);
    display: grid;
    place-items: center;
    align-content: center;
    gap: 12px;
    border-radius: 18px;
    background: rgba(3, 8, 7, 0.72);
    backdrop-filter: blur(10px);
    text-align: center;
  }

  .snake-game__overlay[hidden] {
    display: none;
  }

  .snake-game__overlay strong {
    font-size: 34px;
  }

  .snake-game__overlay span {
    color: rgba(243, 255, 247, 0.76);
  }

  .snake-game button {
    border: 0;
    border-radius: 999px;
    padding: 11px 16px;
    color: #07110d;
    background: var(--snake-green);
    font-weight: 800;
    cursor: pointer;
    transition: transform 160ms ease, filter 160ms ease;
  }

  .snake-game button:hover {
    transform: translateY(-2px);
    filter: brightness(1.08);
  }

  .snake-game__controls {
    display: grid;
    justify-items: center;
    gap: 10px;
  }

  .snake-game__controls div {
    display: flex;
    gap: 10px;
  }

  @media (max-width: 720px) {
    .snake-game__top {
      align-items: stretch;
      flex-direction: column;
    }

    .snake-game__stats {
      justify-content: stretch;
    }

    .snake-game__stats span {
      flex: 1;
    }
  }
</style>

<script>
  (() => {
    const root = document.querySelector("[data-snake-game]");
    if (!root) return;

    const canvas = root.querySelector("canvas");
    const ctx = canvas.getContext("2d");
    const overlay = root.querySelector("[data-overlay]");
    const scoreEl = root.querySelector("[data-score]");
    const bestEl = root.querySelector("[data-best]");
    const speedEl = root.querySelector("[data-speed]");
    const size = 20;
    const cells = canvas.width / size;
    let snake;
    let food;
    let direction;
    let nextDirection;
    let score;
    let best = Number(localStorage.getItem("fy-snake-best") || 0);
    let timer = null;
    let paused = false;
    let running = false;
    let interval = 150;

    bestEl.textContent = best;

    function reset() {
      snake = [
        { x: 8, y: 10 },
        { x: 7, y: 10 },
        { x: 6, y: 10 }
      ];
      direction = { x: 1, y: 0 };
      nextDirection = { x: 1, y: 0 };
      score = 0;
      interval = 150;
      paused = false;
      running = true;
      placeFood();
      updateHud();
      draw();
    }

    function start() {
      reset();
      overlay.hidden = true;
      clearInterval(timer);
      timer = setInterval(step, interval);
    }

    function updateHud() {
      scoreEl.textContent = score;
      speedEl.textContent = Math.max(1, Math.round((170 - interval) / 18));
      if (score > best) {
        best = score;
        bestEl.textContent = best;
        localStorage.setItem("fy-snake-best", String(best));
      }
    }

    function placeFood() {
      do {
        food = {
          x: Math.floor(Math.random() * cells),
          y: Math.floor(Math.random() * cells)
        };
      } while (snake.some((part) => part.x === food.x && part.y === food.y));
    }

    function step() {
      if (!running || paused) return;
      direction = nextDirection;
      const head = {
        x: snake[0].x + direction.x,
        y: snake[0].y + direction.y
      };

      const hitWall = head.x < 0 || head.x >= cells || head.y < 0 || head.y >= cells;
      const hitSelf = snake.some((part) => part.x === head.x && part.y === head.y);
      if (hitWall || hitSelf) {
        gameOver();
        return;
      }

      snake.unshift(head);
      if (head.x === food.x && head.y === food.y) {
        score += 10;
        if (score % 40 === 0 && interval > 74) {
          interval -= 10;
          clearInterval(timer);
          timer = setInterval(step, interval);
        }
        placeFood();
      } else {
        snake.pop();
      }

      updateHud();
      draw();
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      ctx.fillStyle = "#07110d";
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      for (let i = 0; i < snake.length; i++) {
        const part = snake[i];
        const gradient = ctx.createLinearGradient(part.x * size, part.y * size, (part.x + 1) * size, (part.y + 1) * size);
        gradient.addColorStop(0, i === 0 ? "#facc15" : "#47f37b");
        gradient.addColorStop(1, i === 0 ? "#fb7185" : "#16a34a");
        ctx.fillStyle = gradient;
        roundRect(part.x * size + 2, part.y * size + 2, size - 4, size - 4, 6);
        ctx.fill();
      }

      ctx.fillStyle = "#fb7185";
      ctx.shadowColor = "#fb7185";
      ctx.shadowBlur = 18;
      roundRect(food.x * size + 4, food.y * size + 4, size - 8, size - 8, 8);
      ctx.fill();
      ctx.shadowBlur = 0;
    }

    function roundRect(x, y, w, h, r) {
      ctx.beginPath();
      ctx.moveTo(x + r, y);
      ctx.arcTo(x + w, y, x + w, y + h, r);
      ctx.arcTo(x + w, y + h, x, y + h, r);
      ctx.arcTo(x, y + h, x, y, r);
      ctx.arcTo(x, y, x + w, y, r);
      ctx.closePath();
    }

    function gameOver() {
      running = false;
      clearInterval(timer);
      overlay.hidden = false;
      overlay.querySelector("strong").textContent = "游戏结束";
      overlay.querySelector("span").textContent = "本局得分 " + score + "，点击重新开始";
      overlay.querySelector("button").textContent = "再来一局";
    }

    function setDirection(dir) {
      const map = {
        up: { x: 0, y: -1 },
        down: { x: 0, y: 1 },
        left: { x: -1, y: 0 },
        right: { x: 1, y: 0 }
      };
      const next = map[dir];
      if (!next) return;
      if (next.x + direction.x === 0 && next.y + direction.y === 0) return;
      nextDirection = next;
    }

    function togglePause() {
      if (!running) return;
      paused = !paused;
      overlay.hidden = !paused;
      overlay.querySelector("strong").textContent = "暂停中";
      overlay.querySelector("span").textContent = "按空格或按钮继续";
      overlay.querySelector("button").textContent = "继续";
    }

    root.querySelector("[data-start]").addEventListener("click", () => {
      if (paused) {
        togglePause();
      } else {
        start();
      }
    });

    root.querySelector("[data-pause]").addEventListener("click", togglePause);
    root.querySelectorAll("[data-dir]").forEach((button) => {
      button.addEventListener("click", () => setDirection(button.dataset.dir));
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
      if (keyMap[event.key]) {
        event.preventDefault();
        setDirection(keyMap[event.key]);
      }
      if (event.code === "Space") {
        event.preventDefault();
        togglePause();
      }
    });

    reset();
    running = false;
    draw();
  })();
</script>
