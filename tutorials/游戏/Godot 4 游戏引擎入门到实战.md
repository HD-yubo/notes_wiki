---
title: "Godot 4 游戏引擎入门到实战"
tags:
  - tutorial
  - godot
  - game-development
  - gamedev
aliases:
  - Godot 4 入门
  - Godot 4 从入门到实战
---
# Godot 4 游戏引擎入门到实战

## 第1章 Godot 是什么？为什么选择它？

![第1章：Godot 与其他游戏引擎的对比矩阵](assets/godot_4/chapter-01.svg)

> [!abstract]
> 你想做一款游戏，但不知道从哪里开始。打开搜索引擎，Unity、Unreal、Godot 扑面而来。你听说 Godot 是"免费开源"的，但不确定它是否足够强大。这一章帮你搞清楚 Godot 到底是什么，以及它为什么值得你的时间。

### 1.1 游戏引擎到底帮你做了什么？

从零写一个游戏，意味着你要自己处理画面渲染、键盘输入、物理碰撞、音频播放、内存管理……这些事情中的每一件都足够让你忙上几个月。

游戏引擎就是一个"游戏开发工作台"。它把渲染、输入、物理、音频、资源管理等底层工作都做好了，让你可以专注于"做什么游戏"而不是"怎么让像素显示在屏幕上"。

用一个类比：游戏引擎是厨房，食材和工具都准备好了，你只需要决定做什么菜。

### 1.2 Godot 的设计哲学

Godot 有三个核心特点让它与众不同：

**完全免费且开源**：Godot 使用 MIT 许可证，这意味着你不需要支付任何费用，没有收入分成，没有订阅费。你赚的钱 100% 归你。

**场景驱动架构**：Godot 的世界观是"一切皆场景"。游戏是一个大场景，大场景由小场景组成，小场景由节点组成。这种设计让你可以用统一的方式思考所有内容。

**2D 原生支持**：与 Unity 和 Unreal 把 3D 引擎"降级"到 2D 不同，Godot 拥有独立的 2D 渲染管线。2D 游戏在 Godot 中运行在真正的 2D 像素坐标系中，不需要处理透视相机、3D 到 2D 的映射等复杂问题。

### 1.3 Godot vs Unity vs Unreal：新手该选哪个？

| 对比维度 | Godot 4 | Unity | Unreal |
|---------|---------|-------|--------|
| 价格 | 完全免费 | 收入超阈值后收费 | 收入超阈值后 5% 分成 |
| 开源 | MIT 许可，完全开源 | 不开源 | 不开源 |
| 2D 能力 | 原生 2D 引擎 | 3D 引擎模拟 2D | 3D 引擎模拟 2D |
| 脚本语言 | GDScript（类 Python） | C# / JavaScript | C++ / 蓝图可视化 |
| 学习曲线 | 平缓，适合零基础 | 中等 | 陡峭 |
| 安装包大小 | 约 100MB | 数 GB | 数十 GB |
| 社区规模 | 增长中，中等 | 最大 | 大 |

如果你是完全的新手，或者想做 2D 游戏，Godot 是阻力最小的路径。如果你目标是大型 3D 商业项目，Unity 或 Unreal 可能更合适——但你仍然可以先用 Godot 学会游戏开发的核心概念。

### 1.4 你能用 Godot 做什么？

Godot 可以做：
- 2D 平台跳跃游戏（类似《蔚蓝》）
- 2D 角色扮演游戏（类似《星露谷物语》）
- 2D 射击/弹幕游戏
- 卡牌/策略/桌游
- 简单的 3D 游戏
- 移动端游戏（Android、iOS）
- Web 游戏（直接在浏览器中运行）
- 桌面游戏（Windows、macOS、Linux）

### 本章小结

Godot 是一个免费、开源、场景驱动的游戏引擎，对 2D 游戏开发有原生支持，学习曲线平缓。它用 GDScript 作为主要脚本语言，语法类似 Python，对新手友好。

### 第1章检查点：引擎定位与选择

- [ ] 你能说出游戏引擎的至少三个作用
- [ ] 你能说出 Godot 相比 Unity 的两个优势
- [ ] 你知道 Godot 可以导出到哪些平台

---

## 第2章 安装与第一个项目

![第2章：Godot 编辑器核心面板布局](assets/godot_4/chapter-02.svg)

> [!abstract]
> 了解了 Godot 之后，你迫不及待想打开它。但安装完打开编辑器，你会看到一堆面板和按钮，不知道从哪里下手。这一章带你完成安装，认识编辑器界面，并创建你的第一个项目。

### 2.1 下载和安装 Godot 4

Godot 的安装出奇地简单：

1. 打开 https://godotengine.org/download
2. 下载 Godot 4.x 的 Standard 版本（无需注册）
3. 解压到一个你喜欢的文件夹

没错，就这么简单。Godot 是一个单文件可执行程序，不需要安装向导，不会往系统里写注册表。解压后的 `Godot_v4.x.x-stable.exe`（Windows）就是你要双击运行的东西。

