# Freesound Game Audio Browser 快速实操手册 {#top}
> 本文档是一份面向独立游戏开发者的工具实操指南，帮助你在 Godot 4 项目中高效获取商用友好的音效资源。

## 导读摘要 {#section-01}
### 这份手册解决什么问题 {#section-02}
在独立游戏开发中，音效往往比美术更容易被忽视，但一个没有合适音效的游戏会让人"总觉得少了什么"。问题在于：Freesound.org 上有 72 万+ 条音效，但大部分人不知道如何快速筛选出**可以商用**的那一部分。

本手册聚焦一个专门解决这个痛点的浏览器端工具——**[Freesound Game Audio Browser](https://gamesfxmaker.com/)（gamesfxmaker.com）**，教会你在 3 步之内从搜关键词到把音效导入 Godot 编辑器。

### 本手册的三个特点 {#section-03}
1. **纯粹的工具导向**——不讲音效设计理论，只讲怎么搜、怎么筛、怎么下、怎么导入。
2. **与 Godot 工作流衔接**——从格式选择（OGG vs WAV）到导入设置，全部踩在你的引擎选择上。
3. **附赠证照管理方案**——CC0 和 CC-BY 怎么处理，一条一条说清楚，避免发布后踩版权坑。

## 1. 工具概览 {#section-04}
### 一句话定义 {#section-05}
Freesound Game Audio Browser 是一个 Web 工具，它用 Freesound.org 的 API 检索音效，但在返回结果之前自动过滤掉非商用许可，只展示 **CC0**（免费商用、无需署名）和 **CC-BY**（免费商用、需署名）两类。

### 为什么要用它，而不是直接上 Freesound.org {#section-06}
直接上 Freesound.org 搜 "footstep"，出来 500 条结果里有：

- CC0（免费商用、无需署名） ✅
- CC-BY（免费商用、需署名） ✅
- CC-BY-NC（**禁止**商用） ❌
- 保留所有权利（不可用） ❌

你需要**逐条点开看许可证**，效率极低。

gamesfxmaker.com 替你省了这一步：**后台自动过滤，只展示商用友好的结果。**

### 它的额外功能 {#section-07}
- **一键格式转换**：下载时即时转 WAV（Unity/Unreal）或 OGG（Godot），不用额外拿 Audacity 转码。
- **Quick Categories**：预设 Battle、UI、Ambience、Footsteps 等常用分类，不知道该搜什么时直接点。
- **无需注册、免费**：打开网页直接用。

## 2. 三步工作流 {#section-08}
### 第一步：打开工具 {#section-09}
访问 **[https://gamesfxmaker.com/search/](https://gamesfxmaker.com/search/)**

首页有两大入口：

- **Smart Search**（搜索框）—— 关键词搜索，自动过滤许可。
- **Quick Categories**（快捷分类）—— 预设搜索词：Battle、UI、Ambience、Footsteps 等。

> 如果不知道该搜什么，先点 Footsteps、UI、Explosion 等分类，感受一下结果范围。

### 第二步：输入关键词 + 试听 {#section-10}
- **用英文搜索**：Freesound 的标签和文件名基本上是英文的。"footstep grass" 比 "脚步声草地" 结果多两个数量级。
- **组合关键词**：先用一个核心词（如 `footstep`），再用第二个词缩小范围（如 `footstep stone`、`footstep wood`、`footstep dirt`）。
- **试听**：每条结果右侧有播放按钮。音质参差不齐（社区上传），多试几个再下载。
- **留意时长**：普通音效 0.5–3 秒最佳。太长的下载后需要剪裁（Audacity 或 Godot 的 AudioStream 裁剪都可以）。

**常用搜索关键词参考**

| 场景 | 推荐关键词 |
|---|---|
| 脚步（草地） | `footstep grass`、`footstep leaves` |
| 脚步（石板） | `footstep stone`、`footstep concrete` |
| 拾取道具 | `pickup coin`、`collect item`、`powerup` |
| 攻击/挥剑 | `sword swing`、`slash whoosh` |
| 命中 | `hit impact`、`punch` |
| UI 点击 | `ui click`、`button press`、`select confirm` |
| 环境（森林） | `forest ambience`、`birds` |
| 环境（洞穴） | `cave drip`、`echo` |
| 爆炸 | `explosion`、`boom` |

### 第三步：选择格式 + 下载 {#section-11}
| 使用场景 | 推荐格式 | 理由 |
|---|---|---|
| Godot 引擎 | OGG | 原生支持、压缩率好、导入即用 |
| Unity / Unreal | WAV | 无损格式，引擎内再做压缩 |
| 不确定/存底稿 | WAV | 保留最高质量，后续可转 |

工具在浏览器端即时转码，下载到本地就是所选格式。

## 3. 下载后的证照管理 {#section-12}
这是最容易忽略、发布时最容易出事的环节。

### CC0（最省心） {#section-13}
- **不需要署名**。
- 建议：下载时顺便在项目文件或 `README` 里记一条来源 URL。不是法律要求，而是方便将来回溯——比如你觉得这个音效还可以改进，回去找原始文件时可以搜到。

### CC-BY（需要署名） {#section-14}
- **必须署名**。在你的游戏 Credits 或关于页面里按这个格式列一行：

  ```
  "Footsteps Grass" by SoundAuthorName (Freesound)
  License: CC BY 4.0
  Source: https://freesound.org/s/123456/
  ```

- **推荐做法**：在项目目录建一个 `assets/audio/credits.txt`，每次用 CC-BY 音效就追加一行。发布时批量搬到游戏 Credits 里。

### 管理策略 {#section-15}
| 音效数量 | 建议 |
|---|---|
| 少于 10 个 | 全用 CC0，省去署名管理成本 |
| 10–30 个 | 大部分 CC0 + 少量 CC-BY（控制在 Credits 里不超过半屏） |
| 30 个以上 | 优先整套音效包（如 Boolean SFX Bundle，CC0 无需署名），再用本工具补缺 |

## 4. 在 Godot 4 中的导入 {#section-16}
以下流程假设你已经用 gamesfxmaker.com 下载好 OGG 文件。

```
下载的 OGG 文件
    ↓ 复制到 Godot 项目目录 (res://assets/audio/)
    ↓ 在 Godot 编辑器中，FileSystem 面板自动识别
    ↓ 自动生成 AudioStreamOggVorbis 资源
    ↓ 拖拽到 AudioStreamPlayer2D 或 AudioStreamPlayer3D
    ↓ 用代码或信号触发 .play()
```

**循环播放设置**：如果音效需要循环（如环境声、引擎声）：

1. 在 FileSystem 面板选中导入的 OGG 文件
2. 在 Import 面板勾选 **Loop**
3. 点击 **Reimport**

**叠加音效技巧**：如果下载到的音效太短或太单薄，可以在同一个位置放多个 AudioStreamPlayer，用随机偏移时间播放不同变体。Godot 的 `RandomAudioStreamPlayer2D` 插件（Asset Library 上可搜）可以帮你自动化这个流程。

## 5. 与其他方案对比 {#section-17}
| 方案 | 适合场景 | 成本 | 许可确定性 |
|---|---|---|---|
| Freesound Game Audio Browser | 需要精确定位的音效（如某种特定脚步） | 免费 | 高（自动过滤商用许可） |
| Boolean SFX Bundle（itch.io） | 大量通用音效一次性获取（977 个） | 免费 | 高（CC0，无需署名） |
| 400 Sounds Pack（itch.io） | UI、战斗、环境全覆盖 | 免费 | 高（免费商用） |
| SoundsFree（AI 生成） | 实在搜不到的特殊音效 | 免费 | 视服务条款而定 |
| jsfxr / ChipTone | 复古像素风格合成音效 | 免费 | 自己生成，无版权问题 |
| 自己录制（Audacity） | 需要 100% 原创的场景 | 免费 | 完全自持 |

## 6. 快速检查清单 {#section-18}
每次找音效过一遍：

- [ ] 打开 [gamesfxmaker.com/search/](https://gamesfxmaker.com/search/)
- [ ] 用英文关键词搜索
- [ ] 试听 3–5 个候选，挑层次最合适的
- [ ] 选对格式（Godot → OGG）
- [ ] 检查许可证标记（CC0 还是 CC-BY）
- [ ] CC-BY 的记到 `credits.txt`
- [ ] 复制 OGG 到 `res://assets/audio/`
- [ ] 勾选 Loop（如果需要循环）
- [ ] 拖到 AudioStreamPlayer 节点上

## 7. 参考资料 {#section-19}
| 来源 | 网址 | 用途 |
|---|---|---|
| Freesound Game Audio Browser | https://gamesfxmaker.com/ | 核心工具 |
| Freesound.org | https://freesound.org/ | 底层音效数据库（手动查备用） |
| Boolean SFX Bundle（itch.io） | https://booleanbeats.itch.io/boolean-sfx-complete-bundle-977-sounds-2499 | 977 音效 CC0 包 |
| 400 Sounds Pack（itch.io） | https://ci.itch.io/400-sounds-pack | 通用音效包 |
| SoundsFree | https://www.soundsfree.art/ | AI 程序化音效生成 |
| jsfxr | https://sfxr.me/ | 复古合成音效 |
| GameDev StackExchange 署名指南 | https://gamedev.stackexchange.com/questions/205361 | CC-BY 署名格式规范 |
