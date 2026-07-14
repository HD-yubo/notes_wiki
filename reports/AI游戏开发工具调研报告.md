# 用 AI 开发独立游戏 — 工具与框架调研报告（2025–2026）

> 调研范围：GitHub 开源项目为主，独立游戏/小制作场景  
> 调研方向：利用 LLM / AI Agent **辅助开发游戏**（编码、素材生成、关卡设计、游戏从提示词生成）  
> 调研时间：2026 年 7 月

---

## 一、当前项目状态

`indl_game` 项目目前完全空白：

- 无游戏引擎配置
- 无代码文件
- 无依赖声明
- 无设计文档
- 无 git 仓库

**结论**：AI 框架选型严重依赖引擎选型，两者必须一起决策。

---

## 二、AI 辅助开发工具全景

按工作方式分为四大流派：

### 流派 1：提示词 → 完整游戏（全自动生成管线）

给 AI 一段游戏描述，自动生成整个可运行项目。

| 项目 | ⭐ Stars | 目标引擎 | 底层模型 | 特点 |
|---|---|---|---|---|
| **godogen** | **4,552★** | Godot 4 / Bevy / Babylon.js | Claude Code + Codex + Gemini + Grok + Tripo3D | 主流第一。自设计架构 → 生成素材 → 写代码 → 运行截图 → 自修复。输出真实 Godot 工程 |
| **OpenGame** | **~3,000★** | Web（Phaser / three.js / Canvas） | 自带 GameCoder-27B 微调模型 | 多 Agent 管线：GDD → 脚手架 → 代码 → 素材 → 构建。支持交互式模式 |
| **GodotMaker** | 426★ | Godot | Claude Code + Codex + OpenCode | Python 管线，类似 godogen 但独立开发 |
| **game-creator** | 246★ | Web（Phaser / Three.js） | Claude Code 插件 | 从想法到部署到 Play.fun，含变现能力 |
| **UniGen** | 16★ | Unity 3D | 多 Agent（规划/编码/自动化/调试） | 有论文验证，开发时间减少 91.4%（140min → 12min） |
| **multi-agent-game-factory** | 12★ | 通用 | 6 Agent 管线 | Vue 3 前端 + SSE 实时流，实验性但架构新颖 |

**典型工作流**（以 godogen 为例）：

```
一句话描述
  ↓
AI 设计架构文档
  ↓
AI 生成素材（Gemini/Grok → 图像，Tripo3D → 3D）
  ↓
AI 编写全部代码（场景、脚本、资源）
  ↓
AI 运行引擎 → 截图 → 自检视觉缺陷 → 修复
  ↓
输出可运行 Godot 工程 + 15–20 秒证明视频
```

---

### 流派 2：MCP 服务器 → AI 直接操控引擎编辑器

这是 **2025–2026 年最大的变化**。MCP（Model Context Protocol）让 AI 编码助手能直接读写引擎编辑器的内部状态——创建场景、修改对象、运行游戏、截图检查。

#### Unity MCP 生态

| 项目 | 工具数 | 特色 |
|---|---|---|
| **Unity-MCP** (IvanMurzak) | 40+ 工具 | 最成熟。任何 C# 方法加一个属性即可变成 MCP 工具。MIT |
| **Funplay MCP** (FunplayAI) | 128 工具 | 含 Play Mode 自动化、输入模拟、游戏视图截图。MIT |
| **Locus** (r1n7aro) — **612★** | 独立 Rust Agent | 不是 MCP 插件，是独立二进制 Agent，驻留在 Unity Editor 内写 C# 代码、修改场景。增长最快（30 天 +90★） |
| **AkerMCP** (lorenzo-cambiaghi) | 全引擎统一 | **唯一同时驱动 Unity / Godot / Stride** 的 MCP |

#### Godot MCP 生态

| 项目 | 工具数 | 特色 |
|---|---|---|
| **godot-mcp** (UltraRabbit) | **149 工具** | 覆盖网络、3D/2D 渲染、UI 控件、音频、动画树、物理、运行时执行代码。深度最深 |
| **Godot-MCP** (IvanMurzak) | 同族 | 与 Unity-MCP 同族 |
| **Summer Engine** | 51 工具 | 基于 Godot 4 的 AI 原生引擎——**引擎设计目标就是让 AI 代理优先操控** |

#### Unreal MCP 生态

| 项目 | ⭐ | 特色 |
|---|---|---|
| **Unreal_mcp** (ChiR24) | 784★ | C++/TypeScript，通过 Automation Bridge 深度集成 UE5 |

#### 横向 MCP（跨引擎）

- **gamedev-all-in-one-mcp** (dmae97)：Roblox Studio + Unity + Unreal + Blender，67 工具
- **everything-game-dev** (mrcalderon3d)：Unity + Unreal + Godot + HTML，42 Agent / 51 命令 / 86 技能

