# 小游戏

这里放一些可以直接在网页里玩的前端小游戏。

<div class="game-hub">
  <a class="game-hub__card game-hub__card--snake" href="./snake/">
    <span>01</span>
    <h2>贪吃蛇</h2>
    <p>方向键或 WASD 控制移动，吃到能量点会变长，撞墙或撞到自己就结束。</p>
  </a>

  <a class="game-hub__card game-hub__card--2048" href="./2048/">
    <span>02</span>
    <h2>2048</h2>
    <p>滑动合并相同数字，努力拼出 2048。支持键盘和按钮操作。</p>
  </a>

  <a class="game-hub__card game-hub__card--minesweeper" href="./minesweeper/">
    <span>03</span>
    <h2>扫雷</h2>
    <p>左键翻开格子，右键插旗。第一步一定安全，考验推理和一点点胆量。</p>
  </a>

  <a class="game-hub__card game-hub__card--dino" href="./dino-runner/">
    <span>04</span>
    <h2>小恐龙跑酷</h2>
    <p>空格或点击跳跃，躲开障碍物，坚持越久速度越快，分数越高。</p>
  </a>
</div>

<style>
  .game-hub {
    display: grid;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    gap: 18px;
    margin-top: 24px;
  }

  .game-hub__card {
    min-height: 240px;
    padding: 26px;
    overflow: hidden;
    position: relative;
    border-radius: 18px;
    color: #fff !important;
    text-decoration: none !important;
    background: #101827;
    box-shadow: 0 18px 50px rgba(15, 23, 42, 0.22);
    transition: transform 180ms ease, box-shadow 180ms ease;
  }

  .game-hub__card:hover {
    transform: translateY(-6px);
    box-shadow: 0 26px 70px rgba(15, 23, 42, 0.34);
  }

  .game-hub__card::before {
    content: "";
    position: absolute;
    inset: auto -20% -30% auto;
    width: 220px;
    height: 220px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.16);
  }

  .game-hub__card--snake {
    background: linear-gradient(135deg, #0f766e, #22c55e);
  }

  .game-hub__card--2048 {
    background: linear-gradient(135deg, #7c2d12, #f97316);
  }

  .game-hub__card--minesweeper {
    background: linear-gradient(135deg, #1e3a8a, #38bdf8);
  }

  .game-hub__card--dino {
    background: linear-gradient(135deg, #3f3f46, #a3e635);
  }

  .game-hub__card span {
    opacity: 0.76;
    font-weight: 800;
  }

  .game-hub__card h2 {
    margin: 48px 0 12px;
    color: #fff;
    font-size: 32px;
  }

  .game-hub__card p {
    max-width: 420px;
    margin: 0;
    color: rgba(255, 255, 255, 0.86);
  }

  @media (max-width: 760px) {
    .game-hub {
      grid-template-columns: 1fr;
    }
  }
</style>