> [!tip] 建议
> 建议把 Godot 放在一个固定的文件夹（比如 `D:\Godot\`），并在桌面创建快捷方式。

### 2.2 认识编辑器界面

打开 Godot 后，你会看到项目管理器。先创建一个项目，然后进入编辑器。编辑器界面分为以下几个核心区域：

```
┌─────────────────────────────────────────────────────┐
│                    菜单栏 + 工具栏                    │
├──────────┬──────────────────────────┬───────────────┤
│          │                          │               │
│  场景面板  │      2D/3D 视口          │   检查器面板   │
│          │   （你的游戏世界）         │  （节点属性）   │
│          │                          │               │
├──────────┴──────────────────────────┴───────────────┤
│                    脚本编辑器 / 底部面板               │
│              （输出、调试器、动画、音效等）              │
└─────────────────────────────────────────────────────┘
```

四个最重要的面板：

| 面板 | 位置 | 作用 |
|------|------|------|
| **场景面板** | 左上 | 显示当前场景的节点树结构 |
| **视口** | 中间 | 你的游戏世界的可视化预览 |
| **检查器** | 右侧 | 编辑选中节点的所有属性 |
| **底部面板** | 底部 | 输出日志、调试器、动画编辑器等 |

### 2.3 创建你的第一个项目

1. 在项目管理器中点击 **"新建项目"**
2. **项目名称**：输入 `MyFirstGame`
3. **路径**：选择一个文件夹（比如 `D:\Projects\MyFirstGame`）
4. **渲染器**：选择 **"兼容"**（Compatibility）——这对 2D 游戏足够用，且兼容性最好
5. 点击 **"创建并编辑"**

项目创建完成后，你会看到编辑器主界面。此时项目是空的，什么都还没有。

### 2.4 运行一个空场景

让我们运行一下看看效果：

1. 点击菜单 **场景 → 新建场景**
2. 选择 **"2D 场景"**（这会创建一个包含 `Node2D` 根节点的空白场景）
3. 按 **F5** 或点击顶部的播放按钮 ▶

你会看到一个空白窗口弹出来——这就是你的"游戏"。现在什么都没有，但这是一个真正的、可以运行的游戏窗口。

> [!important] 主场景
> 在 Godot 中，你**必须有一个主场景**才能运行游戏。首次按 F5 时，编辑器会问你"选择主场景"，选择你刚创建的场景文件并保存（比如 `main.tscn`）。

### 本章小结

Godot 是免安装的绿色软件。编辑器有四个核心面板：场景面板、视口、检查器、底部面板。创建项目后，新建一个场景并保存为主场景，就可以按 F5 运行。

### 第2章检查点：环境搭建与项目创建

- [ ] 你成功下载并打开了 Godot 4
- [ ] 你创建了一个新项目并进入了编辑器
- [ ] 你运行了一个空场景，看到了游戏窗口
- [ ] 你能说出编辑器四个核心面板的名称和作用

---

## 第3章 场景树与节点——Godot 的灵魂

![第3章 视觉概览](assets/godot_4/chapter-03.svg)

> [!abstract]
> 你打开了编辑器，创建了场景，但你可能会问："这个游戏到底是怎么组织起来的？"在 Godot 中，答案是一棵树——场景树。理解场景树和节点，是理解 Godot 一切功能的前提。

### 3.1 什么是场景树？

在 Godot 中，你的游戏世界是一棵**树**。这棵树由一个个**节点**组成，节点之间有父子关系。

举个具体例子：假设你做了一个射击游戏。你的游戏场景可能是这样的：

```
MainGame（主场景根节点）
├── Player（玩家角色）
│   ├── Sprite（玩家图像）
│   ├── CollisionShape（玩家碰撞体）
│   └── Camera2D（跟随玩家的相机）
├── Enemies（敌人群组）
│   ├── Enemy1
│   └── Enemy2
└── HUD（界面）
    ├── ScoreLabel（分数文字）
    └── HealthBar（血条）
```

这就是一棵场景树。`MainGame` 是根节点，`Player`、`Enemies`、`HUD` 是它的子节点，`Sprite`、`CollisionShape` 又是 `Player` 的子节点。

**为什么用树形结构？** 因为游戏世界天然是有层次的。玩家属于游戏世界，玩家的图像属于玩家。树的层级关系直接反映了这种归属。

### 3.2 节点：一切的基础

节点是 Godot 中最小的构建单元。每个节点做一件事：

| 节点类型 | 作用 |
|---------|------|
| `Node2D` | 2D 空间中的基础节点 |
| `Sprite2D` | 在屏幕上显示一张图片 |
| `Camera2D` | 定义游戏的视角 |
| `CharacterBody2D` | 可移动的角色 |
| `CollisionShape2D` | 定义碰撞检测的形状 |
| `Label` | 显示文字 |
| `Button` | 可点击的按钮 |
| `AudioStreamPlayer` | 播放声音 |
| `Timer` | 倒计时器 |
| `AnimationPlayer` | 播放动画 |

一个节点本身很简单，但当它们组合在一起，就能构建出复杂的游戏功能。

### 3.3 节点的父子关系

节点之间的父子关系不只是组织方式，它还决定了很多行为：

**坐标继承**：子节点的位置是相对于父节点的。如果 `Player` 移动到坐标 (100, 50)，它的子节点 `Sprite` 就会跟着移动，即使 `Sprite` 自己的位置是 (0, 0)。

**生命周期**：父节点被删除时，所有子节点也会被删除。

**可见性继承**：父节点设为不可见，子节点也不会显示。

**处理顺序**：父节点先于子节点接收处理帧。

### 3.4 常用节点类型速览

Godot 有数百种节点类型，但入门阶段只需要掌握这些：

**2D 渲染类**：
- `Node2D` —— 2D 场景的通用容器
- `Sprite2D` —— 显示静态图片
- `AnimatedSprite2D` —— 播放帧动画
- `TileMap` —— 用瓷砖图块拼出地图

**物理类**：
- `CharacterBody2D` —— 玩家控制的角色
- `RigidBody2D` —— 受物理模拟驱动的物体
- `StaticBody2D` —— 静止的物理物体（墙壁、地面）
- `Area2D` —— 检测进入/离开的区域

**UI 类**：
- `Control` —— 所有 UI 节点的基类
- `Label` —— 文字显示
- `Button` —— 按钮
- `TextureRect` —— 显示图片的 UI 元素
- `VBoxContainer` / `HBoxContainer` —— 自动排列子元素

### 3.5 实战：构建一个简单的场景

让我们动手构建一个包含玩家和地面的场景：

1. 新建一个 2D 场景，将根节点重命名为 `GameWorld`
2. 右键根节点 → **添加子节点** → 搜索 `Sprite2D` → 添加
3. 选中 Sprite2D，在右侧检查器中找到 **Texture** 属性，点击 `<空>` → **新建 ImageTexture** → 选择一张角色图片
4. 再添加一个 `StaticBody2D` 子节点作为地面
5. 在 `StaticBody2D` 下添加 `CollisionShape2D` 子节点
6. 选中 `CollisionShape2D`，在检查器中设置 **Shape** 为 `RectangleShape2D`，调整大小

保存这个场景（Ctrl+S），命名为 `game_world.tscn`。

### 本章小结

Godot 的游戏世界是场景树，场景树由节点组成。节点有父子关系，子节点继承父节点的坐标、可见性等属性。入门只需掌握十几个常用节点类型。

### 第3章检查点：场景树与节点系统

- [ ] 你能画出一个简单游戏的节点树结构
- [ ] 你知道子节点的位置是相对于谁计算的
- [ ] 你能区分 `Sprite2D`、`CharacterBody2D`、`Area2D` 的用途
- [ ] 你成功构建了一个包含精灵和碰撞体的场景

---

## 第4章 GDScript 编程基础

![第4章 视觉概览](assets/godot_4/chapter-04.svg)

> [!abstract]
> 场景搭好了，但节点还是"死的"。要让它们动起来，你需要写代码。Godot 使用的编程语言叫 GDScript，它的语法类似 Python，非常容易上手。即使你从未写过代码，这一章也能让你从零开始。

### 4.1 为什么是 GDScript？

Godot 支持多种编程语言（GDScript、C#、C++ via GDExtension），但 GDScript 是专门为 Godot 设计的。它的优势是：

- 语法简洁，类似 Python，用缩进代替大括号
- 与引擎深度集成，访问节点和属性非常方便
- 不需要编译，保存即生效
- 专为游戏开发场景优化

### 4.2 变量与数据类型

在 GDScript 中定义变量：

```gdscript
var player_name = "Hero"       # 字符串
var health = 100               # 整数
var speed = 200.0              # 浮点数
var is_alive = true            # 布尔值
var position_x = 150.5         # 浮点数

