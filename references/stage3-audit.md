# 阶段 3 · 规范审计（规程，两种模式共用）

> 本规程被两处调用：**pre-gen 模式**（阶段 3，审计"想法/方案"）与 **post-gen 模式**（阶段 4 生成后，审计开工包成品）。审计铁律：**不凭记忆**——每条结论必须标注出处；**不产出审计日志文件**——问题直接改正文。

## 0. 事实源（审计前必读，禁止跳过）

| 事实源 | 读取方式 |
|---|---|
| 林枢编码指引 00~08 | Gitee raw：`https://gitee.com/wordy_huang/linhub/raw/master/coding-guide/<文件名>`（master 分支，跟随重定向） |
| 宿主服务契约 | Gitee raw：`.../raw/master/code/host/linhub/host/src/services/services.php` |
| 分导台定义与已装包菜单快照 | Gitee raw：`.../raw/master/code/host/linhub/host/workspaces.php`（六分导台）与 `.../raw/master/code/host/data/integration/registry.json`（已装包 menus.php 快照，菜单结构核对用） |
| workdesktop-ai | **Gitee raw 主源**（分支 **main**，需跟随重定向）：`https://gitee.com/wordy_huang/workdesktop-ai/raw/main/<path>`；GitHub 镜像 `https://github.com/wordyhuang/workdesktop-ai`。**按 `ai-router.md` 场景路由读取，禁止凭名猜测 API**：① `ai-router.md`（场景判定速查表，AI 第一入口）② `scenarios/00-common.md`（通用地基：框架搭建 / 弹层选型 / 操作反馈 / 按钮选型）③ 命中场景子文件 `scenarios/0N-*.md` ④ 组件精确 API `ai-api/wd-{组件名kebab}.md`（34 组件，路径可按组件名直接构造）⑤ `ai-api/use-request-guide.md`（请求流程 / 配置全景 / 排错）。版本以仓库 `README.md`（V_1.5）与 `ai-router.md`（V_1.15）为准 |
| SOP 规则 | skill 内嵌 `references/sop-v1.8.md` |

## 1. pre-gen 模式（阶段 3 · 审计想法/方案）

**审计对象**：《需求确认单》+《技术方案调研报告》中的推荐方案。

| # | 审计项 | 合规标准（出处） |
|---|--------|-----------------|
| P1 | 命名与隔离 | 表前缀 `lg_{SHORT}_`（{SHORT} 原样完整保留含连字符，如 `lg_lin-crm_`；禁止替换为下划线）、命名空间 `Lg\Pack\<PascalCase(short)>`、事件主题 `lg.<short>.`、静态目录 `public/lgg/<short>/`（SOP §8.2.3 命名与隔离表） |
| P2 | 宿主复用优先 | 方案中判 ✅ 宿主已提供的能力**不得安排自建**；T0 不重复建 RBAC/菜单/事件总线/迁移引擎（SOP §8.2.1、红线 6、A5 ③） |
| P3 | 前端形态 | 选林枢时前端固定 workdesktop-ai + 静态页面 + 标准 API 驱动 + UMD 直引无构建链（**不使用模板标签**，数据一律经统一 request 请求标准 API）；对照 F-01~F-12 逐条预检方案不与之冲突（SOP §8.2.4） |
| P4 | 技术栈不偏离 | PHP 8.0+ / ThinkPHP 8.x / MySQL（注意 5.5 语法限制）/ Redis Stream（SOP §8.2.2） |
| P5 | 需求可实现性 | 需求确认单各项在推荐方案中有承载；Out-of-Scope 项不被方案偷偷实现 |

**门禁 G3**：任一项不通过 → 修正方案（必要时回阶段 1/2）后重审；全部通过 → 反馈审计结论给用户并更新调研报告修订版，放行阶段 4。**不产出独立审计记录文件。**

## 2. post-gen 模式（阶段 4 生成后 · 审计开工包成品）

**两段执行，顺序固定：**

### 2.1 SOP 六维审计（SOP 流程内，照 SOP 原文执行）

