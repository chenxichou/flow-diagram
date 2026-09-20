# 空白模板.html — JSON 结构与 Prompt 模板

> 适用对象：`空白模板.html`（单文件零依赖 SVG 可编辑图编辑器，MIT）
> 用途：让 AI 直接产出可「导入 JSON」的图数据，一导入就是一张完整可编辑的图。

---

## 零、两种使用方式

| 方式 | 适用 | 步骤 |
|---|---|---|
| **方式一 · 提示词**（轻、可控） | 自己知道要什么图，想逐项把控细节 | 用「第六章」的提示词模板拼一段发给任意 AI → 拿到 JSON → 打开空白模板 → 右上角「导入 JSON」 |
| **方式二 · skill**（省事、全流程） | 给了文档/源码/Mermaid，或只说「帮我做成图」 | 直接用 skill **`flow-diagram`**，它自动走完「抽取结构 → 生成 JSON → 导入空白模板」 |

**方式二（推荐）** 的 skill 位于 `~/.workbuddy/skills/flow-diagram/`，触发说法举例：
「画一张 XX 流程图」「把这个文档转成架构图」「把这段 mermaid 变成可编辑的图」「把这个项目的模块依赖画出来」。

两种方式的**产物完全一致**：一份 JSON + 同一个空白模板。区别只在于 JSON 是谁写的
（你自己用提示词，还是 skill 代劳）。

---

## 一、导入 / 导出通道

| 通道 | 位置 | 说明 |
|---|---|---|
| **导入 JSON** | 右上角「导入 JSON」按钮 | 对文件全文做 `JSON.parse` → **必须是纯 JSON** |
| **导出 JSON** | 导出菜单 → JSON | 产出 `{version:1, appTitle, tabs:[...]}`，**含阶段线 `hlines`** |
| **本地自动保存** | 编辑后 2.5s 自动存 / 刷新恢复 | localStorage 键 `scm_template_v1`，版本 `v:2` |

