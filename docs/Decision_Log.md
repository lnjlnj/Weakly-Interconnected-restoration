# Decision_Log.md

Date: 2026-07-05
Decision: 当前阶段优先继续加固正式仿真可信性，而不是立刻全力优化算法性能。
Reason: h10 结果显示 rolling、PA-WTLA-C、power critical / capability 等强 baseline 差距不大；若仿真平台、数据、电气审计和 temporary 语义未完全可信，继续调算法可能得到不可靠的性能提升。

Date: 2026-07-05
Decision: 将当前方法定位为“弱联感知的临时—永久协同分阶段滚动恢复优化方法”，而不是完整电力约束 MILP。
Reason: 当前 CPLEX 模型仍主要是 rolling assignment MILP，缺少显式潮流、电压、线路容量、电源容量等电力约束；必须避免方法表述过度。

Date: 2026-07-05
Decision: 推进 v0.8.6 Electrical Audit Hardening。
Reason: 需要修复 pandapower base voltage 字段读取、线路电流单位处理，升级 power-case validation 严重等级，并显式记录 temporary restoration 尚未 AC 建模的事实。

Date: 2026-07-05
Decision: 在 event-level AC audit 中引入 mark 语义，但暂不引入 reject / candidate AC filter。
Reason: 当前 temporary AC 物理语义尚未定义清楚，直接 reject 可能把不成熟的 AC audit 变成强约束，风险较高。

Date: 2026-07-05
Decision: 整理当前代码为 clean-progress 版，删除旧版本脚本、旧说明和历史结果目录。
Reason: 历史版本文件过多，容易混淆当前正式仿真入口和结果目录；清理后便于本地运行、结果复现和后续维护。

Date: 2026-07-05
Decision: 在主仿真循环中加入进度显示和 ETA。
Reason: h10/h20/h50 本地运行时间较长，需要在控制台显示完成比例、已耗时、平均耗时和预计剩余时间，方便用户判断运行进度。
