# 🎨 Vibe Coding Projects

> A collection of fun, single-file browser mini-apps built for the vibe.  
> No frameworks. No build steps. Just vibes.

## 🌐 Live

👉 **[bb123n4.github.io/vibeCoding](https://bb123n4.github.io/vibeCoding/)**

---

## 🗂 Projects

### 🎮 游戏

| | Project | Description |
|--|---------|-------------|
| 🔢 | [2048.html](2048.html) | Classic 2048 tile-merging game with auto-play AI |
| 🏓 | [breakout.html](breakout.html) | 无尽弹球 — endless breakout with increasing speed and shrinking paddle |
| 💣 | [minesweeper.html](minesweeper.html) | 扫雷 — classic minesweeper with 3 difficulties, timer, and safe first click |
| 🎵 | [rhythm.html](rhythm.html) | 节奏打击 — rhythm click game with approach circles, combos, and 3 difficulties |
| 🎰 | [slots.html](slots.html) | 老虎机 — neon slot machine with animated reels, jackpots, and Web Audio |
| 📖 | [adventure.html](adventure.html) | 深夜书店 — text adventure with branching story and 4 endings |
| 🎣 | [fishing.html](fishing.html) | 钓鱼 — moonlit fishing with 6 fish species, bite timing, and catch log |
| 🏰 | [tower.html](tower.html) | 塔防 — tower defense with 4 turret types and endless enemy waves |
| 👾 | [invaders.html](invaders.html) | 太空侵略者 — classic arcade space invaders with UFO and shield walls |
| 🍉 | [merge.html](merge.html) | 合成水果 — physics-based Suika-style fruit merge game |
| ⚗️ | [alchemy.html](alchemy.html) | 元素炼成 — element discovery game, 100 elements, dual-panel 图鉴 |
| 🍵 | [teahouse.html](teahouse.html) | 小茶馆 — idle teahouse incremental game with 8 teas and staff upgrades |

### 🔮 玄学

| | Project | Description |
|--|---------|-------------|
| 🏮 | [luckToday.html](luckToday.html) | 每日灵签 — daily fortune oracle |
| 🃏 | [tarot.html](tarot.html) | 每日塔罗 — three-card tarot spread: love / career / money |
| ⚡ | [energy.html](energy.html) | 今日能量值 — daily 8-dimension energy report with animated radar chart |
| ✦ | [namescore.html](namescore.html) | 命名打分器 — name appraisal scoring 字形, 音律, 五行 & 数理 |
| ☯ | [iching.html](iching.html) | 周易占卦 — I Ching oracle with animated coin throws and all 64 hexagrams |

### 🎨 创作

| | Project | Description |
|--|---------|-------------|
| 🎹 | [instruments.html](instruments.html) | Software Instruments — virtual piano & guitar with auto-play |
| 🖌️ | [pixel.html](pixel.html) | Pixel Board — pixel art canvas with brushes, fill, and PNG export |
| 🎆 | [fireworks.html](fireworks.html) | 烟花 — interactive fireworks over a city skyline, 5 burst types |
| 🏖️ | [sand.html](sand.html) | 沙粒模拟 — falling sand simulation: sand, water, fire, oil & wood |
| 🔭 | [fractal.html](fractal.html) | 分形浏览器 — Mandelbrot browser with smooth coloring, 5 palettes, zoom & pan |
| 🖋️ | [ink.html](ink.html) | 流体墨水 — real-time Navier-Stokes fluid ink on paper, 5 ink colors |

---

## 🚀 Run locally

```bash
# Just open any file directly — no build, no dependencies
open index.html
# or serve with any static server
npx serve .
```

---

## 🏗 Architecture

Every game is a **single `.html` file** with inline CSS and JS — no bundler, no npm.

- **Canvas + DPR scaling** — all games use `devicePixelRatio`-aware canvas with responsive CSS (`min(100vw, calc(100vh * W/H))`)
- **No JS framework** — vanilla Canvas 2D API throughout
- **External deps** — only `merge.html` uses matter.js (CDN); everything else is zero-dependency
- **Persistence** — `localStorage` for high scores and game saves, versioned keys (`game_v1`)
- **Font** — Quicksand (index page), system sans-serif / STKaiti in games

See [`CLAUDE.md`](CLAUDE.md) for development patterns and conventions.
