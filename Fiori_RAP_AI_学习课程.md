# Fiori RAP 开发学习课程（个人版 · AI 提效）

> 技术栈：**SAP RAP + Fiori Elements**（S/4HANA 或 BTP 练习系统皆可）
> 定位：一份**完全属于个人**的学习计划，用于自己练手、上传个人 GitHub。**与公司 ABAP-AI-WORKBENCH 及其 agent / 规则 / 技能无任何关联。**
> 环境（自填）：练习系统 = ____________（本地 S/4 sandbox / BTP Trial / 学习机）；开发包 = 私有包或 `$TMP`；前缀 = `ZP_`；传输请求 = 个人 dev 配置。
> 工具：任意支持本地开发的 IDE + 你自己的 SAP 连接（ADT / BTP）。对象是否存在，连你自己的系统核实，不臆造。

---

## 0. 成果物总览

| # | 成果物 | 形态 | 完成标志 |
|---|--------|------|---------|
| 1 | **可运行的 Fiori Elements 应用** | List Report + Object Page，查询/新建/修改/删除 + 草稿 + 自定义动作 | Preview 里能建一条异常单、改状态、点"关闭"生效 |
| 2 | **RAP 对象源码（序列化）** | `.asddls` / `.asbdef` / `.asrvd` / `.asrvb` / behavior 类 `.abap`，落到本仓库 `src/` | 每个对象本地有备份，可离线复现 |
| 3 | **AI 提效台账** | §5 的表，每课结束亲手填真实工时 | 7 行都有"手工基线 / AI 辅助实际 / 节省" |
| 4 | **每课 AI vs 人工分工卡** | §4 每课末尾固定四段 | 每课都能回答"AI 干了啥、人验了啥、坑在哪" |

---

## 1. Demo 场景：生产异常记录维护（ZP_EXCEPTION）

一个实体覆盖 RAP 全生命周期，贴近生产/MES，业务好讲。

**业务一句话**：产线发生异常时建一张"异常单"，记录物料/工厂/类型/严重度/描述，指派责任人，随状态流转（OPEN → IN_PROGRESS → CLOSED），可关闭。

**数据模型（透明表 ZP_EXCEPTION，12 字符 ✓，下划线在第 3 位之后 ✓）**

| 字段 | 类型 | 说明 | RAP 角色 |
|------|------|------|---------|
| `MANDT` | CLNT | 集团 | 键 |
| `EXCEPT_ID` | CHAR10 | 异常编号 | 键 · 号码范围自动派生 |
| `PLANT` | WERKS_D | 工厂 | 值帮助 |
| `MATERIAL` | MATNR | 物料 | 值帮助 |
| `EXCEPT_TYPE` | CHAR4 | 异常类型（设备/质量/物料/人员） | 固定值下拉 |
| `SEVERITY` | CHAR1 | 严重度 H/M/L | 枚举 |
| `STATUS` | CHAR1 | 状态 1=OPEN 2=IN_PROGRESS 3=CLOSED | 副作用刷新 |
| `DESCRIPTION` | CHAR200 | 异常描述 | 必填校验 |
| `RESPONSIBLE` | CHAR12 | 责任人 | 自定义动作赋值 |
| `CREATED_BY` / `CREATED_AT` | SYUNAME / TIMESTAMPL | 审计 | 只读 |

**要覆盖的 RAP 能力**：草稿驱动的 CRUD · 号码范围 · 字段校验（DESCRIPTION 必填、CLOSED 不可再改）· 自定义动作 `closeException`/`assignOwner` · 副作用刷新 · CDS `@UI` + 值帮助 + 筛选 + 中文标题（i18n）。

---

## 2. 课程地图（7 课）

> 节奏：每课三段 —— 讲概念 → AI 落代码到 `src/` → 你连系统激活 / Preview 验收，回报结果才进下一课。