⚠️ **导入对格式零容忍**：文件里只要出现 ```json 代码块、说明文字、注释，`JSON.parse` 就会失败并提示「导入失败：格式错误」。所以给 AI 的 prompt 必须明确要求**只输出裸 JSON**。

### 支持的三种输入形态

1. **多页签（推荐）** — `{ "version": 1, "tabs": [ ... ] }`，tab 内可带 `hlines`
2. **单页签简写** — `{ "nodes": [...], "flows": [...], "zones": [...], "hlines": [...] }`（合入当前页签）
3. **本地存档** — `{ "v": 2, ... }`（仅供理解，不要手写成这种去导入）

导入后会自动做一次归一化：缺失的 `hlines` 补成 `[]`、`sys` 是字符串时转数组、`flow.style` 缺省补 `curve`、`linkTab` 缺省补 `""`。所以**旧版导出的文件照样能正常导入**。

---

## 二、画布与坐标系

- 画布 `viewBox = 0 0 1200 560`，原点在**左上角**，x 向右、y 向下。
- `x/y` 一律是元素**左上角**坐标（不是中心）。
- 节点默认 `130×50`，区域默认 `200×150`。
- 超出 1200×560 的部分在页面上不可见（画布有滚动/缩放，但导 PNG 会截掉）。

---

## 三、完整 JSON 结构

### 3.1 根对象

```json
{
  "version": 1,
  "appTitle": "图标题（可选，双击页内标题可改）",
  "tabs": [ /* tab 数组，至少一个 */ ]
}
```

> 本地存档（v2）额外还有 `savedAt / activeTab / nextId / animationOn`，导入时可省略。

### 3.2 页签 tab

```json
{
  "id": "tab1",
  "label": "页签名",
  "desc": "页签备注（双击页签可编辑）",
  "nodes": [],
  "flows": [],
  "zones": [],
  "hlines": []
}
```

> `hlines` 可省略，导入时会自动补成 `[]`。

### 3.3 节点 node

```json
{
  "id": "n1",
  "x": 100, "y": 100,
  "w": 130, "h": 50,
  "name": "节点名称",
  "sys": ["SCM"],
  "color": "emerald",
  "fill": "",
  "borderStyle": "solid",
  "zone": "z1",
  "shape": "rect",
  "linkTab": ""
}
```

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
| `zone` | ⬜ | 所属区域 id（只在视觉上归属，不自动跟随移动） |
| `shape` | ⬜ | `rect` 矩形 / `circle` 圆形 / `rhombus` 菱形(审批) / `hexagon` 六边形 |
| `linkTab` | ⬜ | 双击可跳转到的页签 id，不跳转留 `""` |

### 3.4 区域 zone（分组容器，对应"泳道/域"）

```json
{
  "id": "z1",
  "x": 80, "y": 80,
  "w": 400, "h": 300,
  "label": "区域名",
  "color": "blue",
  "fill": "",
  "borderStyle": "solid"
}
```

- 名称字段是 **`label`**（区别于节点的 `name`）。
- 区域标签渲染在左上角，约占 `100×20`，**子节点别压上去**。
- 区域不参与连线，`zone` 只是标记归属。

### 3.5 连线 flow

```json
{
  "id": "f1",
  "from": "n1",
  "to": "n2",
  "color": "emerald",
  "fs": "right",
  "ts": "left",
  "style": "curve",
  "curve": 1,
  "note": "连线备注"
}
```

| 字段 | 说明 |
|---|---|
| `from` `to` | 起止节点 id（必填） |
| `color` | 连线颜色，同节点配色规则 |
| `fs` `ts` | 出/入锚点：`top` `bottom` `left` `right`；不指定写 `null`（自动贴边） |
| `style` | `curve` 贝塞尔曲线（默认）/ `line` 直线 / `poly` 正交折线 |
| `curve` | 曲率倍率 `0.2 ~ 2.5`，默认 1，越大越弯（仅 curve 生效） |
| `note` | 连线上的文字备注 |

### 3.6 阶段线 hline（✅ 已支持导出/导入）

```json
{ "id": "h1", "y": 110, "label": "阶段 1" }
```

| 字段 | 说明 |
|---|---|
| `id` | 唯一，建议 `h1 h2 …` |
| `y` | 横线所在的**垂直位置**（只有 y，横跨整个画布宽度），范围 0~560 |
| `label` | 阶段名，显示在横线左端 |

- 阶段线是一条**横贯画布的虚线**，用来划分阶段/时期，渲染在区域之下。
- 页面里可用工具栏「阶段线」按钮手动添加；现在也能通过 JSON 批量生成。
- 建议相邻阶段线的 `y` 间隔 ≥ 90px，避免标签挤在一起。

---

## 四、颜色与枚举速查

**17 个命名色**（`color` / `fill` / flow `color` 通用）：

| 键 | 色值 | 键 | 色值 | 键 | 色值 |
|---|---|---|---|---|---|
| `emerald` | #22C55E | `blue` | #3B82F6 | `amber` | #F59E0B |
| `purple` | #A855F7 | `cyan` | #06B6D4 | `pink` | #EC4899 |
| `red` | #EF4444 | `slate` | #94A3B8 | `lime` | #84CC16 |
| `teal` | #14B8A6 | `sky` | #0EA5E9 | `indigo` | #6366F1 |
| `violet` | #8B5CF6 | `fuchsia` | #D946EF | `rose` | #F43F5E |
| `orange` | #F97316 | `yellow` | #EAB308 | | |

- `"none"` = 无颜色（透明）
- 也可直接写十六进制字符串，如 `"#22C55E"`

**其他枚举**：`borderStyle` = `solid`|`dashed`|`none`；`shape` = `rect`|`circle`|`rhombus`|`hexagon`；`style` = `curve`|`line`|`poly`；`fs/ts` = `top`|`bottom`|`left`|`right`|`null`。

---

## 五、让 AI 生成"不重叠"的布局约束

把这段塞进 prompt，出图质量会明显变好：

```
画布 1200×560，左上角为原点，x/y 是元素左上角坐标。
- 节点用 130×50；节点之间水平和垂直间距都 ≥ 40px。
- 区域要完整包住其子节点：zone.x + 20 ≤ node.x 且 node.x + node.w ≤ zone.x + zone.w - 20（y 方向同理）。
- 区域标签在左上角占约 100×20，子节点不要压在上面。
- 所有元素必须落在 0~1200 / 0~560 范围内。
- 节点数量 ≤ 12 时单行/双行排布，> 12 时分区域分组。
```

---

## 六、Prompt 模板

### 通用块 A（每个 prompt 都要带，复制一次即可）

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

【布局约束】
画布 1200×560，左上角为原点，x/y 是元素左上角坐标。
- 节点用 130×50；节点之间水平和垂直间距都 ≥ 40px。
- 区域要完整包住其子节点：zone.x + 20 ≤ node.x 且 node.x + node.w ≤ zone.x + zone.w - 20（y 方向同理）。
- 区域标签在左上角占约 100×20，子节点不要压在上面。
- 所有元素必须落在 0~1200 / 0~560 范围内。
- 若用阶段线 hlines：y 间隔 ≥ 90px，且阶段线不要横穿节点（节点上下边与阶段线 y 至少差 30px）。

【输出要求】
只输出一个 JSON 对象。不要 ```json 代码块，不要注释，不要任何解释文字。
```

### 任务块 B1 — 业务流程图

```
任务：画一张「{填入业务流程名，如：电商订单履约流程}」的流程图。
- 用 tab1，label 填流程名，appTitle 同名。
- 5~10 个节点按主流程顺序从左到右、从上到下排列，用 curve 连线串起来。
- 判断/审批环节用 shape:"rhombus"，普通步骤用 rect。
- 关键连线上用 note 标注流转条件。
- 配色：主流程 emerald，异常/回退分支 red，判断节点 amber。
```

### 任务块 B2 — 系统架构图（分区）

```
任务：画一张「{系统名}」的系统架构图。
- 先用 zone 划分 3~5 个域（如 接入层/业务层/数据层/外部系统），每个 zone 给不同 color。
- 每个域内放 2~5 个服务节点，节点的 zone 字段填所属区域 id，color 与区域一致。
- 跨域调用用 flow 连接，style 用 "poly"（正交折线更像架构图），并指定 fs/ts 锚点。
- 数据流向在 note 里写清楚（如 "HTTP 调用"、"MQ 异步"）。
- 外部系统用 shape:"hexagon" 区分。
```

### 任务块 B3 — 数据流 / 供应链 SCM 数据流动图

```
任务：画一张「{业务对象，如：采购订单}」的数据流图，展示它经过哪些系统、产生哪些单据。
- 横向按时间/阶段从左到右分 3~4 个 zone（如 采购/仓储/财务），zone 用不同 color。
- 每个系统是一个节点，sys 字段填系统缩写（如 ["SCM"] ["WMS"] ["ERP"]）。
- 系统之间的数据传递用 flow，note 写单据名（如 "采购单"、"入库单"、"应付凭证"）。
- 节点 name 写系统名，需要时在括号里补动作。
- 主数据流用 emerald，回写/对账流用 blue 虚线以外的实线，异常用 red。
```

### 任务块 B4 — 状态机图

```
任务：画一张「{对象}」的状态机图。
- 每个状态一个节点，shape 用 rect；初始状态用 emerald，终态用 slate，异常态用 red。
- 状态迁移用 flow 连接，note 必须写触发事件（如 "支付成功"、"超时关闭"）。
- 自环迁移（回到自身）也建一条 from===to 的 flow，note 写事件。
- 布局成环形或网格，避免连线互相穿过：同一层的状态 y 相同，相邻层 x 递增 200。
- 状态数控制在 6~10 个。
```

### 任务块 B5 — 审批 / 决策流程（菱形判断）

```
任务：画一张「{审批事项}」的审批流程图。
- 判断节点（菱形 shape:"rhombus"）写清判断条件，如 "金额 > 10万?"。
- 从判断节点出发两条 flow：通过分支 note 写 "是"，拒绝分支 note 写 "否"，拒绝分支 color 用 red。
- 审批动作节点用 rect，不同审批角色用不同 color 区分。
- 结束节点用 slate，name 写 "通过" / "驳回"。
- 整体自上而下排布，节点垂直间距 ≥ 60px 给 note 留空间。
```

### 任务块 B6 — 多页签图集（分域分系统）

```
任务：为「{项目名}」生成一套多页签图集，每个页签一张图。
- tab1：全局总览（各域用 zone 分区，域内只放 1~2 个代表节点，跨页签跳转用 linkTab 指向对应页签 id）。
- tab2、tab3…：每个域的详细流程（节点数 6~10，含完整 flow）。
- 总览页里代表节点的 linkTab 填对应详情页签的 id，实现双击跳转。
- appTitle 填项目名，每个 tab 的 desc 写该页的简要说明。
```

### 任务块 B7 — 阶段划分图（用阶段线）

```
任务：画一张「{项目/流程}」的阶段划分图，用阶段线把画布横向切成几个时期。
- 先用 hlines 定义 3~5 个阶段，y 依次递增（如 120 / 240 / 360 / 480），label 写阶段名（如 "需求期"、"开发期"、"上线期"）。
- 每个阶段带内放 2~4 个节点，节点的 y 要落在该阶段线的下方区间内，不要压线。
- 阶段内的步骤用 flow 串联，跨阶段的衔接也用 flow 连起来。
- 每个阶段用不同的 color 区分（如 emerald / blue / amber / purple）。
- 不需要 zone 时可以不要区域，用阶段线划分即可。
```

---

## 七、拼装好的完整示例（可直接复制用）

### 示例 1：生成订单履约流程图

> 把下面整段直接发给 AI（= 通用块 A + 任务块 B1）

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

【布局约束】
画布 1200×560，左上角为原点，x/y 是元素左上角坐标。
- 节点用 130×50；节点之间水平和垂直间距都 ≥ 40px。
- 区域要完整包住其子节点：zone.x + 20 ≤ node.x 且 node.x + node.w ≤ zone.x + zone.w - 20（y 方向同理）。
- 区域标签在左上角占约 100×20，子节点不要压在上面。
- 所有元素必须落在 0~1200 / 0~560 范围内。
- 若用阶段线 hlines：y 间隔 ≥ 90px，且阶段线不要横穿节点（节点上下边与阶段线 y 至少差 30px）。

【输出要求】
只输出一个 JSON 对象。不要 ```json 代码块，不要注释，不要任何解释文字。