# 类型声明（可选但推荐）
var max_health: int = 100
var move_speed: float = 200.0
var player_name: String = "Hero"

# 常量
const GRAVITY = 980.0
```

GDScript 的常用数据类型：

| 类型 | 说明 | 示例 |
|------|------|------|
| `int` | 整数 | `100`, `-5` |
| `float` | 浮点数 | `3.14`, `200.0` |
| `bool` | 布尔值 | `true`, `false` |
| `String` | 字符串 | `"Hello"` |
| `Vector2` | 二维向量 | `Vector2(10, 20)` |
| `Array` | 数组 | `[1, 2, 3]` |
| `Dictionary` | 字典 | `{"key": "value"}` |

`Vector2` 是游戏开发中最常用的类型之一，它表示一个二维坐标或方向：

```gdscript
var direction = Vector2(1, 0)   # 向右的方向
var position = Vector2(100, 200) # x=100, y=200 的位置
```

### 4.3 函数与返回值

函数是一段可以重复使用的代码：

```gdscript
func calculate_damage(base_damage: float, multiplier: float) -> float:
    return base_damage * multiplier

func take_damage(amount: int):
    health -= amount
    if health <= 0:
        die()

func die():
    is_alive = false
    print("Game Over!")
```

GDScript 中有几个特殊的内置函数：

| 函数 | 何时被调用 |
|------|-----------|
| `_ready()` | 节点第一次进入场景树时调用，用于初始化 |
| `_process(delta)` | 每一帧都调用，用于游戏逻辑更新 |
| `_physics_process(delta)` | 每个物理帧调用，用于物理相关逻辑 |
| `_input(event)` | 有输入事件时调用（按键、鼠标等） |

### 4.4 控制流：if、for、while

```gdscript
# if 判断
if health > 50:
    print("状态良好")
elif health > 0:
    print("受伤了")
else:
    print("已死亡")

# for 循环
for i in range(5):
    print(i)   # 输出 0, 1, 2, 3, 4

for enemy in enemies_array:
    enemy.take_damage(10)

# while 循环
while health > 0:
    health -= 10
    print("剩余生命: ", health)
```

### 4.5 信号（Signals）：节点间的通信

信号是 Godot 中节点之间传递消息的机制。简单理解：信号就是"广播"。一个节点说"我发生了某事"，其他节点可以"听到"并做出反应。

```gdscript
# 定义信号
signal health_changed(new_health)
signal player_died

# 发射信号
func take_damage(amount: int):
    health -= amount
    health_changed.emit(health)    # 广播：生命值变了
    if health <= 0:
        player_died.emit()         # 广播：玩家死了
```

其他节点可以连接这个信号来做出反应：

```gdscript
# 在 HUD 节点中
func _ready():
    player.health_changed.connect(_on_health_changed)

func _on_health_changed(new_health: int):
    health_label.text = str(new_health)
```

### 4.6 实战：让一个节点动起来

让我们写第一个脚本，让一个 Sprite2D 节点根据按键移动：

1. 创建一个新场景，添加 `CharacterBody2D` 作为根节点，重命名为 `Player`
2. 在 `Player` 下添加 `Sprite2D` 和 `CollisionShape2D`
3. 选中 `Player` 节点，点击检查器上方的 **附加脚本** 按钮
4. 编写以下代码：

```gdscript
extends CharacterBody2D

@export var speed: float = 200.0

func _physics_process(delta):
    # 获取输入方向
    var direction = Vector2.ZERO
    direction.x = Input.get_axis("ui_left", "ui_right")
    direction.y = Input.get_axis("ui_up", "ui_down")
    
    # 移动
    velocity = direction * speed
    move_and_slide()
