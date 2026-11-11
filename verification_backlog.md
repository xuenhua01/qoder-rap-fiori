# 待联网核实清单（verification_backlog）

> 离线开发时，凡是"本应连系统核实、但当时没核实"的假设，逐条登记。连上系统后按"如何核实"批量清，状态改 VERIFIED/FIXED。

| # | 对象 | 存疑假设 | 如何核实 | 状态 |
|---|------|---------|---------|------|
| B-01 | `ZP_EXCEPTION` | 透明表 DDL 语法（`DEFINE TABLE ... CLIENT DEPENDENT`）在本系统 ADT 是否支持直接粘贴 | ADT 建表时试粘 DDL 源；不支持则用 DDIC 表单编辑器手填 | OPEN |
| B-02 | `ZP_EXCEPT_TYPE` / `ZP_EXCEPT_STATUS` | 域固定值 DDL 语法（`VALUE 01 IF '...'`）是否正确 | ADT/SE11 建域时核对固定值录入方式 | OPEN |
| B-03 | 字段类型 | `WERKS_D`/`MATNR`/`SYUNAME`/`TIMESTAMPL` 是否为系统真实数据元素 | MCP 连上后 `ddic_element` / SE11 查证 | OPEN |
| B-04 | 命名冲突 | `ZP_EXCEPTION`/`ZP_EXCEPT_TYPE`/`ZP_EXCEPT_STATUS` 是否已被占用 | MCP `abap_object_info`（Z 对象应"不存在"才可建） | OPEN |
| B-05 | 包落点 | 对象建到哪个包（`ZXUENHUA` 建包失败，暂用 `ZAIXUENHUA`/`ZAITEMP` 待定） | 确认目标包 + 传输请求 | OPEN |
| B-06 | 号码范围 | `EXCEPT_ID` 用号码范围自动生成，需建号段对象 | 联网后 `SNRO`/`SE06` 建号段 | OPEN |
