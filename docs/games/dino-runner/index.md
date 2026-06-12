# 小恐龙跑酷

<div class="dino-game" data-dino-game>
  <section class="dino-game__top">
    <div>
      <p>Endless Runner</p>
      <h1>小恐龙跑酷</h1>
    </div>
    <div class="dino-game__stats">
      <span>分数 <b data-score>0</b></span>
      <span>最佳 <b data-best>0</b></span>
      <span>速度 <b data-speed>1</b></span>
    </div>
  </section>

  <section class="dino-game__stage">
    <canvas width="900" height="360" aria-label="小恐龙跑酷游戏区域"></canvas>
    <div class="dino-game__overlay" data-overlay>
      <strong>准备起跑</strong>
      <span>空格、上方向键或点击画面跳跃</span>
      <button type="button" data-start>开始游戏</button>
    </div>
  </section>

  <section class="dino-game__actions">
    <button type="button" data-jump>跳跃</button>
    <button type="button" data-restart>重新开始</button>
  </section>
</div>

<style>
  .md-content__inner:has(.dino-game) {
    max-width: 1100px;
  }

  .dino-game {
    --dino-lime: #a3e635;
    --dino-dark: #18181b;
    display: grid;
    gap: 18px;
    margin: 20px 0 40px;
    padding: clamp(16px, 4vw, 28px);
    border-radius: 26px;
    color: #f7fee7;
    background:
      radial-gradient(circle at 16% 8%, rgba(163, 230, 53, 0.24), transparent 28%),
      radial-gradient(circle at 90% 0%, rgba(34, 197, 94, 0.16), transparent 24%),
      linear-gradient(135deg, #111113, #27272a 56%, #1a2e05);
    box-shadow: 0 28px 90px rgba(24, 24, 27, 0.32);
  }

  .dino-game__top {
    display: flex;
    justify-content: space-between;
    align-items: end;
    gap: 18px;
  }

  .dino-game__top p {
    margin: 0 0 4px;
    color: var(--dino-lime);
    font-weight: 900;
    letter-spacing: 0.12em;
    text-transform: uppercase;
  }

  .dino-game__top h1 {
    margin: 0;
    color: #fff;
    font-size: clamp(40px, 7vw, 82px);
    line-height: 0.96;
  }

  .dino-game__stats {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: flex-end;
  }

  .dino-game__stats span {
    min-width: 96px;
    padding: 10px 12px;
    border: 1px solid rgba(190, 242, 100, 0.22);
    border-radius: 14px;
    background: rgba(255, 255, 255, 0.08);
    color: rgba(247, 254, 231, 0.74);
    text-align: center;
  }

  .dino-game__stats b {
    display: block;
    color: #fff;
    font-size: 22px;
  }

  .dino-game__stage {
    position: relative;
    overflow: hidden;
    border: 1px solid rgba(190, 242, 100, 0.22);
    border-radius: 22px;
    background: #fafaf9;
    box-shadow: 0 20px 64px rgba(0, 0, 0, 0.28);
  }

  .dino-game canvas {
    display: block;
    width: 100%;
    height: auto;
    max-height: 520px;
    background: linear-gradient(#f5f5f4, #ecfccb);
  }

  .dino-game__overlay {
    position: absolute;
    inset: clamp(22px, 8vw, 82px);
    display: grid;
    place-items: center;
    align-content: center;
    gap: 12px;
    border-radius: 20px;
    background: rgba(24, 24, 27, 0.74);
    backdrop-filter: blur(10px);
    text-align: center;
  }

  .dino-game__overlay[hidden] {
    display: none;
  }

  .dino-game__overlay strong {
    color: #fff;
    font-size: 34px;
  }

  .dino-game__overlay span {
    color: rgba(247, 254, 231, 0.78);
  }

  .dino-game__actions {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 10px;
  }

  .dino-game button {
    border: 0;
    border-radius: 999px;
    padding: 11px 16px;
    color: #1a2e05;
    background: var(--dino-lime);
    font-weight: 900;
    cursor: pointer;
    transition: transform 160ms ease, filter 160ms ease;
  }

  .dino-game button:hover {
    transform: translateY(-2px);
    filter: brightness(1.07);
  }

  @media (max-width: 760px) {
    .dino-game__top {
      align-items: stretch;
      flex-direction: column;
    }

    .dino-game__stats span {
      flex: 1;
    }
  }
</style>

<script>
  (() => {
    const root = document.querySelector("[data-dino-game]");
    if (!root) return;

    const canvas = root.querySelector("canvas");
    const ctx = canvas.getContext("2d");
    const overlay = root.querySelector("[data-overlay]");
    const scoreEl = root.querySelector("[data-score]");
    const bestEl = root.querySelector("[data-best]");
    const speedEl = root.querySelector("[data-speed]");
    let best = Number(localStorage.getItem("fy-dino-best") || 0);
    let running = false;
    let lastTime = 0;
    let spawnTimer = 0;
    let score = 0;
    let speed = 5.2;
    let groundY = 278;
    let obstacles = [];
    let dust = [];
    let frameId = null;
    const dino = {
      x: 86,
      y: groundY - 62,
      w: 48,
      h: 62,
      vy: 0,
      jumping: false
    };

    bestEl.textContent = best;

    function reset() {
      if (frameId) cancelAnimationFrame(frameId);
      running = true;
      lastTime = performance.now();
      spawnTimer = 0;
      score = 0;
      speed = 5.2;
      obstacles = [];
      dust = [];
      dino.y = groundY - dino.h;
      dino.vy = 0;
      dino.jumping = false;
      overlay.hidden = true;
      updateHud();
      frameId = requestAnimationFrame(loop);
    }

    function jump() {
      if (!running) {
        reset();
        return;
      }
      if (!dino.jumping) {
        dino.vy = -15.4;
        dino.jumping = true;
      }
    }

    function loop(time) {
      if (!running) return;
      const dt = Math.min(32, time - lastTime) / 16.67;
      lastTime = time;
      update(dt);
      draw();
      if (running) frameId = requestAnimationFrame(loop);
    }

    function update(dt) {
      score += dt * 0.42;
      speed += dt * 0.0024;
      dino.vy += 0.82 * dt;
      dino.y += dino.vy * dt;
      if (dino.y >= groundY - dino.h) {
        dino.y = groundY - dino.h;
        dino.vy = 0;
        dino.jumping = false;
      }

      spawnTimer -= dt;
      if (spawnTimer <= 0) {
        obstacles.push(makeObstacle());
        spawnTimer = 56 + Math.random() * 58 - Math.min(22, speed * 2.2);
      }

      obstacles.forEach((obstacle) => {
        obstacle.x -= speed * dt;
      });
      obstacles = obstacles.filter((obstacle) => obstacle.x + obstacle.w > -30);

      if (!dino.jumping && Math.random() < 0.35) {
        dust.push({ x: dino.x + 8, y: groundY - 10, r: 3 + Math.random() * 4, life: 1 });
      }
      dust.forEach((item) => {
        item.x -= speed * 0.72 * dt;
        item.y -= 0.6 * dt;
        item.life -= 0.04 * dt;
      });
      dust = dust.filter((item) => item.life > 0);

      if (obstacles.some(hit)) {
        gameOver();
      }

      updateHud();
    }

    function makeObstacle() {
      const tall = Math.random() > 0.5;
      return {
        x: canvas.width + 20,
        y: groundY - (tall ? 60 : 42),
        w: tall ? 28 : 38,
        h: tall ? 60 : 42
      };
    }

    function hit(obstacle) {
      const pad = 8;
      return dino.x + pad < obstacle.x + obstacle.w &&
        dino.x + dino.w - pad > obstacle.x &&
        dino.y + pad < obstacle.y + obstacle.h &&
        dino.y + dino.h - pad > obstacle.y;
    }

    function updateHud() {
      const rounded = Math.floor(score);
      scoreEl.textContent = rounded;
      speedEl.textContent = Math.max(1, Math.floor(speed - 4));
      if (rounded > best) {
        best = rounded;
        bestEl.textContent = best;
        localStorage.setItem("fy-dino-best", String(best));
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      const sky = ctx.createLinearGradient(0, 0, 0, canvas.height);
      sky.addColorStop(0, "#f5f5f4");
      sky.addColorStop(1, "#ecfccb");
      ctx.fillStyle = sky;
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      ctx.fillStyle = "rgba(113, 113, 122, 0.16)";
      for (let i = 0; i < 7; i++) {
        const x = (i * 170 - (score * 2.1) % 170);
        ctx.beginPath();
        ctx.ellipse(x + 90, 82 + (i % 3) * 22, 46, 12, 0, 0, Math.PI * 2);
        ctx.fill();
      }

      ctx.strokeStyle = "#84cc16";
      ctx.lineWidth = 4;
      ctx.beginPath();
      ctx.moveTo(0, groundY);
      ctx.lineTo(canvas.width, groundY);
      ctx.stroke();

      dust.forEach((item) => {
        ctx.globalAlpha = item.life;
        ctx.fillStyle = "#a1a1aa";
        ctx.beginPath();
        ctx.arc(item.x, item.y, item.r, 0, Math.PI * 2);
        ctx.fill();
      });
      ctx.globalAlpha = 1;

      drawDino();
      obstacles.forEach(drawObstacle);
    }

    function drawDino() {
      ctx.fillStyle = "#3f6212";
      roundRect(dino.x, dino.y + 8, dino.w, dino.h - 8, 12);
      ctx.fill();
      ctx.fillStyle = "#65a30d";
      roundRect(dino.x + 24, dino.y, 36, 34, 12);
      ctx.fill();
      ctx.fillStyle = "#111827";
      ctx.beginPath();
      ctx.arc(dino.x + 48, dino.y + 12, 4, 0, Math.PI * 2);
      ctx.fill();
      ctx.strokeStyle = "#3f6212";
      ctx.lineWidth = 8;
      ctx.beginPath();
      ctx.moveTo(dino.x + 4, dino.y + 42);
      ctx.lineTo(dino.x - 18, dino.y + 28);
      ctx.stroke();
      ctx.lineWidth = 6;
      ctx.beginPath();
      ctx.moveTo(dino.x + 16, dino.y + dino.h);
      ctx.lineTo(dino.x + 4, dino.y + dino.h + 16);
      ctx.moveTo(dino.x + 34, dino.y + dino.h);
      ctx.lineTo(dino.x + 48, dino.y + dino.h + 14);
      ctx.stroke();
    }

    function drawObstacle(obstacle) {
      ctx.fillStyle = "#57534e";
      roundRect(obstacle.x, obstacle.y, obstacle.w, obstacle.h, 8);
      ctx.fill();
      ctx.fillStyle = "#71717a";
      roundRect(obstacle.x + obstacle.w * 0.46, obstacle.y - 12, obstacle.w * 0.32, 18, 6);
      ctx.fill();
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
      overlay.hidden = false;
      overlay.querySelector("strong").textContent = "游戏结束";
      overlay.querySelector("span").textContent = "本局分数 " + Math.floor(score) + "，点击重新开始";
      overlay.querySelector("button").textContent = "再跑一次";
    }

    root.querySelector("[data-start]").addEventListener("click", reset);
    root.querySelector("[data-restart]").addEventListener("click", reset);
    root.querySelector("[data-jump]").addEventListener("click", jump);
    canvas.addEventListener("pointerdown", jump);

    window.addEventListener("keydown", (event) => {
      if (event.code === "Space" || event.key === "ArrowUp" || event.key === "w" || event.key === "W") {
        event.preventDefault();
        jump();
      }
    });

    draw();
  })();
</script>