```

5. 按 F5 运行，使用方向键或 WASD 移动角色

> [!info] Input 输入映射
> `Input.get_axis()` 使用 Godot 内置的输入映射。`ui_left`、`ui_right`、`ui_up`、`ui_down` 默认对应方向键和 WASD。`move_and_slide()` 是 `CharacterBody2D` 的内置方法，它会根据速度移动节点并自动处理碰撞。

### 本章小结

GDScript 语法类似 Python，使用缩进组织代码。核心概念包括变量、函数、控制流和信号。`_ready()` 用于初始化，`_process()` 和 `_physics_process()` 用于每帧更新。`Vector2` 是游戏开发中最常用的数据类型。

### 第4章检查点：GDScript 基础语法

- [ ] 你能定义不同类型的变量并解释 `Vector2` 的用途
- [ ] 你能写出 `_ready()` 和 `_process()` 的区别
- [ ] 你能理解信号的定义、发射和连接过程
- [ ] 你成功让一个角色响应键盘输入移动

---

## 第5章 2D 游戏开发核心

![第5章 视觉概览](assets/godot_4/chapter-05.svg)

> [!abstract]
> 角色能动了，但这离一个"游戏"还差得远。你需要显示图像、播放动画、处理输入、计算分数。这一章把所有 2D 游戏开发的基础知识串起来，让你能开始做一个真正的游戏。

### 5.1 2D 坐标系与位置

Godot 的 2D 坐标系原点在**左上角**：
- x 轴向右为正
- y 轴向下为正（这和数学课本的坐标系不一样！）

```
(0,0) ───────────────→ x+
  │
  │     屏幕
  │
  ↓
  y+
```

每个 `Node2D` 都有 `position` 属性（相对于父节点）和 `global_position` 属性（相对于世界原点）。修改位置有两种方式：

```gdscript
# 直接设置位置
player.position = Vector2(100, 200)

# 增量移动
player.position += Vector2(5, 0)  # 向右移动 5 像素
```

### 5.2 Sprite2D：显示图像

`Sprite2D` 是最基础的显示节点，它负责在屏幕上画一张图片：

```gdscript
# 在代码中设置纹理
var texture = load("res://assets/player.png")
$Sprite2D.texture = texture
```

常用属性：
- `texture`：要显示的图片
- `flip_h` / `flip_v`：水平/垂直翻转
- `modulate`：颜色叠加（可以用来做受伤闪红效果）
- `scale`：缩放倍数

### 5.3 AnimatedSprite2D：帧动画

帧动画就是把一组图片快速连续播放，产生运动的错觉。

1. 选中 `AnimatedSprite2D` 节点
2. 在检查器中点击 **SpriteFrames** → **新建 SpriteFrames**
3. 在打开的动画面板中：
   - 点击 **+** 添加动画，命名为 `walk`
   - 把行走帧图片拖入帧列表
   - 调整 **FPS**（每秒帧数），通常 8-12 帧足够流畅
4. 再创建一个 `idle` 动画，放入站立帧

在代码中切换动画：

```gdscript
func _process(delta):
    if velocity.length() > 0:
        $AnimatedSprite2D.play("walk")
    else:
        $AnimatedSprite2D.play("idle")
```

### 5.4 处理玩家输入

Godot 提供了多种输入处理方式：

**方式一：Input.is_action_pressed()**（推荐）

```gdscript
func _physics_process(delta):
    if Input.is_action_pressed("move_right"):
        position.x += speed * delta
```

这种方式使用 Godot 的**输入映射系统**。你可以在 **项目 → 项目设置 → 输入映射** 中自定义动作和对应的按键。

**方式二：Input.get_axis()**（更简洁）

```gdscript
func _physics_process(delta):
    var direction = Input.get_axis("move_left", "move_right")
    velocity.x = direction * speed
```

**方式三：_input(event)**（处理单次事件）

```gdscript
func _input(event):
    if event.is_action_pressed("jump"):
        jump()
```

### 5.5 _process 与 _physics_process

这两个函数每帧都会执行，但有重要区别：

| | `_process(delta)` | `_physics_process(delta)` |
|---|---|---|
| 调用频率 | 渲染帧率（不固定） | 固定物理帧率（默认 60 次/秒） |
| 用途 | 动画、UI、非物理逻辑 | 移动、碰撞、物理模拟 |
| delta | 上一帧到这一帧的时间 | 固定时间步长 |

**经验法则**：移动角色用 `_physics_process`，其他逻辑用 `_process`。

### 5.6 实战：创建一个可移动的角色

把前面的知识组合起来，创建一个完整的可移动角色：

```gdscript
extends CharacterBody2D

@export var speed: float = 200.0

func _physics_process(delta):
    # 获取输入
    var input_dir = Vector2.ZERO
    input_dir.x = Input.get_axis("move_left", "move_right")
    input_dir.y = Input.get_axis("move_up", "move_down")
    
    # 标准化对角线移动速度
    if input_dir.length() > 0:
        input_dir = input_dir.normalized()
    
    # 设置速度并移动
    velocity = input_dir * speed
    move_and_slide()
    
    # 切换动画
    if input_dir.length() > 0:
        $AnimatedSprite2D.play("walk")
    else:
        $AnimatedSprite2D.play("idle")
