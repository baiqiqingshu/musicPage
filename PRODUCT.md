# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Users

中文用户，在浏览器中寻找并收听在线音乐的普通听众。场景为桌面或移动端浏览器内的日常听歌：搜索歌曲、导入歌单、收藏喜爱的音乐、沉浸播放。（依据代码与文案推断，未访谈。）

## Product Purpose

「云音乐」是一个单文件在线音乐播放器前端（`music.html`），配合 `worker.js`（Cloudflare Worker）提供 API 代理。聚合多个音乐来源（网易云、QQ音乐、酷我、JOOX、酷狗、咪咕、Deezer、Spotify、Apple Music、YouTube Music、TIDAL、Qobuz、喜马拉雅），让用户在一个页面内完成搜索、试听、收藏、下载与歌词跟随。

## Positioning

免登录、零安装、单页即用的多源音乐聚合播放器；收藏保存在浏览器本地（localStorage），不依赖账户体系。

## Operating Context

- 主界面：左侧曲库（待播 / 搜索 / 歌单 / 收藏 四个视图），右侧深色播放台（封面、歌词、播放控制、音质、音量、下载）。
- 播放台支持「网页全屏」与「屏幕全屏」两种沉浸模式，全屏内可唤出歌曲列表抽屉。
- 播放模式：顺序 / 列表循环 / 单曲循环 / 随机（含随机历史回退）。
- 页面加载后默认进入网页全屏播放态（`enterFullscreenPlayback('page')`）；文档不整体滚动，仅列表区域滚动。
- API：`https://music-api.gdstudio.xyz/api.php`。

## Capabilities and Constraints

- 纯静态前端：单文件 `music.html`（内联 CSS + 原生 JS），无构建工具，无框架。
- 图标原依赖 Font Awesome CDN，本次改造改为内联 SVG（用户明确要求）。
- 可访问性基线已存在：skip-link、aria-label、focus-visible、prefers-reduced-motion、44px 触控目标——重设计必须保留。
- 多档响应式断点与全屏态适配（1500/1140/900/760/640/360px、高度 560px）必须保留或等价覆盖。
- 收藏与播放模式持久化于 localStorage（`musicPage.favorites.v1`、`musicPage.playbackMode.v1`）。

## Brand Commitments

- 名称：云音乐（Your Daily Soundtrack）。中文为第一语言，全站中文文案。
- 用户绑定的视觉约束（2026-10-31 重设计 brief）：
  - 现代质感：柔和弥散阴影、卡片悬停上浮、磨砂玻璃。
  - 渐变仅用于品牌区 / 导航局部 / 聚焦态；**按钮不使用渐变**，尽量减少「AI 感」。
  - 中文文案、SVG 图标、圆角卡片与按钮、现代配色。
  - 下拉框选项与抽屉、通知等隐藏组件同样纳入设计范围。

## Evidence on Hand

- `music.html`：现有全部 UI 与交互逻辑（产品事实的唯一权威）。
- `worker.js`：API 代理实现。
- `README.md`：项目说明。
- 无其他素材、图片或品牌资产；不得虚构数据与声明。

## Product Principles

1. 音乐优先：界面服务于「找歌—听歌—收藏」闭环，装饰让位于任务效率。
2. 一屏完成：桌面端曲库与播放台同屏可达；移动端以全屏播放为主场景。
3. 无负担：不登录、不注册，收藏即存即得。
4. 沉浸可选：需要专注时，全屏播放让界面退到音乐之后。

## Accessibility & Inclusion

沿用现有标准：对比度 ≥4.5:1、键盘可达、可见焦点、动态减弱偏好、44px 触控目标、中文文案无障碍标签。