按 `sop-v1.8.md` §5 执行 A1~A6 六维审计，结论写入 TASK-CHECKLIST 副本的审计记录区（附录 A.2）——这是 SOP 流程内步骤，不算额外审计日志。

### 2.2 规范审计（skill 层，对照社区事实源逐条核成品）

| # | 审计项 | 核对内容 |
|---|--------|---------|
| C1 | 后端合规 | 文件 3 的 T0 范围、服务声明、事件主题前缀、目录约定对照 `coding-guide/02_后端编码指引.md`、`04_功能包开发指引.md`；**API 响应 code 语义符合约定（`{code,data,message}`，≥0 成功 / 失败一律负数 code，禁止正数或 0 表示失败——SOP §8.2.2 API 响应格式行、文件 3 §2 统一响应层任务）** |
| C2 | 数据合规 | 文件 2 表前缀、迁移方式对照 `coding-guide/05_数据与迁移指引.md` |
| C3 | 前端合规 | 文件 4 组件映射、F-01~F-12 满足方式对照 `coding-guide/03_前端编码指引.md` + workdesktop-ai 实际组件能力；**组件名与 props/emits/slots 必须能逐一在 `ai-api/wd-{组件名}.md` 查到（禁止凭记忆或猜测组件/参数名，查不到的判虚构）** |
| C4 | 服务映射真实性 | 文件 6 服务映射三态结论对照 `services.php` 实际清单，禁止虚构服务 id |
| C5 | 框架口径 | 六份 front matter `framework`/`frontend_build` 三处一致（SOP 红线 6、A5） |
| C6 | 页面规格覆盖 | 文件 4 §1 每个页面均附页面规格卡，内容 / 布局 / 元素 / 操作四层齐备；逐页核对：无"等"字省略、无规格外自创元素与交互、推导项带"推导，待评审"标注、页面任务回链 PG-ID（SOP §2.4 文件 4 §1/§2、TASK-10 判据） |
| C7 | 编码规范声明 | 六份文件 §0 + `TASK-CHECKLIST.md` 头部逐字含 SOP 公共要求 7 的编码规范遵循声明（指向 linhub coding-guide 00~08 + 社区仓库地址），front matter `coding_guide: linhub/coding-guide` 齐备，§0.1 会话规约含开工前阅读提醒——共 7 处，缺一或改写即不通过（SOP §2.3 第 7 条、A1 ⑤） |
| C8 | 页面 UMD 范式（机器扫描） | 对 `src/view/` 下全部 `.html` 执行 §2.3 十二项静态扫描，全部 PASS 才通过；与「四级对齐」检查并列，同为页面交付前必过项（细则见 §2.3，规范出处：coding-guide/03 §三、04 §7.2） |
| C9 | 菜单结构符合性（人工核对） | `menus.php` 对照 `coding-guide/04_功能包开发指引.md` §6.2「宿主渲染规则与 group 聚合分组」逐条比对：工作区归属、group 值、children 挂载、perms 并集（细则见 §2.4，与 C8、四级对齐并列，同为交付前必过项） |

### 2.3 页面 UMD 范式检查（机器可执行静态扫描，交付前必过）

- **扫描对象**：`src/view/` 下全部 `.html`；
- **判定规则**：12 项全部 PASS → 该页面 PASS；任何一项 FAIL → 该页面整体 FAIL，**输出页面名 + 缺失项清单**；全部页面 PASS → 本项通过；
- **规范出处**：`coding-guide/03_前端编码指引.md` §三（页面骨架）、`coding-guide/04_功能包开发指引.md` §7.2（包页面 GUI 渲染规范——"唯一正确模式"）。