```

> [!warning] 对角线标准化
> 如果不做标准化，对角线移动的速度会比直线移动快约 41%（因为 √2 ≈ 1.414）。`normalized()` 会把向量的长度缩放到 1，只保留方向信息。

### 本章小结

Godot 的 2D 坐标系原点在左上角，y 轴向下。`Sprite2D` 显示静态图片，`AnimatedSprite2D` 播放帧动画。输入处理推荐使用输入映射系统。`_physics_process` 用于移动，`_process` 用于其他逻辑。`Vector2.normalized()` 是处理对角线移动的关键。

### 第5章检查点：2D 游戏开发核心

- [ ] 你知道 Godot 2D 坐标系中 y 轴正方向是向下
- [ ] 你能创建并配置一个 AnimatedSprite2D 的帧动画
- [ ] 你能解释 `_process` 和 `_physics_process` 的区别
- [ ] 你理解为什么需要对角线移动标准化

---

## 第6章 物理系统与碰撞

![第6章 视觉概览](chapter-06.svg)

> [!abstract]
> 你的角色可以移动了，但它穿墙而过、无视敌人——这不像游戏。要让游戏世界有"实体感"，你需要物理系统。这一章教你如何让物体有碰撞、有重力、有真实的物理交互。

### 6.1 物理体类型

Godot 提供了四种 2D 物理体，每种用途不同：

| 物理体 | 用途 | 谁控制运动 |
|--------|------|-----------|
| `CharacterBody2D` | 玩家角色、怪物等 | 你（代码） |
| `RigidBody2D` | 箱子、球、可被推动的物体 | 物理引擎 |
| `StaticBody2D` | 墙壁、地面、平台 | 不动 |
| `Area2D` | 检测区域（触发器、收集品） | 不涉及运动 |

**选择原则**：根据"谁控制运动"来选择。如果玩家控制就用 `CharacterBody2D`，如果物理引擎控制（受重力、推力）就用 `RigidBody2D`，如果不动就用 `StaticBody2D`，如果不需要运动只需要检测就用 `Area2D`。

### 6.2 CollisionShape2D：定义碰撞形状

物理体本身没有"形状"，你需要给它添加一个 `CollisionShape2D` 子节点来定义碰撞检测的边界：

常用形状：
- `RectangleShape2D` —— 矩形，适合角色、箱子
- `CircleShape2D` —— 圆形，适合球、硬币
- `CapsuleShape2D` —— 胶囊形，适合站立的角色
- `ConvexPolygonShape2D` —— 凸多边形，适合不规则物体

```
Player (CharacterBody2D)
├── CollisionShape2D (CapsuleShape2D)   ← 定义碰撞边界
├── AnimatedSprite2D                     ← 显示图像
└── Camera2D
```

> [!warning] 碰撞形状与图像分离
> 碰撞形状和图像是分开的。`Sprite2D` 负责"看起来"，`CollisionShape2D` 负责"碰到"。它们可以大小不同——比如碰撞体比图像小一点，让手感更好。

### 6.3 move_and_slide()：角色移动

`CharacterBody2D` 的核心方法是 `move_and_slide()`。你设置 `velocity`（速度），它负责移动并处理碰撞：

```gdscript
extends CharacterBody2D

@export var speed: float = 200.0
@export var jump_force: float = -400.0
@export var gravity: float = 980.0

func _physics_process(delta):
    # 添加重力
    if not is_on_floor():
        velocity.y += gravity * delta
    
    # 跳跃
    if Input.is_action_just_pressed("jump") and is_on_floor():
        velocity.y = jump_force
    
    # 水平移动
    var direction = Input.get_axis("move_left", "move_right")
    velocity.x = direction * speed
    
    move_and_slide()
```

`move_and_slide()` 会自动处理：
- 与墙壁的碰撞（停止移动）
- 与地面的接触（`is_on_floor()` 返回 true）
- 斜坡上的滑动
- 与其他物理体的碰撞响应

### 6.4 碰撞检测与响应

**方式一：使用 Area2D 检测进入/离开**

`Area2D` 会发射 `body_entered` 和 `body_exited` 信号：

```gdscript
# 在金币 Area2D 的脚本中
func _ready():
    body_entered.connect(_on_body_entered)

func _on_body_entered(body):
    if body is CharacterBody2D:
        collect()  # 玩家碰到了金币，收集它

func collect():
    queue_free()   # 删除自己
```

**方式二：使用 move_and_collide() 获取碰撞信息**

```gdscript
var collision = move_and_collide(velocity * delta)
if collision:
    var normal = collision.get_normal()
    velocity = velocity.bounce(normal)  # 反弹
```

### 6.5 实战：让角色与敌人碰撞

让我们做一个简单的"躲避敌人"场景：

1. 创建玩家 `CharacterBody2D`（有碰撞体和脚本）
2. 创建敌人 `Area2D`，添加 `CollisionShape2D` 和 `Sprite2D`
3. 敌人脚本：

```gdscript
extends Area2D

@export var speed: float = 100.0

func _process(delta):
    # 向左移动
    position.x -= speed * delta
    
    # 超出屏幕左侧时删除
    if position.x < -50:
        queue_free()
```

4. 玩家脚本中连接敌人的信号：

```gdscript
func _ready():
    # 连接所有 Area2D 的 body_entered 信号
    # 或者在敌人场景中连接
    pass

func take_damage():
    print("受伤！")
    # 处理受伤逻辑
```

### 本章小结

Godot 有四种物理体：`CharacterBody2D`（玩家控制）、`RigidBody2D`（物理引擎控制）、`StaticBody2D`（不动）、`Area2D`（只检测）。碰撞形状和图像是分开的。`move_and_slide()` 自动处理碰撞移动，`Area2D` 通过信号报告进入和离开。

### 第6章检查点：物理系统与碰撞检测

- [ ] 你能说出四种物理体的区别和使用场景
- [ ] 你知道为什么碰撞形状和图像要分开
- [ ] 你理解 `move_and_slide()` 做了什么
- [ ] 你成功让两个物体能检测碰撞

---

## 第7章 信号与节点通信进阶

![第7章 视觉概览](chapter-07.svg)

> [!abstract]
> 你可能已经注意到一个问题：当敌人被消灭时，分数怎么更新到 HUD 上？当玩家死亡时，游戏怎么知道要显示"Game Over"？节点之间需要通信，而信号就是 Godot 推荐的通信方式。

### 7.1 为什么用信号而不是直接引用？

一种常见的做法是直接找到节点然后修改它：

```gdscript
# 不推荐的方式
func collect_coin():
    get_node("../HUD/ScoreLabel").text = str(score)
    get_node("../GameManager").score += 1
```

这种写法的问题：
- 如果 HUD 的节点路径变了，所有引用都要改
- 节点之间的耦合太紧，换一个场景就报错
- 代码难以复用

信号的优势：

```gdscript
# 推荐的方式
signal coin_collected(value)

