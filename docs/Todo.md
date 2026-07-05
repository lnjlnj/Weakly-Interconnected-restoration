p0
1. 本地运行 clean-progress 版 h10：python run_h10.py
2. 检查 CPLEX 是否全 optimal，确认 fallback=False、invalid_solve=0
3. 检查 power_case_validation_report.csv，重点处理 very low PF、missing length、extreme x/r、max current unit
4. 检查 event AC audit 本地是否 available，确认 event_ac_audit_available_rate=1
5. 检查 temporary_unmodeled_rate，明确 temporary proxy 对结果的影响范围

p1
1. 若 h10 通过，运行 h20，确认趋势稳定
2. 做 rolling vs PA-WTLA-C / power critical 的 paired win-rate 分析
3. 做 high-mismatch subset 分析，而不是只看 high/medium/low 均值
4. 诊断 temporary 使用不足和 critical AUC48 不占优的原因
5. 根据 power-case validation 结果决定是否修数据或调整 validation 阈值

p2
1. 设计 temporary_ac_model 的物理含义：none / line_derating / mobile_generator / load_pickup
2. 设计 AC-corrected restoration metrics
3. 评估 candidate AC filter 的计算成本和必要性
4. 中期准备 LinDistFlow rolling MILP，但暂不进入当前版本
5. 整理方法命名、机制图和实验诊断框架