【任务】
画一张「电商订单履约流程」的流程图。
- 用 tab1，label 填流程名，appTitle 同名。
- 5~10 个节点按主流程顺序从左到右、从上到下排列，用 curve 连线串起来。
- 判断/审批环节用 shape:"rhombus"，普通步骤用 rect。
- 关键连线上用 note 标注流转条件。
- 配色：主流程 emerald，异常/回退分支 red，判断节点 amber。
```

### 示例 2：生成系统架构图

把「示例 1」里【任务】那一段替换为「任务块 B2」的内容即可。其余通用块 A 完全不变。

---

## 八、最小可导入样例（自检用）

存成 `test.json` 导入，能立刻看到效果：

```json
{
  "version": 1,
  "appTitle": "最小样例",
  "tabs": [
    {
      "id": "tab1",
      "label": "示例",
      "desc": "",
      "zones": [
        { "id": "z1", "x": 80, "y": 90, "w": 460, "h": 260, "label": "订单域", "color": "emerald", "fill": "", "borderStyle": "solid" }
      ],
      "nodes": [
        { "id": "n1", "x": 130, "y": 170, "w": 130, "h": 50, "name": "下单", "sys": ["SCM"], "color": "emerald", "fill": "", "borderStyle": "solid", "zone": "z1", "shape": "rect", "linkTab": "" },
        { "id": "n2", "x": 340, "y": 170, "w": 130, "h": 50, "name": "支付", "sys": ["PAY"], "color": "blue", "fill": "", "borderStyle": "solid", "zone": "z1", "shape": "rect", "linkTab": "" },
        { "id": "n3", "x": 235, "y": 280, "w": 130, "h": 50, "name": "库存充足?", "sys": [], "color": "amber", "fill": "", "borderStyle": "solid", "zone": "z1", "shape": "rhombus", "linkTab": "" }
      ],
      "flows": [
        { "id": "f1", "from": "n1", "to": "n2", "color": "emerald", "fs": "right", "ts": "left", "style": "curve", "curve": 1, "note": "创建支付单" },
        { "id": "f2", "from": "n1", "to": "n3", "color": "amber", "fs": "bottom", "ts": "top", "style": "poly", "curve": 1, "note": "校验库存" }
      ],
      "hlines": [
        { "id": "h1", "y": 130, "label": "下单阶段" },
        { "id": "h2", "y": 380, "label": "履约阶段" }
      ]
    }
  ]
}
```

---

## 九、已知限制（重要）

1. **导入不做字段校验** —— id 重复、`from/to` 指向不存在的节点，不会报错，但会导致渲染异常（连线消失/节点选中错乱）。让 AI 生成时务必强调 id 唯一。
2. **`zone` 只是标记，不联动** —— 拖动区域时内部节点不会跟着走；反过来节点拖出区域，`zone` 值也不变。
3. **导入是整份替换** —— 导入 JSON 会覆盖当前所有页签，不会合并（导入后会立即落盘保存）。先导出备份再导入。
4. **不支持 JSON 注释** —— 任何 `//` 或 `/* */` 都会让解析失败。
5. **阶段线只有 `y` 没有 `x`** —— 它横贯整个画布宽度，无法做成垂直的阶段分隔线。