func collect_coin():
    coin_collected.emit(1)  # 只负责广播"我被吃了"
```

HUD 自己连接信号：

```gdscript
func _ready():
    player.coin_collected.connect(_on_coin_collected)

func _on_coin_collected(value):
    score += value
    score_label.text = str(score)
```

这样，金币不需要知道 HUD 在哪里，HUD 自己决定要不要听。这就是**松耦合**。

### 7.2 定义和发射自定义信号

```gdscript
# player.gd
extends CharacterBody2D

signal health_changed(new_value: int)
signal died

var health: int = 100:
    set(value):
        health = value
        health_changed.emit(health)
        if health <= 0:
            died.emit()

func take_damage(amount: int):
    health -= amount
```

> [!info] 属性设置器语法
> 上面的代码使用了 GDScript 4 的**属性设置器**语法。当 `health` 被赋值时，自动发射信号。这是一种很优雅的做法。

### 7.3 在编辑器中连接信号

Godot 允许你在编辑器中可视化地连接信号：

1. 选中发射信号的节点
2. 点击检查器右侧的 **"节点"** 标签页
3. 找到要连接的信号
4. 双击信号，选择接收方节点和方法
5. 编辑器自动生成连接代码

这种方式适合简单场景。对于复杂项目，推荐在代码中连接（7.4 节）。

### 7.4 在代码中连接信号

```gdscript
func _ready():
    # 方式一：直接连接
    button.pressed.connect(_on_button_pressed)
    
    # 方式二：连接时传递参数
    button.pressed.connect(_on_button_pressed.bind("extra_data"))
    
    # 方式三：使用 Callable
    var callback = func(): print("clicked!")
    button.pressed.connect(callback)

func _on_button_pressed():
    print("Button clicked!")
```

**断开连接**：

```gdscript
# 断开某个特定连接
button.pressed.disconnect(_on_button_pressed)

# 或者断开某个对象的所有连接
button.pressed.disconnect_all()
```

### 7.5 实战：用信号实现游戏逻辑

让我们实现一个完整的小游戏逻辑：

```gdscript
# game_manager.gd —— 全局游戏管理（使用 Autoload）
extends Node

signal score_changed(new_score: int)
signal game_over

var score: int = 0:
    set(value):
        score = value
        score_changed.emit(score)

func add_score(points: int):
    score += points

func end_game():
    game_over.emit()
```

在 **项目 → 项目设置 → 自动加载** 中添加 `game_manager.gd`，命名为 `GameManager`。这样它就成为一个全局可访问的单例：

```gdscript
# 在任何脚本中
func collect_coin():
    GameManager.add_score(10)
    queue_free()
```

```gdscript
# HUD 脚本
func _ready():
    GameManager.score_changed.connect(_on_score_changed)
    GameManager.game_over.connect(_on_game_over)

func _on_score_changed(new_score):
    $ScoreLabel.text = "分数: " + str(new_score)

func _on_game_over():
    $GameOverPanel.visible = true
```

### 本章小结

信号是 Godot 推荐的节点通信方式，它实现了松耦合。定义信号用 `signal`，发射用 `.emit()`，连接用 `.connect()`。Autoload 单例是全局游戏状态管理的利器。

### 第7章检查点：信号系统与节点通信

- [ ] 你能解释为什么信号比直接引用更好
- [ ] 你能定义自定义信号并发射它
- [ ] 你知道 Autoload 是什么以及它的用途
- [ ] 你成功用信号实现了两个节点之间的通信

---

## 第8章 动画系统

![第8章 视觉概览](chapter-08.svg)

> [!abstract]
> 角色站在那里一动不动，即使它在"走"。游戏需要动画来让一切生动起来。Godot 提供了强大的动画系统，从简单的属性变化到复杂的状态机，都能胜任。

### 8.1 AnimationPlayer：关键帧动画

`AnimationPlayer` 是 Godot 的核心动画工具。它可以对**任何节点的几乎任何属性**做动画。

使用方法：

1. 添加一个 `AnimationPlayer` 节点
2. 在底部动画面板中点击 **新建动画**
3. 设置动画时长和循环
4. 选中要动画的节点和属性，点击 **"钥匙"** 图标插入关键帧
5. 移动时间轴，修改属性，再插入关键帧

```gdscript
# 在代码中控制动画
$AnimationPlayer.play("fade_in")
$AnimationPlayer.play("attack", true)  # 第二个参数：是否混合
$AnimationPlayer.play_backwards("door_open")

# 检查动画是否播放完毕
if $AnimationPlayer.is_playing():
    print("正在播放")
```

### 8.2 AnimationTree：状态机与动画混合

当你有多个动画（idle、walk、run、jump），需要平滑地在它们之间切换。`AnimationTree` 就是做这个的。

设置步骤：

1. 创建 `AnimationPlayer`，做好所有动画
2. 添加 `AnimationTree` 节点
3. 在 `AnimationTree` 属性中指定 `anim_player` 为你的 AnimationPlayer
4. 设置 **Root Node** 为 `AnimationNodeStateMachine`
5. 在动画树编辑器中添加状态和转换条件

```gdscript
# 在代码中控制状态机
@onready var anim_tree = $AnimationTree

func _physics_process(delta):
    # 设置混合参数
    var speed_ratio = velocity.length() / max_speed
    anim_tree.set("parameters/BlendSpace2D/blend_position", 
                  Vector2(speed_ratio, 0))
    
    anim_tree.active = true
```

### 8.3 Tween：代码驱动的动画

`Tween` 适合用代码做简单的过渡动画：

```gdscript
# 让节点在 0.5 秒内移动到 (100, 200)
var tween = create_tween()
tween.tween_property(self, "position", Vector2(100, 200), 0.5)

# 链式调用：先移动，再缩放
var tween = create_tween()
tween.tween_property(self, "position", Vector2(100, 200), 0.5)
tween.tween_property(self, "scale", Vector2(2, 2), 0.3)