| 课 | 主题 | 本课产出对象 | 验收 checkpoint |
|----|------|-------------|----------------|
| L0 | RAP 分层 + AI 分工约定 | 概念对齐，无代码 | 能说出 I/C/BDEF/SRVD/SRVB 各层职责 |
| L1 | 数据模型：透明表 + 域/数据元素 | `ZP_EXCEPTION` + 域 `ZP_EXCEPT_TYPE`/`ZP_EXCEPT_STATUS` | SE11 激活、能维护样例数据 |
| L2 | 接口 CDS（取数层） | `ZP_I_EXCEPTN`（`.asddls`） | 能 SELECT 出数据，无 `@UI` |
| L3 | 消费 CDS（呈现层 @UI） | `ZP_C_EXCEPTN` + `ZP_I_EXCEPTN_VH`（值帮助） | 注解齐全，字段中文标题 |
| L4 | 行为定义 BDEF + behavior 类 | `ZP_EXCEPTN`（`.asbdef`）+ behavior pool | 草稿/动作/校验/号码范围都定义 |
| L5 | 服务定义 + 绑定 + Publish | `ZP_UI_EXCEPTN`（`.asrvd`）+ `ZP_UI_EXCEPTN_O4`（`.asrvb`） | Publish → Preview 出 List Report |
| L6 | Fiori Elements 增强 | Object Page、筛选、动作按钮、i18n 调优 | 能建单/改状态/点"关闭"生效 |
| L7 | 端到端验收 + 提效复盘 | 台账 §5 填实 + 序列化归档 `src/` | 4 项成果物齐 + 每课分工卡填完 |

---

## 3. 开发约定（个人版）

1. **零臆造**：命名冲突、字段/数据元素是否存在，连你自己的系统核实；连不上就转人工核对清单。
2. **命名合规**：DDIC 表/结构 ≤16 字符、下划线不在第 2/3 位；CDS 走 `ZP_I_`/`ZP_C_`。
3. **不 Core Modification**：只用标准对象 + RAP 自身扩展机制。
4. **本地优先**：断网时先在 `src/` 写全，联网后统一 Activate/Publish。
5. **不整包一次生成**：每课只生成本课文件，其余留空，看见"生长过程"。

---

## 4. 每课详解（AI vs 人工分工卡）

> 每张分工卡固定四段：**AI 产出 / 人工把关 / 提效点 / 提效数据**。"手工基线"是估算，"AI 辅助实际"必须你亲手填真实值。

### L0 · RAP 分层 + AI 分工约定
- 讲清 5 层：透明表 → Interface View → Consumption View（`@UI`）→ Behavior Definition → Service Definition/Binding。
- **分工卡**：AI=分层图与对照表；人=自己复述边界；提效=省翻文档。基线 1.5h / 实际 ___h。

### L1 · 数据模型（透明表 + 域）
- 建域 `ZP_EXCEPT_TYPE`/`ZP_EXCEPT_STATUS`（固定值）、透明表 `ZP_EXCEPTION`。
- **分工卡**
  - AI：表结构、字段类型选型（对照 WERKS_D/MATNR 等真实数据元素）、域固定值清单。
  - 人：① 核实标准数据元素真实存在；② 表名长度/下划线合规；③ 命名冲突查询。
  - 提效：AI 给可粘贴字段表，省逐个查 DDIC。数据：基线 2.0h / 实际 ___h / 节省 ___h。

### L2 · 接口 CDS `ZP_I_EXCEPTN`
- 只取数 + 语义化别名 + 关联，**不加 `@UI`**。
- **分工卡**：AI=DDL 骨架/别名/association 雏形；人=字段与表列对齐、键正确、连系统 Activate。数据：基线 2.0h / 实际 ___h / 节省 ___h。

