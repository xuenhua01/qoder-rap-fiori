# 待联网核实清单（verification_backlog）

> 离线开发时，凡是"本应连系统核实、但当时没核实"的假设，逐条登记。连上系统后按"如何核实"批量清，状态改 VERIFIED/FIXED。
> 当前 demo：物料主数据报表（单表 `ZP_MATERIAL`，全标准字段，无新建域/DTEL）。

| # | 对象 | 存疑假设 | 如何核实 | 状态 |
|---|------|---------|---------|------|
| B-01 | `ZP_MATERIAL` | 透明表 DDL 语法（`DEFINE TABLE ... CLIENT DEPENDENT`）在本系统 ADT 是否支持直接粘贴 | ADT 建表试粘 DDL 源；不支持则 DDIC 表单编辑器手填 | OPEN |
| B-02 | 标准数据元素 | `MATNR`/`MAKTX`/`MTART`/`MATKL`/`MEINS`/`LVORM` 存在 | `abap_object_info`(DTEL) 逐个核实：全部 responsible=SAP、active/partlyActive | VERIFIED (2026-11-11) |
| B-03 | 命名冲突 | `ZP_MATERIAL` 是否已被占用 | `abap_object_info`(TABL) 返回 object not found → 无冲突可新建 | VERIFIED (2026-11-11) |
| B-04 | 包落点 | 对象建到哪个包（`ZXUENHUA` 建包失败，暂用 `ZAIXUENHUA`/`ZAITEMP` 待定） | 确认目标包 + 传输请求 | OPEN |