| # | 检查项 | 规则 |
|---|--------|------|
| 1 | 双 CSS · element-plus | 含 `<link rel="stylesheet" ... element-plus/index.css">` 恰 1 条 |
| 2 | 双 CSS · workdesktop-ai | 含 `<link rel="stylesheet" ... workdesktop-ai/style.css">` 恰 1 条 |
| 3 | UMD · vue | 含 `<script src=".../vue/vue.global.prod.js">` 恰 1 条 |
| 4 | UMD · element-plus | 含 `<script src=".../element-plus/index.full.min.js">` 恰 1 条 |
| 5 | UMD · icons | 含 `<script src=".../icons/index.iife.min.js">` 恰 1 条 |
| 6 | UMD · workdesktop-ai | 含 `<script src=".../workdesktop-ai/index.umd.js">` 恰 1 条 |
| 7 | x-template | 含 `<script type="text/x-template" id="...-tpl">` 1 条（模板以字符串模板承载） |
| 8 | createApp 挂模板 | 含 `createApp({ template: '#...-tpl' ... })` 1 处（禁止 `app.mount` 读内联 DOM） |
| 9 | 注册 ElementPlus | 含 `app.use(ElementPlus)` 1 处 |
| 10 | 注册 WorkDesktop | 含 `app.use(WorkDesktop)` 1 处 |
| 11 | 无裸 fetch | 页面内不出现 `fetch(` 调用 |
| 12 | 无裸 axios | 页面内不出现 `axios.` 调用（数据请求一律 `WD.request`：成功判定 `code >= 0`，失败码为负数，解封 `code / message / data`） |

> 本项与「四级对齐」检查（`coding-guide/04_功能包开发指引.md` §7.2 ④：菜单 `route` ↔ `route.php` GET 路由 ↔ 控制器 `pageXxx` 方法 ↔ `src/view` 视图文件，可脚本静态扫描）及 §2.4 菜单结构符合性检查（C9）**并列，同为页面交付前必过项**。

### 2.4 菜单结构符合性检查（人工核对，交付前必过）

- **核对对象**：功能包 `menus.php` 全部声明项；
- **核对基准**：`coding-guide/04_功能包开发指引.md` §6.2「菜单归类指导（强制遵循）」+「宿主渲染规则与 group 聚合分组」（读取方式见 §0 事实源表——宿主 `workspaces.php` + `registry.json` 快照）；
- **判定规则**：下表 4 项逐条比对，任一项不符 → 整体 FAIL，输出菜单项 id + 不符点；全部符合 → 通过。

| # | 核对项 | 规则 |
|---|--------|------|
| 1 | 工作区归属 | `parent` 指向语义匹配的现有分导台（六分导台见 guide04 §6.2 表）；业务操作 → `menu.workspace`，配置类 → `menu.system`，待办聚合 → `menu.approval`，报表 → `menu.report`；**不另起平行的根菜单、不自建"待办/报表根菜单"** |
| 2 | group 值 | 按原生 group 聚合模式：已装包已用过的语义匹配 `group` 值优先复用（多包归入同一分组）；`group` 仅对一级菜单声明，深层菜单不声明（无效）；group 值为语义清晰的中文业务名词 |
| 3 | children 挂载 | 一级菜单挂 `children` 后自身 `route` 被丢弃（留空/不填路由）；叶子菜单 route 指向包路由或宿主页面；**禁止自创折叠/嵌套中间层**（不得为了"整理菜单"发明宿主不存在的结构层级） |
| 4 | perms 并集 | 挂子节点的父级 `perms` = 子项权限码的**并集**（菜单按权限过滤可见性）；叶子菜单 perms 与 `permissions.php` 声明、route 门禁 `checkPerm` 三处一致 |

> 本项与 C8 页面 UMD 范式扫描（§2.3）、「四级对齐」检查（guide04 §7.2 ④）**并列，同为交付前必过项**。

### 2.5 静默修正闭环

发现不合规点 → **直接用编辑工具修改文档正文** → 复审 → 循环至全部通过 → 交付。对话中只汇报"发现 X 处、已修正"级别摘要，**不输出审计日志/审计记录文件**。

## 3. 出处标注格式（强制）

每条审计结论按此格式标注，无出处视为无效：

```
[结论] 通过/不通过 — [对象] — [出处：仓库名/文件名/条目编号 或 SOP §节号]
```

示例：`不通过 — 文件 3 T0 计划自建菜单服务 — 出处：services.php list 键含 menu.service（宿主已提供，SOP A5③ 禁止自建）`
