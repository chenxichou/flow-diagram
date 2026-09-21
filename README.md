# 空白模板 · Blank Flow Diagram Template

![License](https://img.shields.io/badge/license-MIT-green)
![Dependencies](https://img.shields.io/badge/dependencies-0-brightgreen)
![Single File](https://img.shields.io/badge/single%20file-HTML-blue)
![Offline](https://img.shields.io/badge/offline-%E5%8F%AF%E7%94%A8-success)

---

## 这是什么

**把想法变成可编辑的流程图。** 你描述想要什么图，拿到一份 JSON，导入这个模板就得到一张完整的、可以在浏览器里拖拽编辑的图。

JSON 怎么来都行 —— 让 AI 生成、自己手写、从其他工具导出，只要符合 JSON 结构就能导入。最常见的用法是把需求描述丢给 AI，几秒后拿到一张可编辑的图。

整个项目就一个 HTML 文件 —— 没有构建步骤、没有 npm install、没有后端、没有网络请求。双击打开就能用，发给别人也能用。

**适合**：业务流程、系统架构、数据流、状态机、阶段规划、多页签方案图集。

---

## 核心特性

- **单文件零依赖** —— 一个 `.html`，离线可用，拷走即用
- **AI 友好** —— 纯 JSON 数据结构，配色规则让 AI 输出自动保持视觉一致
- **多页签** —— 每页独立图数据，可自由增删、拖拽排序、双击改名
- **区域分组** —— 半透明彩色容器，充当泳道 / 域 / 子系统
- **四种节点形状** —— 矩形、圆形、菱形（审批判断）、六边形（外部系统）
- **三种连线样式** —— 曲线 / 直线 / 正交折线，可指定锚点、加文字备注
- **8 套主题** —— 浅色 / 孟菲斯 / 黏土软 3D / dark-glass / tech-blue / emerald-night / 暖调编辑 / 简洁仪表，实时切换
- **快捷键** —— 复制粘贴、撤销、中键平移无限画布
- **导出** —— JSON / PNG / GIF 动图
- **本地自动保存** —— 编辑后自动存浏览器，刷新不丢

---

## 30 秒上手

**三步走**：复制 prompt → 发给 AI 拿到 JSON → 导入模板，立刻得到一张可编辑的图。

### 第一步：复制 prompt，贴上你的内容，发给 AI

```
你是图表数据生成器。请输出一份 JSON，用于导入到 SVG 可编辑图编辑器。

【数据结构】
节点 node: {"id":"n1","x":100,"y":100,"w":130,"h":50,"name":"名称","sys":[],"color":"emerald","fill":"","borderStyle":"solid","zone":"","shape":"rect","linkTab":""}
区域 zone: {"id":"z1","x":80,"y":80,"w":400,"h":300,"label":"域名","color":"blue","fill":"","borderStyle":"solid"}
连线 flow: {"id":"f1","from":"n1","to":"n2","color":"emerald","fs":null,"ts":null,"style":"curve","curve":1,"note":""}
页签 tab:  {"id":"tab1","label":"页签名","desc":"","nodes":[],"flows":[],"zones":[],"hlines":[]}
根对象:    {"version":1,"appTitle":"标题","tabs":[tab,...]}

【枚举约束】
color/fill: emerald blue amber purple cyan pink red slate lime teal sky indigo violet fuchsia rose orange yellow | "none" | "#RRGGBB"
shape: rect | circle | rhombus(审批/判断) | hexagon
flow.style: curve | line | poly
flow.fs/ts: top | bottom | left | right | null
注意：节点名字段是 name，区域名字段是 label，不要混用。

【配色规则】
1. 节点的 fill 与其所属 zone 的 color 一致。没有 zone 的节点按语义角色填（判断 amber、结束 slate、异常 red）。
2. 连线 color 跟随 from 节点的颜色。异常/拒绝分支用 red。

【布局约束】
画布 1200×560，左上角为原点，x/y 是元素左上角坐标。
- 节点 130×50，间距 ≥ 40px。
- 区域包住子节点，标签占左上角 100×20，子节点别压。
- 所有元素在 0~1200 / 0~560 内。

【输出要求】
只输出一个 JSON 对象。不要 ```json 代码块，不要注释，不要任何解释文字。

【任务】
画一张系统架构图，展示下面这个系统的模块划分和调用关系。
- 用 zone 划分不同层级/模块，每个 zone 给不同 color
- 节点放在对应 zone 内，fill 与 zone color 一致
- 模块之间的调用关系用 flow 连接，note 写调用方式
- 外部系统用 shape:"hexagon"

--- 以下是系统描述，请根据它生成图 ---

这是一个在线电商系统：
- 前端有 Web 商城（React）和小程序（Taro）
- 后端有 API 网关（Kong）、用户服务、商品服务、订单服务
- 数据层有 MySQL、Redis 缓存、ElasticSearch 搜索
- 外部依赖：微信支付、顺丰 API
```

### 第二步：AI 返回纯 JSON，保存为 `.json` 文件

AI 会返回一段纯 JSON，把它存成 `xxx.json`。

> **要点**：必须是**纯 JSON**，不能有 \`\`\` 代码块或说明文字，否则导入会失败。

### 第三步：打开空白模板 → 导入 JSON

导入后立刻看到一张分层架构图：前端（蓝）→ 后端服务（绿）→ 数据层（黄），外部系统（紫）用六边形区分。可以继续拖拽、改名、加节点。

> **提示**：把 prompt 里「--- 以下是系统描述 ---」下面的内容换成你实际的内容，AI 就会根据它生成对应的图。不同场景的 prompt 写法见下方「使用示例」。

---

## 使用示例

以下场景覆盖最常见的使用方式。每个场景给出可直接复制的**任务块**，拼在通用块 A（即「30 秒上手」里那段 prompt）后面发给 AI 即可。

> JSON 来源不限于 AI 生成 —— 手写 JSON、从其他工具导出、或直接用 `test-jsons/` 里的示例文件，都能导入编辑。

### 场景 1 · 熟悉的系统，让 AI 画出架构图

你对系统已经很熟，想把脑子里的架构落成一张可分享的图。直接描述模块和关系，AI 帮你排版上色。

```
【任务】
画一张「{系统名}」的系统架构图。
- 用 zone 划分 3~5 个域（如 接入层 / 业务层 / 数据层 / 外部系统），每个 zone 给不同 color
- 每个域内放 2~5 个服务节点，color 和 fill 都与区域 color 一致
- 跨域调用用 flow 连接，style 用 "poly"（正交折线），note 写调用方式
- 外部系统用 shape:"hexagon" 区分

--- 以下是系统描述 ---

{描述你熟悉的系统：有哪些模块、怎么分层、外部依赖什么}
```

### 场景 2 · 首次接触的系统，让 AI 生成各类图帮你快速理解

拿到一个新项目 / 新仓库，面对一堆文档还没头绪 —— 让 AI 根据 README 或源码分别生成架构图、数据流图、状态机图，从不同维度建立认知。

**架构图**（先看全貌）：

```
【任务】
请根据下面这份项目文档，画一张系统架构图帮我快速了解全貌。
- 用 zone 划分不同层级，每个 zone 给不同 color
- 模块之间的调用关系用 flow 连接，note 写调用方式
- 外部系统用 shape:"hexagon"

--- 以下是项目文档 ---

{粘贴 README / 架构说明}
```

**数据流图**（追踪一个对象怎么流转）：

```
【任务】
请根据下面的文档，画一张数据流图，展示「{业务对象，如：订单}」经过哪些系统、产生哪些单据。
- 横向按阶段从左到右分 3~4 个 zone，zone 用不同 color
- 每个系统是一个节点，sys 字段填系统缩写
- 系统之间的数据传递用 flow，note 写单据名

--- 以下是业务文档 ---

{粘贴业务流程说明 / 单据流转文档}
```

**状态机图**（搞清对象的生命周期）：

```
【任务】
请根据下面的文档，画一张「{对象，如：订单}」的状态机图。
- 每个状态一个节点，shape 用 rect；初始 emerald，终态 slate，异常 red
- 状态迁移用 flow，note 写触发事件（如 "支付成功"、"超时关闭"）
- 状态数控制在 6~10 个

--- 以下是业务文档 ---

{粘贴状态说明 / 业务规则文档}
```

### 场景 3 · 业务流程文档 → 流程图

手里有一份业务文档（需求说明、审批制度、操作手册），想快速生成一张可编辑的流程图。

```
【任务】
画一张「{业务流程名}」的流程图。
- 5~10 个节点按主流程从左到右排列，用 curve 连线串起来
- 判断/审批环节用 shape:"rhombus"，普通步骤用 rect
- 判断节点出发两条 flow：通过 note "是"，拒绝 note "否" 且 color 用 red
- 关键连线上用 note 标注流转条件

--- 以下是业务描述 ---

{粘贴业务流程说明 / 需求文档 / 审批制度}
```

### 场景 4 · 阶段划分 —— 项目规划 / 里程碑

把项目按时间线拆成几个阶段，用阶段线横贯画布，每个阶段内放关键任务。

```
【任务】
画一张「{项目名}」的阶段划分图。
- 用 hlines 定义 3~5 个阶段，y 依次递增（如 120/240/360/480），label 写阶段名
- 每个阶段内放 2~4 个节点，y 落在该阶段区间内，不要压线
- 阶段内步骤用 flow 串联，跨阶段也用 flow 衔接
- 每个阶段用不同 color，节点 fill 与所在阶段颜色一致

--- 以下是项目计划 ---

{粘贴项目计划 / 里程碑说明}
```

### 场景 5 · 多页签图集 —— 大项目从总览到细节

项目比较大，一张图放不下。生成多个页签：总览页 + 各模块详情页，双击可跳转。

```
【任务】
为「{项目名}」生成一套多页签图集。
- tab1：全局总览（zone 分区，每域 1~2 个代表节点，linkTab 指向详情页签 id）
- tab2+：各域详情（6~10 个节点，含完整 flow）
- 总览页代表节点的 linkTab 填对应详情页签 id，实现双击跳转
- appTitle 填项目名，每个 tab 的 desc 写简要说明

--- 以下是项目描述 ---

{粘贴完整项目文档 / 多模块说明}
```

---

## 操作说明

| 操作 | 效果 |
|---|---|
| 拖动节点 / 区域 | 移动位置 |
| 双击节点 | 直接改名（居中弹窗，回车确认 / ESC 取消） |
| 双击区域标题 / 页签 | 改名 / 改备注 |
| 右键节点 | 菜单：跳转页签、从此连线、切形状、复制、删除 |
| 右键区域 | 菜单：节点排列（均匀分布 / 居中）、添加节点、删除 |
| 右键连线 | 菜单：备注、样式、颜色、删除 |
| **右键画布空白处** | 添加区域 / 添加节点（落在点击位置） |
| `Delete` | 删除选中项 |
| `Ctrl+C` / `Ctrl+V` / `Ctrl+Z` | 复制 / 粘贴 / 撤销 |
| 中键拖拽 | 平移画布（无限画布） |
| `−` `+` 或 `Ctrl/⌘ + 滚轮` | 缩放 |
| 顶部「导入 JSON / 导出」 | 数据进出（导出支持 JSON / PNG / GIF） |

---

## JSON 结构参考

导入时对文件**全文**做 `JSON.parse` —— 不能有注释、不能有 \`\`\` 代码块、不能有任何说明文字。

### 根对象

```json
{ "version": 1, "appTitle": "图标题", "tabs": [ ... ] }
```

### 页签 tab

```json
{ "id": "tab1", "label": "页签名", "desc": "备注", "nodes": [], "flows": [], "zones": [], "hlines": [] }
```

> `hlines` 可省略，导入时自动补 `[]`。

### 节点 node

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | ✅ | 全局唯一，建议 `n1 n2 …`（不要和 zone/flow 重号） |
| `x` `y` | ✅ | 左上角坐标 |
| `w` `h` | ✅ | 尺寸，建议 130×50 起步 |
| `name` | ✅ | **节点名用 `name`**（区域才用 `label`，别混） |
| `sys` | ⬜ | 系统标识 chips 数组，如 `["SCM","WMS"]`；不需要就 `[]` |
| `color` | ⬜ | **边框色**：17 个命名色 / `"none"` / `"#RRGGBB"` |
| `fill` | ⬜ | **填充色**：`""`=默认浅底 / 命名色 / `"none"`=透明 |
| `borderStyle` | ⬜ | `solid` 实线 / `dashed` 虚线 / `none` 无边框 |
| `zone` | ⬜ | 所属区域 id（仅视觉归属，不联动移动） |
| `shape` | ⬜ | `rect` / `circle` / `rhombus`(菱形) / `hexagon` |
| `linkTab` | ⬜ | 双击可跳转到的页签 id，不跳转留 `""` |

### 区域 zone

```json
{ "id": "z1", "x": 80, "y": 80, "w": 400, "h": 300, "label": "区域名", "color": "blue", "fill": "", "borderStyle": "solid" }
```

- 名称字段是 **`label`**（区别于节点的 `name`）
- 标签渲染在左上角约 `100×20`，**子节点别压上去**

### 连线 flow

| 字段 | 说明 |
|---|---|
| `from` `to` | 起止节点 id（必填） |
| `color` | 连线颜色，跟随 from 节点 |
| `fs` `ts` | 出/入锚点：`top` `bottom` `left` `right`；不指定写 `null` |
| `style` | `curve` 贝塞尔（默认）/ `line` 直线 / `poly` 正交折线 |
| `curve` | 曲率倍率 `0.2 ~ 2.5`，默认 1（仅 curve 生效） |
| `note` | 连线上的文字备注 |

### 阶段线 hline

```json
{ "id": "h1", "y": 110, "label": "阶段 1" }
```

横贯画布的虚线，只有 `y`（范围 0~560），渲染在区域之下。相邻 `y` 间隔 ≥ 90px。

### 三个最容易踩的坑

1. **节点名用 `name`，区域名用 `label`** —— 混用会导致名称空白
2. **`shape` 没有 `diamond` / `hex`** —— 菱形是 `rhombus`，六边形是 `hexagon`
3. **导入是整份替换** —— 会覆盖当前所有页签，已有图请先「导出 JSON」备份

---

## Prompt 模板库

### 通用块 A（每个 prompt 都要带）

```
你是图表数据生成器。请输出一份 JSON，用于导入到 SVG 可编辑图编辑器。

【数据结构】
节点 node: {"id":"n1","x":100,"y":100,"w":130,"h":50,"name":"名称","sys":[],"color":"emerald","fill":"","borderStyle":"solid","zone":"z1","shape":"rect","linkTab":""}
区域 zone: {"id":"z1","x":80,"y":80,"w":400,"h":300,"label":"域名","color":"blue","fill":"","borderStyle":"solid"}
连线 flow: {"id":"f1","from":"n1","to":"n2","color":"emerald","fs":null,"ts":null,"style":"curve","curve":1,"note":""}
页签 tab:  {"id":"tab1","label":"页签名","desc":"","nodes":[],"flows":[],"zones":[],"hlines":[]}
阶段线 hline(可选): {"id":"h1","y":110,"label":"阶段 1"}   横贯画布的虚线，y 范围 0~560，相邻间隔 ≥90
根对象:    {"version":1,"appTitle":"标题","tabs":[tab,...]}

【枚举约束】
color/fill: emerald blue amber purple cyan pink red slate lime teal sky indigo violet fuchsia rose orange yellow | "none" | "#RRGGBB"
borderStyle: solid | dashed | none
shape: rect | circle | rhombus(审批/判断) | hexagon
flow.style: curve | line | poly
flow.fs/ts: top | bottom | left | right | null
注意：节点名字段是 name，区域名字段是 label，不要混用。

【配色规则】
1. 节点的 fill 必须与其所属 zone 的 color 一致。没有 zone 的节点按语义角色填（判断 amber、结束 slate、异常 red）。
2. 连线 flow 的 color 跟随起始节点（from）的颜色。例外：异常/拒绝/回退分支统一用 "red"。
3. 区域的 color 和其中节点的 fill 保持同色系，让同一域内的元素视觉归属清晰。

【布局约束】
画布 1200×560，左上角为原点，x/y 是元素左上角坐标。
- 节点用 130×50；节点之间间距 ≥ 40px。
- 区域要完整包住其子节点：zone.x + 20 ≤ node.x 且 node.x + node.w ≤ zone.x + zone.w - 20（y 方向同理）。
- 区域标签在左上角占约 100×20，子节点不要压在上面。
- 所有元素必须落在 0~1200 / 0~560 范围内。
- 若用阶段线 hlines：y 间隔 ≥ 90px，且不要横穿节点。

【输出要求】
只输出一个 JSON 对象。不要 ```json 代码块，不要注释，不要任何解释文字。
```

### 任务块 B1 — 业务流程图

```
任务：画一张「{填入业务流程名}」的流程图。
- 用 tab1，label 填流程名，appTitle 同名。
- 5~10 个节点按主流程顺序从左到右、从上到下排列，用 curve 连线串起来。
- 判断/审批环节用 shape:"rhombus"，普通步骤用 rect。
- 关键连线上用 note 标注流转条件。
```

### 任务块 B2 — 系统架构图（分区）

```
任务：画一张「{系统名}」的系统架构图。
- 先用 zone 划分 3~5 个域（如 接入层/业务层/数据层/外部系统），每个 zone 给不同 color。
- 每个域内放 2~5 个服务节点，color 和 fill 都与区域 color 一致。
- 跨域调用用 flow 连接，style 用 "poly"，并指定 fs/ts 锚点。
- 数据流向在 note 里写清楚。外部系统用 shape:"hexagon"。
```

### 任务块 B3 — 数据流图

```
任务：画一张「{业务对象}」的数据流图。
- 横向按阶段从左到右分 3~4 个 zone，zone 用不同 color。
- 每个系统是一个节点，sys 字段填系统缩写。
- 系统之间的数据传递用 flow，note 写单据名。
```

### 任务块 B4 — 状态机图

```
任务：画一张「{对象}」的状态机图。
- 每个状态一个节点，shape 用 rect；初始 emerald，终态 slate，异常 red。
- 状态迁移用 flow，note 写触发事件。
- 自环迁移也建 flow（from===to）。
- 布局成环形或网格，状态数 6~10 个。
```

### 任务块 B5 — 审批 / 决策流程

```
任务：画一张「{审批事项}」的审批流程图。
- 判断节点（菱形 shape:"rhombus"）写清判断条件。
- 通过分支 note "是"，拒绝分支 note "否" 且 color 用 red。
- 不同审批角色用不同 color 区分。结束节点用 slate。
- 整体自上而下，垂直间距 ≥ 60px。
```

### 任务块 B6 — 多页签图集

```
任务：为「{项目名}」生成多页签图集。
- tab1：全局总览（zone 分区，每域 1~2 个代表节点，linkTab 指向详情页签 id）。
- tab2+：各域详情（6~10 个节点，含完整 flow）。
- appTitle 填项目名，每个 tab 的 desc 写简要说明。
```

### 任务块 B7 — 阶段划分图

```
任务：画一张「{项目/流程}」的阶段划分图。
- 用 hlines 定义 3~5 个阶段，y 依次递增（如 120/240/360/480），label 写阶段名。
- 每个阶段内放 2~4 个节点，y 落在该阶段区间内，不要压线。
- 阶段内步骤用 flow 串联，跨阶段也用 flow 衔接。
- 每个阶段用不同 color，节点 fill 与所在阶段颜色一致。
```

### 拼装方式

**通用块 A + 任务块 B** 拼成一段发给 AI 即可。

例如：复制通用块 A，在末尾追加任务块 B1（把 `{}` 替换为实际名称），整段发给 AI。

---

## 配色与布局

### 17 个命名色

| 键 | 色值 | 键 | 色值 | 键 | 色值 |
|---|---|---|---|---|---|
| `emerald` | #22C55E | `blue` | #3B82F6 | `amber` | #F59E0B |
| `purple` | #A855F7 | `cyan` | #06B6D4 | `pink` | #EC4899 |
| `red` | #EF4444 | `slate` | #94A3B8 | `lime` | #84CC16 |
| `teal` | #14B8A6 | `sky` | #0EA5E9 | `indigo` | #6366F1 |
| `violet` | #8B5CF6 | `fuchsia` | #D946EF | `rose` | #F43F5E |
| `orange` | #F97316 | `yellow` | #EAB308 | | |

- `"none"` = 透明；也可直接写 `"#RRGGBB"`

### 配色规则

1. 节点 `fill` 与其所属 zone 的 `color` 一致。没有 zone 的节点按语义角色填（判断 `amber`、结束 `slate`、异常 `red`）。
2. 连线 `color` 跟随 from 节点。异常/拒绝分支统一用 `red`。
3. 同一 zone 内的节点 fill 和连线 color 保持同色系。

### 布局约束

- 节点 `130×50`，间距 ≥ 40px
- 区域完整包住子节点：`zone.x + 20 ≤ node.x`，`node.x + node.w ≤ zone.x + zone.w - 20`（y 同理）
- 区域标签占左上角 `100×20`，子节点别压
- 所有元素在 `0~1200 / 0~560` 内
- 阶段线 `y` 间隔 ≥ 90px，不横穿节点

---

## 主题

顶部下拉实时切换，选择会被记住。切换主题时界面和图中元素配色同步更新。

| 孟菲斯 | 黏土软 3D |
|---|---|
| ![孟菲斯](screenshots/theme-memphis.png) | ![黏土软3D](screenshots/theme-clay-soft.png) |

| dark-glass | tech-blue |
|---|---|
| ![dark-glass](screenshots/theme-dark-glass.png) | ![tech-blue](screenshots/theme-tech-blue.png) |

---

## 文件结构

```
.
├── flow-diagram.html                # 主程序：单文件编辑器（打开这个）
├── README.md                        # 本文档
└── screenshots/                     # 配图
```

---

## 协议

[MIT](LICENSE) —— 可自由使用、修改、分发，保留版权声明即可。