# 闪烁效果（受伤反馈）
var tween = create_tween()
tween.tween_property(sprite, "modulate", Color.RED, 0.1)
tween.tween_property(sprite, "modulate", Color.WHITE, 0.1)
tween.set_loops(3)  # 循环 3 次
```

### 8.4 实战：角色动画状态机

让我们做一个简单的角色动画系统：

```gdscript
extends CharacterBody2D

@onready var anim_sprite = $AnimatedSprite2D
@export var speed: float = 200.0

func _physics_process(delta):
    var input_dir = Vector2.ZERO
    input_dir.x = Input.get_axis("move_left", "move_right")
    input_dir.y = Input.get_axis("move_up", "move_down")
    
    velocity = input_dir.normalized() * speed
    move_and_slide()
    
    # 更新动画
    _update_animation(input_dir)

func _update_animation(direction: Vector2):
    if direction.length() > 0:
        # 根据移动方向翻转精灵
        if direction.x > 0:
            anim_sprite.flip_h = false
        elif direction.x < 0:
            anim_sprite.flip_h = true
        
        anim_sprite.play("walk")
    else:
        anim_sprite.play("idle")
```

> [!tip] 动画选择建议
> 对于简单的 2D 游戏，`AnimatedSprite2D` + 代码切换动画就够用了。只有当你需要复杂的动画混合（如 8 方向行走、跑跳过渡）时，才需要使用 `AnimationTree`。

### 本章小结

`AnimationPlayer` 对任何属性做关键帧动画。`AnimationTree` 管理多个动画的切换和混合。`Tween` 适合用代码做简单的过渡效果。对于简单 2D 游戏，`AnimatedSprite2D` 就够用。

### 第8章检查点：动画系统与状态机

- [ ] 你能用 AnimationPlayer 做一个简单的属性动画
- [ ] 你知道 Tween 适合什么场景
- [ ] 你能用代码切换 AnimatedSprite2D 的动画
- [ ] 你理解 AnimationTree 的作用是什么

---

## 第9章 UI 界面设计

![第9章 视觉概览](chapter-09.svg)

> [!abstract]
> 你需要显示分数、血条、菜单、对话框。Godot 的 UI 系统基于 `Control` 节点，功能强大但也容易让新手困惑。这一章帮你掌握 UI 的核心概念：锚点、容器、布局。

### 9.1 Control 节点体系

所有 UI 元素都是 `Control` 节点的子类：

| 节点 | 用途 |
|------|------|
| `Label` | 显示文字 |
| `Button` | 可点击按钮 |
| `TextureRect` | 显示图片 |
| `ProgressBar` | 进度条 / 血条 |
| `LineEdit` | 文字输入框 |
| `RichTextLabel` | 富文本（支持颜色、粗体等） |
| `Panel` | 背景面板 |
| `ColorRect` | 纯色矩形 |

`Control` 节点和 `Node2D` 不同——它不参与游戏世界的坐标系，而是使用**屏幕坐标系**（以像素为单位）。

### 9.2 锚点与布局系统

锚点决定了 `Control` 节点相对于其父容器的位置。

```
左上角锚点 (0, 0)          右上角锚点 (1, 0)
┌────────────────────────────┐
│                            │
│  元素位置 = 相对父容器      │
│  左上角的偏移量             │
│                            │
└────────────────────────────┘
左下角锚点 (0, 1)          右下角锚点 (1, 1)
```

在编辑器中设置锚点：
1. 选中 Control 节点
2. 点击视口上方的 **"锚点"** 按钮（一个十字图标）
3. 选择锚点预设（如"左上"、"居中"、"底部居中"等）

```gdscript
# 在代码中设置锚点
score_label.set_anchors_preset(Control.PRESET_TOP_LEFT)

# 手动设置锚点
score_label.anchor_left = 0.0
score_label.anchor_top = 0.0
score_label.anchor_right = 0.0
score_label.anchor_bottom = 0.0
```

### 9.3 容器节点：自动布局

手动设置锚点很灵活但很麻烦。容器节点可以自动排列子元素：

| 容器 | 排列方式 |
|------|---------|
| `VBoxContainer` | 垂直排列 |
| `HBoxContainer` | 水平排列 |
| `GridContainer` | 网格排列 |
| `MarginContainer` | 添加边距 |
| `CenterContainer` | 居中子元素 |
| `PanelContainer` | 带背景面板的容器 |

**实战：构建一个 HUD**

```
HUD (CanvasLayer)
└── MarginContainer
    └── VBoxContainer
        ├── HBoxContainer          ← 顶部信息栏
        │   ├── ScoreLabel
        │   └── HealthBar
        └── CenterContainer        ← 中央信息
            └── GameOverLabel
```

```gdscript
# hud.gd
extends CanvasLayer

@onready var score_label = $MarginContainer/VBoxContainer/HBoxContainer/ScoreLabel
@onready var health_bar = $MarginContainer/VBoxContainer/HBoxContainer/HealthBar
@onready var game_over_label = $MarginContainer/VBoxContainer/CenterContainer/GameOverLabel

func _ready():
    game_over_label.visible = false
    GameManager.score_changed.connect(_on_score_changed)
    GameManager.game_over.connect(_on_game_over)

func _on_score_changed(new_score: int):
    score_label.text = "分数: " + str(new_score)

func update_health(health: int, max_health: int):
    health_bar.value = float(health) / max_health * 100

func _on_game_over():
    game_over_label.text = "游戏结束!"
    game_over_label.visible = true
