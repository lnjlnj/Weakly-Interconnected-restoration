P0
本地运行 weaklink_restoration_v051.zip，确认完整流程可运行。
检查 v0.5.1 默认 h50 输出结果：
structural_metrics.csv
hazard_manifestation_metrics.csv
weaklink_response_profile.csv
weaklink_classification.csv
metric_validation_report.csv
weight_sensitivity.csv
active_fault_tasks.csv
power_edge_metrics.csv
分析 random active fault set 下 low / medium / high 是否仍稳定分开。
检查 moderate / severe activation probability 是否符合预期。
检查权重敏感性结果，确认分类不是由单一权重设置偶然产生。
P1
增加不同 active fault count 实验，区分“灾害规模”与“结构弱联”。
完善 spatial_correlated 灾害生成逻辑，使道路损伤与电网故障共享空间灾害场。
优化 coupling metric，使其更明确地结合：
power fault importance；
traffic path scarcity；
bottleneck overlap；
vehicle-type-specific accessibility delay。
整理 v0.5.1 指标体系说明文档，明确每个指标的物理含义和作用边界。
P2
接入第二个 power case，例如 IEEE33 或 case69。
设计多电网拓扑下的泛化验证实验。
在弱联场景分类指标稳定后，再回到恢复调度算法设计。
后续算法重点应放在瓶颈排序、资源匹配、阶段协同、临时/永久恢复选择，而不是单纯路径搜索
