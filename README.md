# 班级工具箱 · 生存手册

> 青，取之于蓝，而青于蓝。——《荀子·劝学》

一个高三班级为自己做的日常仪式工具：**练字提醒、小组讨论、名言生成、高考倒计时、随机点名、作文赏析、大字报、小游戏、好词积累、反应测试**。

线上地址：https://sequoia-cyber.github.io/

两套视觉风格可随时切换：**生存**（Minecraft 像素风）/ **创造**（编辑排印风）。

---

## 设计

- **双风格体系**：生存模式为 Minecraft 视觉语言（体素天空、方块云、物品栏格子、像素字体）；创造模式为编辑排印风
- **原生 WebGL2 背景**：双场景着色器 —— 体素（MC 天空 / 方块云 / 远景方块地形）与墨流（域扭曲 FBM）；无 WebGL 时自动降级
- **三套主题**：夜观 / 宣纸 / 苔痕，与两种风格正交组合（6 种观感），全部由 CSS 变量驱动
- **动效语言**：Framer Motion 统一编排 —— 逐字揭示、滚动入场、页面转场、磁吸按钮、数字滚动
- **字体**：创造模式用系统宋体/黑体；生存模式用 Fusion Pixel 缝合像素字体（已子集化至 75KB，按需懒加载）
- **无障碍**：`prefers-reduced-motion` 下所有动效降级为直接呈现；所有装饰图形为内联 SVG，不依赖符号字体

## 技术栈

| | |
|---|---|
| 构建 | Vite 5 + TypeScript |
| 框架 | React 18 + React Router 6 |
| 样式 | Tailwind CSS 3（语义化 CSS 变量色板） |
| 动效 | Framer Motion 11 |
| 图形 | 原生 WebGL2（无 3D 库依赖） |
| 部署 | GitHub Pages（静态产物） |

## 目录

```
src/
├── components/       # InkBackground(WebGL) · Chrome(导航/目录/页脚/光标) · Motion(动效原语) · UI(通用构件) · Icons(SVG 图标集)
│   └── games/        # Minesweeper · Tetris · Pvz
├── data/             # students(名单) · quotes(名言) · words(雅词)
├── lib/              # schedule(时间口径) · nav(导航元数据) · hooks · theme · toast
├── pages/            # 12 个路由页面
└── styles/index.css  # 设计令牌与三套主题
```

## 常用修改

| 想改什么 | 改哪里 |
|---|---|
| 班级名单 / 权重 / 彩蛋 | `src/data/students.ts` |
| 名言库 | `src/data/quotes.ts` |
| 好词库 | `src/data/words.ts` |
| 练字 / 讨论时间 | `src/lib/schedule.ts` → `PRACTICE_WINDOW` / `DISCUSSION_WINDOW` |
| 高考年份与日期 | `src/lib/schedule.ts` → `GAOKAO_START` / `GAOKAO_END` |
| 导航条目 | `src/lib/nav.ts` |
| 主题配色 | `src/styles/index.css` → `[data-theme='…']` |

## 本地开发

```bash
npm install
npm run dev        # http://127.0.0.1:5173
npm run build      # 产物在 dist/
npm run preview    # 预览生产构建
```

## 质量校验

```bash
node scripts/verify.mjs    # 逐页渲染校验（DOM 状态 / 控制台报错 / 截图）
node scripts/interact.mjs  # 交互回归：22 项功能断言
```

两个脚本需要本机存在 Chrome，可用 `playwright-core` 驱动。

## 部署

产物为纯静态文件，构建后推送到仓库 `main` 分支根目录即可：

```bash
npm run build
GITHUB_TOKEN=xxx node scripts/deploy.mjs
```

`scripts/deploy.mjs` 通过 GitHub REST API 上传 `dist/`、清理仓库中的历史文件，并开启 Pages。
（走 API 而非 `git push` 是因为部分网络环境会拦截 git 协议端口。）

### SPA 深链接

GitHub Pages 不支持前端路由回退，因此：

- `public/404.html` 会把原始路径存入 `sessionStorage` 后跳回首页
- `src/main.tsx` 取回该路径并交给 React Router
- 旧的多页面地址（`/practice.html`、`/picker.html` …）会被自动映射到新路由，**旧链接不会失效**

## 许可

供班级内部使用。