### L3 · 消费 CDS `ZP_C_EXCEPTN` + 值帮助视图
- 加 `@UI`：`lineItem`/`fieldGroup`/`selectionField`、中文标题、`valueList`/`valueHelp`。
- **分工卡**：AI=全套 `@UI` 注解 + VH 视图；人=注解与呈现预期一致、i18n 正确。数据：基线 3.0h / 实际 ___h / 节省 ___h。

### L4 · 行为定义 BDEF + behavior 类（最重）
- `.asbdef`：`draft`、`create/update/delete`、`number range`、`validation`、`action closeException/assignOwner`、`side effects`、`determination`。
- behavior pool 局部类实现 action/validation。
- **分工卡**：AI=BDEF 语法 + 方法骨架 + 号码范围步骤；人=业务规则确认、action 返回与权限、过激活。数据：基线 6.0h / 实际 ___h / 节省 ___h。

### L5 · 服务定义 + 绑定 + Publish
- SRVD → SRVB（OData V4 UI）→ Publish → Preview。
- **分工卡**：AI=SRVD/SRVB 内容 + Publish/Preview 步骤；人=服务确实 Publish、代理 URL/Client 对。数据：基线 2.0h / 实际 ___h / 节省 ___h。

### L6 · Fiori Elements 增强
- Object Page、筛选栏、动作按钮（"关闭异常"）、草稿体验、值帮助联调。
- **分工卡**：AI=注解级 UI 调整 + manifest 片段；人=交互符合业务直觉、动作反馈。数据：基线 5.0h / 实际 ___h / 节省 ___h。

### L7 · 端到端验收 + 提效复盘
- 走完整用户故事：建单→存草稿→激活→改状态→点关闭→列表筛选；序列化全部对象到 `src/`；补齐 §5 真实工时。
- **分工卡**：AI=验收 checklist + 序列化清单；人=真机跑通并填真实数字。数据：基线 2.0h / 实际 ___h / 节省 ___h。

---

## 5. AI 提效台账（亲手填真实数字）

| 课 | 主题 | 手工基线(h) | AI 辅助实际(h) | 节省(h) | 节省% | AI 主要帮到的点 |
|----|------|-----------|--------------|--------|------|----------------|
| L0 | 概念对齐 | 1.5 | ___ | ___ | ___% | 分层对照表 |
| L1 | 透明表+域 | 2.0 | ___ | ___ | ___% | 字段选型/固定值 |
| L2 | 接口 CDS | 2.0 | ___ | ___ | ___% | DDL 骨架 |
| L3 | 消费 CDS @UI | 3.0 | ___ | ___ | ___% | 注解补齐 |
| L4 | BDEF+behavior | 6.0 | ___ | ___ | ___% | 语法兜底 |
| L5 | SRVD/SRVB/Publish | 2.0 | ___ | ___ | ___% | 排错清单 |
| L6 | Fiori 增强 | 5.0 | ___ | ___ | ___% | 注解级 UI 试错 |
| L7 | 验收+复盘 | 2.0 | ___ | ___ | ___% | checklist/序列化 |
| **合计** | | **23.5** | ___ | ___ | ___% | |

**填法**：只计动手产出时间，不含等网络/激活空档；"帮到的点"要具体到省了哪一步；AI 帮倒忙（错字段/幻觉对象）也如实记，反证"人必须把关"。

---

## 6. 目录结构（个人仓库内）

```
PersonalCode/
├── README.md
├── .gitignore
├── Fiori_RAP_AI_学习课程.md      # 本文件
└── src/                          # RAP 对象序列化，逐课填充
    ├── zp_exception.tabl.xml
    ├── zp_i_exceptn.ddls.asddls
    ├── zp_c_exceptn.ddls.asddls
    ├── zp_exceptn.tabl.asbdef
    ├── zp_ui_exceptn.srvd.asrvd
    └── zp_ui_exceptn_o4.srvb.asrvb
```

> 本文件为个人学习用途，不含任何公司包名/请求号/内部系统信息，可安全放入个人 GitHub。
