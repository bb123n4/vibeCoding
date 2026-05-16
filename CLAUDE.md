# vibeCoding — CLAUDE.md

## 项目概述

单文件 HTML5 Canvas 迷你游戏集合，无框架、无构建步骤。  
每个游戏是独立的 `.html` 文件，`index.html` 是首页展示台。

**部署**：直接 `git push`，静态托管自动更新。  
**字体**：Quicksand（index.html 用），游戏页面用 system sans-serif / STKaiti。  
**favicon**：`favicon.svg`（所有页面共用，已存在，直接引用）。

---

## 每个游戏文件的固定结构

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<link rel="icon" href="favicon.svg" type="image/svg+xml">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>游戏名</title>
<style>
*{box-sizing:border-box;margin:0;padding:0}
body{background:#背景色;display:flex;align-items:center;justify-content:center;min-height:100vh;overflow:hidden}
/* 响应式 canvas — 保持宽高比填满视口 */
canvas{display:block;width:min(100vw,calc(100vh * W / H));height:min(100vh,calc(100vw * H / W))}
</style>
</head>
<body>
<canvas id="c"></canvas>
<script>
const W=1200, H=700;  // 逻辑分辨率，自定义
const dpr=Math.min(window.devicePixelRatio||1,2);
const cv=document.getElementById('c');
cv.width=W*dpr; cv.height=H*dpr;
// 不要写 cv.style.width/height！CSS 负责响应式
const ctx=cv.getContext('2d');
ctx.scale(dpr,dpr);
// ... 游戏代码 ...
</script>
</body>
</html>
```

**关键点**：
- `cv.style.width/height` **不能写**，会覆盖 CSS 响应式规则
- DPR 上限 2，防止 4K 屏过度消耗
- 所有游戏坐标基于逻辑分辨率 (W×H)，canvas 物理像素是 W*dpr × H*dpr

---

## 坐标映射（必须这样写）

canvas 在 CSS 中被缩放显示，点击坐标需要还原到逻辑空间：

```javascript
cv.addEventListener('click', e => {
  const r = cv.getBoundingClientRect();
  const lx = (e.clientX - r.left)  * W / r.width;
  const ly = (e.clientY - r.top)   * H / r.height;
  onTap(lx, ly);
});

// touch 同理
cv.addEventListener('touchstart', e => {
  e.preventDefault();
  const r = cv.getBoundingClientRect();
  const t = e.touches[0];
  onTap((t.clientX - r.left) * W / r.width, (t.clientY - r.top) * H / r.height);
}, {passive: false});
```

---

## 游戏循环模板

```javascript
let last = 0;
function loop(ts) {
  const dt = Math.min((ts - last) / 1000, 0.05); // 秒，上限防卡帧
  last = ts;
  update(dt);
  draw();
  requestAnimationFrame(loop);
}
requestAnimationFrame(ts => { last = ts; requestAnimationFrame(loop); });
```

---

## LocalStorage 存档模式

```javascript
const SAVE_KEY = 'gamename_v1'; // 版本号防旧数据污染

function save() {
  try { localStorage.setItem(SAVE_KEY, JSON.stringify({ /* state */ })); } catch(e) {}
}
function load() {
  try {
    const d = JSON.parse(localStorage.getItem(SAVE_KEY));
    if (d) { /* 恢复状态 */ }
  } catch(e) {}
}
window.addEventListener('beforeunload', save);
load(); // 启动时加载
```

---

## 粒子系统模板

```javascript
let particles = [];
function spawnParticle(x, y, text) {
  particles.push({ x, y, vx: (Math.random()-0.5)*2, vy: -2-Math.random()*2,
                   life: 1, text, color: '#FFD700' });
}
function updateParticles(dt) {
  particles = particles.filter(p => p.life > 0);
  for (const p of particles) {
    p.x += p.vx; p.y += p.vy; p.vy += 0.08; p.life -= dt * 0.8;
    ctx.globalAlpha = p.life;
    ctx.fillStyle = p.color;
    ctx.font = '16px sans-serif';
    ctx.fillText(p.text, p.x, p.y);
    ctx.globalAlpha = 1;
  }
}
```

---

## index.html 新增游戏卡片 — 步骤清单

每次新增游戏必须改 4 处：

### 1. 添加 CSS（在 Alchemy 样式块后面）
```css
/* ════  新游戏名  ════ */
.p-newgame { background:#背景色; position:relative; overflow:hidden; }
.p-newgame canvas { display:block; width:100%; height:100%; }
```

### 2. 在对应分类 `.grid` 里添加卡片 HTML
```html
<a class="card" href="newgame.html">
  <div class="preview p-newgame"><canvas id="newCanvas"></canvas></div>
  <div class="info">
    <div class="info-left">
      <div class="card-name">游戏名</div>
      <div class="card-desc">关键词 · 关键词 · 关键词</div>
    </div>
    <div class="card-arrow">→</div>
  </div>
</a>
```
- 每行 4 列，新增卡片时检查是否需要删占位符 `card-ph` 或补占位符
- 占位符：`<div class="card-ph"><div class="ph-icon">✦</div><div class="ph-text">新的在路上</div></div>`

### 3. 更新 ALL_APPS 数组
```javascript
const ALL_APPS = [ ..., 'newgame.html' ];
```

### 4. 添加 mini canvas 绘制函数（`</script>` 前）
```javascript
// ── NewGame mini ──
(function(){
  const cv = document.getElementById('newCanvas');
  if(!cv) return;
  const par = cv.parentElement;
  const W = par.offsetWidth||200, H = par.offsetHeight||160;
  cv.width = W; cv.height = H;
  const ctx = cv.getContext('2d');
  // 静态缩略图，不需要 rAF，直接画
  ctx.fillStyle = '#背景色'; ctx.fillRect(0,0,W,H);
  // ... 绘制游戏特色元素 ...
})();
```

---

## 现有游戏一览

| 文件 | 游戏 | 分类 | 尺寸 | 特殊依赖 |
|------|------|------|------|---------|
| `2048.html` | 2048 | 游戏 | 自适应 | — |
| `breakout.html` | 无尽弹球 | 游戏 | — | — |
| `minesweeper.html` | 扫雷 | 游戏 | — | — |
| `rhythm.html` | 节奏打击 | 游戏 | — | — |
| `slots.html` | 老虎机 | 游戏 | — | — |
| `adventure.html` | 深夜书店 | 游戏 | — | — |
| `fishing.html` | 钓鱼 | 游戏 | — | — |
| `tower.html` | 塔防 | 游戏 | — | — |
| `invaders.html` | 太空侵略者 | 游戏 | — | — |
| `merge.html` | 合成水果 | 游戏 | 380×580 | matter.js (CDN) |
| `alchemy.html` | 元素炼成 | 游戏 | 1120×720 | — |
| `teahouse.html` | 小茶馆 | 游戏 | 1200×700 | — |
| `luckToday.html` | Your Luck Today | 玄学 | — | — |
| `tarot.html` | Tarot | 玄学 | — | — |
| `energy.html` | 今日能量值 | 玄学 | — | — |
| `namescore.html` | 命名打分器 | 玄学 | — | — |
| `iching.html` | 周易占卦 | 玄学 | — | — |
| `instruments.html` | Software Instruments | 创作 | — | — |
| `pixel.html` | Pixel Board | 创作 | — | — |
| `fireworks.html` | 烟花 | 创作 | — | — |
| `sand.html` | 沙粒模拟 | 创作 | — | — |
| `fractal.html` | 分形浏览器 | 创作 | — | — |
| `ink.html` | 流体墨水 | 创作 | — | — |

---

## 视觉风格速查

| 游戏 | 背景色 | 主色调风格 |
|------|--------|-----------|
| 元素炼成 | `#EDE8DE` | 米色宣纸·暖褐墨色文字 `rgba(40,28,16,x)` |
| 小茶馆 | `#F5EDD8` | 暖米宣纸·茶褐色系 |
| 合成水果 | `#FFFFFF` 容器 | 白底·彩色描边·简洁 |
| 塔防/侵略者 | `#0f1520` / `#000` | 暗色科幻·发光效果 |
| 钓鱼 | `#0c0820` | 深夜水墨·月色氛围 |
| 玄学类 | 各异深色 | 金色/紫色·神秘感 |
| 创作类 | 主题相关 | 工具类偏亮，烟花/沙粒暗底 |

---

## 常见陷阱

1. **cv.style.width/height**：绝对不能在 JS 里设置，会破坏 CSS 响应式缩放
2. **坐标不还原**：点击后直接用 `e.clientX` 而不除以缩放比例，导致在非 1:1 显示时点击偏移
3. **recipe 冲突**（alchemy.html）：新增合成配方前检查 `rec(a,b,x)` 中 `[a,b].sort().join('+')` 是否已存在
4. **SAVE_KEY 版本**：改变存档结构时递增版本号，防止旧数据 parse 出错
5. **DPR 不限制**：不加 `Math.min(..., 2)` 会在 Retina 屏上把 canvas 放大 3x，帧率暴跌
6. **index 占位符数量**：每个分类 grid 是 4 列，卡片 + 占位符总数必须是 4 的倍数（不强制，但好看）

---

## Git 工作流

```bash
# 开发完一个 feature 后
git add index.html newgame.html   # 明确列出文件，避免带入临时文件
git commit -m "feat: 一句话描述"
git push
```

worktree 路径（本地预览用）：  
`.claude/worktrees/zen-goldstine-ec3061/`  
预览时把文件 cp 过去：`cp newgame.html .claude/worktrees/zen-goldstine-ec3061/`

---

## 快速新建游戏 Checklist

- [ ] 创建 `newgame.html`，套用上面的 HTML 结构模板
- [ ] 确定 W×H（宽屏交互推荐 1200×700，竖屏/手机推荐 380×600）
- [ ] 实现 update(dt) + draw() + rAF 循环
- [ ] localStorage 存档（SAVE_KEY 用 `gamename_v1`）
- [ ] 触摸支持（touchstart/touchmove/touchend）
- [ ] index.html：CSS + 卡片 HTML + ALL_APPS + mini canvas 函数
- [ ] git add + commit + push
