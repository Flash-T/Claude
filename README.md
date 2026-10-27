# Claude skills

## painted-animation（手绘水彩动画 / MV）

来源：[Joker-chichi/claude-mv-skill](https://github.com/Joker-chichi/claude-mv-skill)（上游为 tuzhechen2005/opus-video-skills，基于 JohnHeibel/ClaudeAnimationBase，MIT 许可）。
已作为项目级 skill 安装在 `.claude/skills/painted-animation/`，在本仓库中打开 Claude Code 即自动可用。

### 它做什么

用纯代码生成"手绘水彩 + 墨线"风格的动画短片 / 音乐 MV / 歌词视频，不使用任何图像或视频生成模型：

- 每一帧都是时间 `t` 的纯函数，用 p5.js + p5.brush 绘制 1920×1080 画面；
- `render.mjs` 通过 headless Chrome（puppeteer-core）渲染关键帧拼图、逐帧条带、局部裁剪，或并行渲染全部帧；
- ffmpeg 编码 MP4，可混入音频；
- `scripts/beat_grid.py` 测 BPM 与节拍相位，并把 LRC 每行歌词对到节拍网格上；
- `src/karaoke.js` 提供逐字卡拉 OK 字幕。

### 工作流（SKILL.md 规定）

0. **搭项目**：`bash .claude/skills/painted-animation/scripts/new_project.sh <目录>`，然后通读项目里的 `ANIMATION_GUIDE.md`。
   做 MV 时必须先要**音频 + LRC 歌词文件**，并只问**一个**关于故事/情绪/意象/禁忌的自然语言问题（可留空）。
1. **先写分镜** `STORYBOARD.md`（logline、世界与色彩弧、母题、情绪弧、每个镜头的 reads），给用户确认。
2. **逐镜头实现**：`src/config.js` 设时长/BPM/音频，每章一个 IIFE 场景文件，以 `shots([[t0, fn], ...])` 结尾。
3. **渲染预览并亲眼检查**：每个镜头至少一张 contact sheet、关键动作一条 strip、关键表情一张 crop。
4. **最终渲染前必须明确询问"本地渲染还是云端渲染"**，没有明确答复不跑完整渲染；选本地则只给出本地命令。

### 核心规则

只用 `paint()` / `inkLine()` 手绘笔触、纯 2D；画面里不出现文字（歌词卡拉 OK 除外）；每个镜头都有事件；给观众留够理解时间；角色始终"活着"并踩拍；每个接缝都要转场；整部片子是一个整体，结尾呼应开头。

### 依赖

Node.js、Chrome/Chromium、ffmpeg、Python 3 + numpy（仅测速用）。本云环境已有 node、ffmpeg、numpy 和 `/opt/pw-browsers` 下的 Chromium；首次搭项目时 `npm install` 会安装 p5、p5.brush、puppeteer-core。

### 相对上游的改动

- `scripts/new_project.sh`：`rsync` 换成 `cp -a`（本环境没有 rsync），Chrome 检测加入 `$PLAYWRIGHT_BROWSERS_PATH`；
- `template/render.mjs`：自动在 `$PLAYWRIGHT_BROWSERS_PATH` 中查找 Chromium（本环境为 `/opt/pw-browsers`）；
- `SKILL.md`：脚手架路径改为项目内的 `.claude/skills/painted-animation/`。