```

### 本章小结

UI 节点都是 `Control` 的子类，使用屏幕坐标系。锚点决定元素相对父容器的位置。容器节点（VBox、HBox 等）可以自动排列子元素，比手动锚点更方便。HUD 通常放在 `CanvasLayer` 中，这样它不会跟随游戏世界移动。

### 第9章检查点：UI 系统与布局

- [ ] 你能说出至少 5 种 Control 节点类型
- [ ] 你知道锚点系统的工作原理
- [ ] 你能使用 VBoxContainer 和 HBoxContainer 构建一个简单布局
- [ ] 你成功构建了一个包含分数和健康值的 HUD

---

## 第10章 导出与发布

![第10章 视觉概览](chapter-10.svg)

> [!abstract]
> 游戏做好了，但你还不能分享给别人。你需要把它"导出"成一个可执行文件。Godot 支持导出到 Windows、macOS、Linux、Web、Android、iOS 等平台。这一章带你走完最后一步。

### 10.1 导出模板下载

导出的第一步是安装导出模板：

1. 打开 **编辑器 → 管理导出模板**
2. 点击 **"下载并安装"**
3. 等待下载完成（约几百 MB）

导出模板是 Godot 为每个目标平台预编译的引擎二进制文件。没有它们，你无法导出。

### 10.2 配置导出预设

1. 打开 **项目 → 导出**
2. 点击 **"添加"**
3. 选择目标平台（如 "Windows Desktop"）
4. 配置选项：
   - **产品名称**
   - **图标**
   - **可执行文件名**
   - 平台特定选项

### 10.3 导出到 Windows、macOS、Linux

桌面平台导出最简单：

1. 添加对应平台的导出预设
2. 填写产品名称和图标
3. 点击 **"导出项目"**
4. 选择输出路径和文件名
5. 完成！你会得到一个 `.exe`（Windows）或 `.dmg`（macOS）或 `.x86_64`（Linux）文件

> [!warning] 跨平台导出注意事项
> macOS 导出只能在 macOS 上进行（由于系统限制）。Windows 和 Linux 可以在任何平台上交叉编译。

### 10.4 导出到 Web（HTML5）

Web 导出让你的游戏可以在浏览器中运行：

1. 添加 "Web" 导出预设
2. 导出后会得到多个文件：
   - `index.html` —— 入口页面
   - `index.wasm` —— WebAssembly 二进制
   - `index.js` —— JavaScript 加载器
   - `index.png` —— 图标
3. 把这些文件**全部上传**到同一个 Web 服务器目录
4. 服务器需要配置正确的 MIME 类型（`.wasm` 必须是 `application/wasm`）

> [!bug] Web 导出的限制
> Web 导出不支持某些功能（如线程、某些文件操作）。对于简单 2D 游戏通常没问题。

### 10.5 导出到移动端

Android 和 iOS 导出需要额外配置：

**Android**：
- 安装 Android SDK
- 在 Godot 编辑器设置中配置 Android SDK 路径
- 配置包名、签名证书
- 导出得到 APK 文件

**iOS**：
- 只能在 macOS 上导出
- 需要 Xcode
- 需要 Apple 开发者账号
- 导出后需要在 Xcode 中签名和打包

### 10.6 发布前检查清单

导出前确认：

- [ ] 游戏能正常运行，没有报错
- [ ] 所有资源文件（图片、音频）都已包含
- [ ] 主场景设置正确
- [ ] 窗口大小设置合理（项目设置 → 显示 → 窗口）
- [ ] 所有输入映射已配置
- [ ] 测试了所有平台特定的功能
- [ ] 导出模板已安装

### 本章小结

导出前需要安装导出模板。桌面平台导出最简单，设置预设后一键导出。Web 导出需要把所有文件放到 Web 服务器上。移动端需要额外配置 SDK 和证书。

### 第10章检查点：导出与发布流程

- [ ] 你知道导出模板是什么以及怎么安装
- [ ] 你成功导出了一次 Windows 或 Web 版本
- [ ] 你能列出导出前需要检查的事项

---

## 附录 A 常用快捷键

| 快捷键 | 功能 |
|--------|------|
| `F5` | 运行游戏 |
| `F6` | 运行当前场景 |
| `Ctrl+S` | 保存场景 |
| `Ctrl+Shift+S` | 保存所有 |
| `Ctrl+Z` | 撤销 |
| `Ctrl+Y` | 重做 |
| `Ctrl+D` | 复制节点 |
| `Delete` | 删除节点 |
| `Ctrl+T` | 创建新节点（弹出节点选择） |
| `Ctrl+Click` | 多选节点 |

## 附录 B 调试技巧

**print() 调试**：最简单的方法，在关键位置打印信息：

```gdscript
func _physics_process(delta):
    print("位置: ", position, " 速度: ", velocity)
```

**内置调试器**：底部面板的"调试器"标签页可以设置断点、单步执行、查看变量值。

**远程场景树检查**：运行游戏时，编辑器左侧的场景面板会切换到远程模式，显示运行时的节点树和属性值。

**print_tree()**：打印整个场景树结构：

```gdscript
func _ready():
    get_tree().print_tree()
```

## 附录 C 下一步学习路径

完成本教程后，你可以根据兴趣选择方向：

**2D 进阶**：
- TileMap 地图编辑器
- 寻路导航（NavigationAgent2D）
- 2D 灯光和阴影
- 着色器基础（Shader）

**3D 入门**：
- 3D 场景与节点
- 3D 材质与光照
- 3D 角色控制
- 3D 导入（Blender → Godot）

**游戏设计**：
- 关卡设计原则
- 游戏状态管理
- 存档系统
- 音频系统设计

**推荐资源**：
- Godot 官方文档：https://docs.godotengine.org
- Godot Demo Projects：https://github.com/godotengine/godot-demo-projects
- GDQuest 频道（YouTube）：https://www.youtube.com/@GDQuest
- Godot Learning：https://godotlearning.com
- 同一系列：[[独立游戏开发入门]] —— 从创意到可玩游戏的全局视角

---

## 参考资料

- Godot 官方文档 — https://docs.godotengine.org
- Godot Demo Projects (GitHub) — https://github.com/godotengine/godot-demo-projects
- GDQuest: Learn GDScript From Zero — https://www.gdquest.com
- Godot Learning Physics Guide — https://godotlearning.com
- KidsCanCode Godot 4 Recipes — https://kidscancode.org/godot_recipes/4.x/