**典型工作流**（以 Unity-MCP + Cursor 为例）：

```
开发者："在场景里创建一个红色立方体，加一个旋转脚本"
  ↓
Cursor (Claude Code) 通过 MCP 通知 Unity Editor
  ↓
Unity-MCP 在 Editor 中执行：CreatePrimitive → AddComponent → 写 C# 脚本
  ↓
截图返回给 AI 确认结果
  ↓
AI 发现代码有 bug → 通过 MCP 读取错误日志 → 修复 → 重新运行
```

---

### 流派 3：AI Studio 编排层——给 AI 会话建立团队结构

不是生成工具，而是为 AI 游戏开发会话建立**分工和流程**。

#### Claude Code Game Studios — **22,748★**（GitHub 上所有游戏 AI 项目中最高星）

- 把 Claude Code 变成一个完整的游戏开发工作室
- **49 个专业 Agent**：创意总监、主程、美术总监、QA 主管……三层管理架构
- **73 个斜杠命令技能**、12 个 hooks、11 条规则、41 个模板
- 支持 Godot 4 / Unity / Unreal Engine 5
- 定义了设计评审 → QA 门禁 → 生产工作流的完整升级路径

**对独立开发者的意义**：可以用 `\design-review` 命令让 AI 自检设计合理性，用 `\qa` 命令提 bug，用 `\crunch` 赶工——不需要自己做项目管理。

---

### 流派 4：AI 游戏素材生成工具

| 项目 | ⭐ | 生成内容 | 输入 → 输出 |
|---|---|---|---|
| **agent-sprite-forge** | **3,168★** | 2D 精灵 / 精灵表 / 地图 / GIF | 自然语言 → AI 规划管线 → 图像生成 → Python 去底/提帧/对齐 → 透明 PNG / Godot TileMap / Unity 兼容资源 |
| **pixel-asset-master-skills** | - | 像素风全套资源 | 中文文档。像素尺寸/调色板/精灵模板库、帧动画、QA、合图。目标 Unity + Godot |
| **sprite-gen** (aldegad) | - | 精灵表 + 动画图集 | 画一张 → 出完整精灵表 |
| **tilemapgen** (charmed-ai) | - | 等距地牢瓦片图 | Stable Diffusion。有 Web 版 |
| **pixel_music_creator** (chepedev) | - | 芯片音乐（循环 BGM） | 情绪 + 种子 → 无缝循环游戏音乐 |
| **16bits-gaudio-mcp** (Matuyuhi) | - | BGM / SFX / Jingle | Zig 编写的 MCP 服务器，零依赖 |

---

## 三、关键趋势（2025–2026）

### 趋势 1：MCP 成为新标准

- 一年前基本不存在，现在所有主流引擎都有至少一个 MCP 实现
- Unity 有 5 个竞争实现，Godot 有 3 个
- 意味着 **AI 编码助手可以直接操控引擎编辑器**，不再是"生成代码让你自己去粘贴"

### 趋势 2：Studio 编排 > 纯生成

- 最高星项目（22,748★）不是游戏生成器，而是**流程/组织层**
- 社区更看重有结构的过程而非万能生成
- godogen（4,552★）证明了全自动管线的需求，但工作室编排更受欢迎

### 趋势 3：Godot 成为 AI 游戏开发的第一引擎

- 开源、轻量（可无头运行）、无许可限制、社区活跃
- 跨平台 MCP 支持（AkerMCP / UltraRabbit 149 工具）
- 三大生成管线都优先支持 Godot（godogen / GodotMaker / Summer Engine）

### 趋势 4：从「生成代码」到「生成+验证+修复」闭环

- godogen 的工作流包含运行游戏→截图→AI 自我检查→修复
- Locus 可以实时读取 Unity 错误日志并自动修复
- 这是 2025 年之前几乎没有的功能

---

## 四、独立游戏开发者的选择路径

```
你想怎么用 AI 做游戏？
│
├─ 方式 A：一句话生成整个游戏
│   ├─ 目标引擎是 Godot → godogen（4,552★）
│   ├─ 目标引擎是 Web → OpenGame（3,000★）
│   └─ 目标引擎是 Unity → UniGen（论文验证，但星少）
│   ⚠️ 适合原型验证，不适合长期维护
│
├─ 方式 B：AI 助手直接在引擎里帮你干活
│   ├─ 用 Unity → Locus（612★ Rust Agent）+ Unity-MCP
│   ├─ 用 Godot → UltraRabbit/godot-mcp（149 工具）+ AkerMCP
│   ├─ 用 Unreal → Unreal_mcp（784★）
│   └─ 跨引擎 → AkerMCP（Unity + Godot + Stride 统一接口）
│   ✅ 最适合「人主导 + AI 辅助」的开发流程
│
├─ 方式 C：给 AI 会话建立工作室流程
│   └─ Claude Code Game Studios（22,748★）
│   ✅ 适合多 Agent 协作、需要 QA 门禁的复杂项目
│
└─ 方式 D：我需要 AI 生成素材
    ├─ 2D 精灵 → agent-sprite-forge（3,168★）
    ├─ 像素风全套 → pixel-asset-master-skills
    └─ 音乐/SFX → pixel_music_creator / 16bits-gaudio-mcp
```

