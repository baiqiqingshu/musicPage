# 前端重设计实施文档 —「雾屿声场」

日期：2026-10-31 ｜ 目标文件：`music.html`（单文件应用）｜ 模式：Operate（任务型界面）
状态：✅ 已实施。落地后的持久设计记录见根目录 `DESIGN.md`（以代码为准）。

## 背景

单文件音乐播放器：左侧曲库（待播/搜索/歌单/收藏），右侧深色播放台（封面、歌词、控制、音质、音量、下载），支持网页全屏/屏幕全屏与歌曲列表抽屉。本次重设计替换视觉世界，保留全部功能、布局骨架与无障碍基线。

设计依据：`.agents/skills/impeccable`（craft-floor 质量底线）+ `.agents/skills/ui-ux-pro-max`（UX 规范库）。用户 brief 已锁定美学方向，跳过概念抽签。

## 设计方向

「雾屿声场」：浅色磨砂玻璃曲库悬浮于安静的暖调纸面，右侧播放台为深墨绿「夜间聆听」舞台。一个界面两种质地，音乐为唯一主角。

## 视觉约束（用户绑定）

- 柔和弥散阴影（带偏移的软阴影，非光晕）；卡片悬停上浮 -2px + 阴影加深。
- **按钮一律实色，不用渐变。** 渐变仅允许出现在：品牌图标块、导航栏磨砂玻璃的细微色泽、聚焦环色调。
- 磨砂玻璃：`backdrop-filter: blur() saturate()` + 半透明白，用于导航栏、曲库卡片、抽屉、通知。
- 圆角卡片 16–18px、控件 10–12px；中文文案；内联 SVG 图标（移除 Font Awesome CDN）。
- 下拉框、抽屉、通知、滚动条、选区、光标等"隐藏组件"同样纳入设计。
- 减少 AI 感：不用紫蓝大渐变、不用渐变文字、不用 emoji/图标字体。

## 设计令牌（Tokens）

```css
/* 浅色区（曲库） */
--bg: #EEF0EB;                        /* 暖调纸面 */
--surface: rgba(255,255,255,.66);     /* 磨砂玻璃卡 */
--surface-solid: #FFFFFF;
--ink: #17211C;
--muted: #5C6B61;
--line: rgba(23,33,28,.09);
--accent: #0E7A5B;                    /* 深青绿，唯一强调色 */
--accent-hover: #0B664D;
--accent-soft: #DDEEE6;
--danger: #B4453A;

/* 深色舞台（播放台） */
--stage: #10221A;
--stage-2: #16291F;                    /* 台面内控件底 */
--stage-line: rgba(255,255,255,.12);
--stage-ink: #EDF4EE;
--stage-muted: #9DB4A6;
--mint: #A7DFB8;                       /* 深色区强调色（播放键、进度、焦点） */

/* 形与影 */
--radius-card: 18px; --radius-ctl: 12px;
--shadow-sm: 0 1px 2px rgba(23,33,28,.05);
--shadow-md: 0 6px 20px -6px rgba(23,33,28,.12), 0 2px 6px -2px rgba(23,33,28,.06);
--shadow-lg: 0 16px 38px -12px rgba(23,33,28,.20);
--ease: cubic-bezier(.22,1,.36,1);     /* 已有，沿用 */
```

- 背景氛围：body 上用 1–2 个极淡的径向色晕（青绿/雾灰），饱和度低，不抢内容。
- 聚焦：`:focus-visible` 用 3px 外描边（浅区 accent 色 / 深区 mint 色），保留无障碍。
- 选区 `::selection`、滚动条（细、随区域配色）、`caret-color` 均出自令牌。

## 组件规范

| 组件 | 规范 |
|---|---|
| 导航栏 | 磨砂玻璃（半透明白 + blur），1px 发丝底线；品牌块为唯一渐变元素（青绿→薄荷对角）；导航链接 pill 形 hover |
| 主卡（曲库） | 磨砂玻璃卡，shadow-md；悬停时 translateY(-2px) + shadow-lg |
| 按钮 | 主按钮 = accent 实色白字；次按钮 = 描边/幽灵；**无渐变**；44px 触控高 |
| Tabs | 磨砂胶囊容器；激活项 = 实色白底 + 轻投影；默认激活「收藏」 |
| 输入框/下拉 | 输入框实底圆角 + 聚焦环；下拉为自绘弹层 `buildUiSelect`（原生 select 隐藏保留数据层，圆角玻璃面板 + check 标记 + 键盘导航）；播放模式为图标循环按钮 |
| 歌曲行 | 圆角行；hover 浅底；当前播放行 accent-soft 底；操作钮 hover 浮出 accent-soft 底 |
| 播放台 | 深墨绿实底（可保留极轻径向提亮作品牌区质感）；控件玻璃化；播放键 mint 实色大圆钮 |
| 抽屉/通知 | 磨砂玻璃 + 阴影；通知保留 type 色变体（success/error/warning/info） |
| 空状态/加载 | 图形留白 + 中文短文案；加载用 SVG spinner（CSS 旋转） |

## 图标系统

内联 SVG，24 viewBox，stroke 1.8，round caps（Lucide 风格，统一笔触）。JS 中建立 `ICONS` 映射 + `setIcon(el,name)` 助手，替换：

- HTML 静态图标：brand(wave-square→audio-lines)、layer-group、headphones、align-left、music、search、list-ul、heart、arrow-right、lock、expand/compress、window-maximize、step-backward/forward、play/pause、sliders、volume、download、file-lines、chevron-right、quote-left、spinner。
- JS 动态：`updateFavoriteButton`/`refreshFavorites`（heart 填充切换）、`showListMessage`（spinner/music）、`displaySearchResults` action()（heart/download/file-text）、`updatePlayButton`（play/pause）、音量三态（mute/down/up）。
- 删除 Font Awesome `<link>`；`<i>` 标签可保留作容器或改为 `<span class="ic">`。

## 功能改动

1. **默认进入收藏页**：`activeTab = 'favorites'`；HTML 中「收藏」tab 按钮与 `favoritesTab` 面板改为 active + `aria-pressed="true"`，「搜索」对应取消。搜索触发时仍 `switchTab('search')`（行为不变）。
2. 图标全量替换（见上）。
3. CSS 全量重写视觉属性；**保留**：grid 布局骨架、全屏 `.is-expanded`、抽屉、media query 结构、无障碍（skip-link、focus-visible、prefers-reduced-motion、aria）。

## 禁止项（craft-floor）

渐变文字、图标字体/emoji 图标、零偏移彩色光晕、装饰性玻璃、幽灵卡（1px 边 + 大阴影）、标题上方 eyebrow、硬偏移阴影、等宽字体装饰。

## 验收

- `music.html` 无 `fa-` 类名残留；无外部图标 CDN。
- 默认打开即「我的收藏」视图。
- 全部 JS 函数与 aria 属性保留；`impeccable detect` 无高危项。
- 桌面/移动断点下布局不破坏（一屏布局、抽屉、全屏可用）。
