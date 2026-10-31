# Design —「雾屿声场」

云音乐的视觉世界：**浅色磨砂玻璃曲库**悬浮于暖调纸面之上，**深墨绿播放台**承载夜间聆听。一个界面两种质地，音乐是唯一主角。

## 色彩策略

Restrained（克制）：中性色取自世界本身 + 一个强调色。浅区暖白纸面配深青绿强调色；深区墨绿舞台配薄荷强调色。两区各自闭环，互不混色。

### 浅色区（曲库 / 导航 / 抽屉 / 通知底）

| Token | 值 | 用途 |
|---|---|---|
| `--bg` | `#EEF0EB` | 页面底色（暖调纸面），body 上叠加两处低饱和径向色晕（青绿 / 雾灰） |
| `--surface` | `rgba(255,255,255,.66)` | 磨砂玻璃卡面，配 `backdrop-filter: blur(20px) saturate(1.5)` |
| `--surface-solid` | `#FFFFFF` | 输入框、激活 tab 等实底 |
| `--ink` | `#17211C` | 主文字 |
| `--muted` | `#5C6B61` | 次要文字 |
| `--line` | `rgba(23,33,28,.09)` | 发丝分隔线 |
| `--accent` | `#0E7A5B` | 唯一强调色：主按钮实底、聚焦态、图标着色 |
| `--accent-hover` | `#0B664D` | hover 深化；同时作 accent-soft 底上的文字色（≥4.5:1） |
| `--accent-soft` | `#DDEEE6` | 选中/hover 浅底（tab、徽标、收藏态、当前行） |
| `--danger` | `#B4453A` | 错误/删除 |

### 深色舞台（播放台 / 全屏 / 抽屉内）

| Token | 值 | 用途 |
|---|---|---|
| `--stage` | `#10221A` | 舞台底（实色，全屏态仅叠加极轻中性提亮，不用彩色径向晕） |
| `--stage-2` | `#16291F` | 台内控件/歌词卡底 |
| `--stage-line` | `rgba(255,255,255,.12)` | 台内分隔线/描边 |
| `--stage-ink` | `#EDF4EE` | 台内主文字 |
| `--stage-muted` | `#9DB4A6` | 台内次要文字 |
| `--mint` | `#A7DFB8` | 深色区强调色：播放键实底、进度条、音量 accent、focus 环、歌词高亮 |

### 渐变纪律

**按钮一律实色**（主按钮 accent 实底白字，播放键 mint 实底），无例外。
渐变只允许出现在三处：品牌图标块（accent→mint 135°）、导航栏磨砂玻璃的轻微白向渐变、focus 环的色调。禁止渐变文字、禁止彩色发光阴影（dark-glow）、禁止大面积彩色径向晕。

## 形与影

- 圆角：卡片 `--radius-card: 18px`，控件 `--radius-ctl: 12px`，徽标/导航链接用 pill（999px）。
- 阴影（柔和弥散、带偏移的中性影）：
  - `--shadow-sm: 0 1px 2px rgba(23,33,28,.05)`
  - `--shadow-md: 0 6px 20px -6px rgba(23,33,28,.12), 0 2px 6px -2px rgba(23,33,28,.06)`
  - `--shadow-lg: 0 16px 38px -12px rgba(23,33,28,.20)`
- 卡片悬停：`translateY(-2px)` + shadow-md→lg，220ms `--ease: cubic-bezier(.22,1,.36,1)`。
- 深色区用中性黑影（`rgba(0,0,0,.55)`），不用彩色光晕。

## 图标

内联 SVG，`viewBox 0 0 24 24`、stroke 1.8、round caps，Lucide 风格统一笔触。JS 侧 `ICONS` 映射 + `setIcon(el,name)`，需要切换图标的控件用 `<span class="ic">` 容器（收藏 heart/heart-filled、播放 play/pause、音量 volume-x/1/2、加载 loader 带 `.spin` 动画）。无图标字体、无 emoji。

## 组件要点

- **导航栏**：磨砂玻璃（半透明白 + blur 20px）+ 发丝底线；品牌块为唯一渐变元素。
- **主卡（曲库）**：磨砂玻璃 + shadow-md；抽屉内复用时还原为透明扁平（`.library-drawer .content-section`）。
- **Tabs**：磨砂胶囊容器，激活项白底 + shadow-sm；默认视图为「收藏」。
- **播放模式**：图标循环按钮（顺序→列表循环→单曲循环→随机），点击切换、两处同步；深区幽灵圆钮、浅区描边方钮。图标 `sequence/repeat/repeat-1/shuffle`。
- **下拉框**：自绘弹层组件 `buildUiSelect`（原生 `<select hidden>` 作数据层，保留 `.value`/`change`）；按钮沿用原 class 样式 + chevron 图标，弹出面板为圆角玻璃卡（浅区 surface-solid+shadow-lg / 深区 stage-2+mint 选中），option 行带 check 标记；支持方向键/Home/End/Esc/外点关闭；音质面板在舞台底部向上弹出。
- **音量滑块**：自定义 WebKit/Moz 轨道（4px 半透明轨 + 14px mint 圆钮，hover 放大）。
- **通知**：磨砂玻璃 + 类型色变体（success/error/warning/info）。
- **浏览器面**：`::selection`、滚动条（细、浅/深区各自配色）、`caret-color`、focus-visible 3px 环（浅区 accent / 深区 mint）均出自令牌。

## 排版

系统中文栈：`-apple-system, "PingFang SC", "Microsoft YaHei", sans-serif`。标题 650–750 字重、负字距；功能/元信息文字 ≥12px（11px 仅保留在极端紧凑的徽标场景以外）；tabular-nums 用于序号、时长、徽标计数。无 eyebrow/kicker。

## 动效

缓动统一 `cubic-bezier(.22,1,.36,1)`，时长 180–240ms（歌词跟随 420ms）。保留既有签名动效：封面旋转（24s linear）、抽屉滑入、歌词 mask 渐隐。`prefers-reduced-motion` 全局禁用动画与过渡。

## 布局

一屏式：文档不滚动，仅列表区滚动。左曲库右舞台 grid，全屏态 `.is-expanded` 三行结构（工具栏/内容/底栏），抽屉 `library-drawer` 右侧滑入。断点 1500/1150/1140/900/760/640/360px + 高度 560px/600px 组合，全部保留。

## 无障碍

skip-link、aria-label/pressed/live、focus-visible 3px 环、44px 触控目标、文字对比 ≥4.5:1（accent-soft 底上的文字用 accent-hover）、`prefers-reduced-motion`、`color-scheme` 分区声明。