### 对 `indl_game` 的建议

当前项目完全空白，推荐路径：

**第一步**：确定目标游戏类型和引擎

- **Godot 4** → AI 生态最丰富（godogen + UltraRabbit/godot-mcp + Claude Code Game Studios）
- **Unity** → 开发体验最成熟（Locus + Unity-MCP + Funplay MCP）
- **Web** → 最快速出原型（OpenGame + game-creator）

没有引擎偏好的话，**Godot 4 是当前 AI 辅助开发支持最好的引擎**：
- 三大 AI 游戏生成管线优先支持 Godot
- Godot MCP 有 149 工具（Unity MCP 最深 128）
- Godot 开源，可无头运行，方便 AI 自动化
- Summer Engine 证明 Godot 做 AI-native 引擎最合适

**第二步**：决定工作方式

- **纯 AI 生成原型** → `godogen`，输入游戏描述，15 分钟出可运行 demo
- **AI 辅助迭代开发** → Cursor + Claude Code Game Studios + Godot-MCP
- **素材生成** → agent-sprite-forge + pixel_music_creator

---

## 五、风险与缺口

| 风险 | 说明 |
|---|---|
| **生产级质量仍需人力** | 没有一个工具能不经人工干预产出商业品质游戏。godogen 最接近但仍需 API key + GPU + 小时级运行 |
| **生成代码可维护性** | AI 生成的代码后续手动修改可能困难。godogen 的工程结构较好，但 OpenGame/UniGen 的输出质量波动大 |
| **多人/网络支持空白** | 目前没有一个 AI 游戏生成工具充分支持多人/联网游戏的生成 |
| **3D 素材质量差距** | 2D 精灵生成已很成熟（agent-sprite-forge 3,168★），3D 模型仍显著落后 |
| **AI 音频不成熟** | 最高星的游戏 AI 音频工具不足 100★ |
| **许可证注意** | 大部分项目为 MIT/Apache-2.0（godogen、Claude Code Game Studios、agent-sprite-forge、Locus），少数有商业限制或未声明 |

---

## 六、核心结论

### 三个核心建议

1. **全新项目** → **Godot 4** + godogen（原型阶段）→ Godot-MCP + Claude Code Game Studios（迭代开发阶段）
2. **保留 Unity 生态** → **Locus**（Rust 独立 Agent，增长快）+ Unity-MCP
3. **最轻量快速验证** → **OpenGame**（Web 游戏，无需安装引擎）

### 值得深入研究的候选

**Claude Code Game Studios**（22,748★）值得作为 AI 游戏开发的工作流基础层——它给你团队结构，不绑定引擎。建议作为下一步深入研究的首选。

---

## 附录：完整项目列表

| 项目 | GitHub | ⭐ | 类别 |
|---|---|---|---|
| Claude Code Game Studios | https://github.com/Donchitos/Claude-Code-Game-Studios | 22,748★ | Studio 编排 |
| godogen | https://github.com/htdt/godogen | 4,552★ | 全自动生成 |
| agent-sprite-forge | https://github.com/0x0funky/agent-sprite-forge | 3,168★ | 素材生成 |
| OpenGame | https://github.com/leigest519/OpenGame | ~3,000★ | 全自动生成（Web） |
| Unreal_mcp | https://github.com/ChiR24/Unreal_mcp | 784★ | MCP 服务器 |
| Locus | https://github.com/r1n7aro/Locus | ~612★ | Unity Dev Agent |
| GodotMaker | https://github.com/RandallLiuXin/GodotMaker | 426★ | 全自动生成 |
| game-creator | https://github.com/PlayableIntelligence/game-creator | 246★ | 全自动生成（Web） |
| UltraRabbit/godot-mcp | https://github.com/UltraRabbit/godot-mcp | - | MCP 服务器（149 工具） |
| IvanMurzak/Unity-MCP | https://github.com/IvanMurzak/Unity-MCP | - | MCP 服务器 |
| AkerMCP | https://github.com/lorenzo-cambiaghi/AkerMCP | - | 跨引擎 MCP |
| Summer Engine | https://github.com/SummerEngine/summer-engine-agent | - | AI 原生引擎 |
| UniGen | https://github.com/yxwan123/UniGen | 16★ | 全自动生成（论文） |
| gamedev-all-in-one-mcp | https://github.com/dmae97/roblox_all_in_one_mcp | - | 多引擎 MCP |
