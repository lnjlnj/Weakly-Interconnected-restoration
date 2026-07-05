# Project_Status.md

## Current Project Position

项目当前处于 **正式仿真可信性加固阶段**，而不是算法性能冲刺阶段。

当前代码基线为：

```text
weaklink_restoration_current_clean_progress
```

该版本基于 v0.8.6 Electrical Audit Hardening，并进一步完成了代码清理与运行进度显示：

* 删除旧版本 run 脚本、旧说明文档、历史 results 目录；
* 保留当前正式流程所需的 `configs/`、`data/`、`src/` 和主运行入口；
* 新增简洁入口：

  * `run_smoke.py`
  * `run_h10.py`
  * `run_h20.py`
  * `run_h50.py`
* 主程序 `run_realistic_power_eval.py` 已加入进度显示，包括完成比例、已耗时、平均耗时和 ETA。

当前阶段目标是：**先确认仿真平台、数据、电气审计、求解器状态和结果输出口径可信，再进入算法增强。**

---

## Confirmed Research Direction

当前研究方向保持为：

```text
弱联交通—配电耦合灾后恢复中，面向资源可达性错配的临时—永久协同恢复调度研究。
```

当前方法雏形为：

```text
弱联感知的临时—永久协同分阶段滚动恢复优化方法
```

核心机制包括：

1. 弱联结构量化；
2. 轻装/重装资源可达性错配建模；
3. temporary / permanent 恢复动作区分；
4. power-aware AUC-oriented rolling score；
5. service restoration / cleanup phase 分阶段；
6. full-repair no-action guard；
7. solver / data / AC audit guardrails。

当前不把方法表述为完整潮流约束 MILP。当前 rolling MILP 本质仍是 **power-aware rolling assignment MILP**，不是完整配电网潮流约束恢复优化模型。

---

## Key Conclusions

1. **v0.8.5 已证明 phase-separated rolling 机制有效。**
   相比 v0.8.4，修复了 high 场景 no-action / full-repair 不完成问题，并使 rolling 在 high/medium 的 total outage AUC 上具备竞争力。

2. **h10 结果显示不同强 baseline 差距不大。**
   rolling、PA-WTLA-C、power critical / capability 等方法在不同指标上各有胜负，不能当前就宣称新算法全面显著优于所有 baseline。

3. **当前更合理的论文路线是“场景机制 + 方法 + 诊断分析”联合型。**
   重点不应只放在算法碾压，而应说明：弱联强度、资源可达性错配、temporary-permanent 协同、critical-load trade-off 如何影响不同策略表现。

4. **仿真可信性仍是当前优先级最高的问题。**
   需要先封住 power-case 数据、pandapower 建模单位、temporary AC 语义、event-level AC audit 和 solver 状态等风险。

5. **v0.8.6 / clean-progress 版已加固电气审计层。**
   已修复 base voltage 字段读取、线路电流单位处理，增加严格 power-case validation、temporary unmodeled flag、event AC mark mode，并加入运行进度 ETA。

---

## Current Risks

1. **power-case 数据存在严格校验 error。**
   当前 validator 会暴露 very low power factor、missing length、extreme x/r 等问题。正式仿真前必须人工确认或修复。

2. **temporary restoration 仍未被真实 AC 建模。**
   当前 `temporary_ac_model = "none"`，temporary 恢复收益仍是 proxy restoration，尚未转换为临时线路、移动电源或局部负荷 pickup 的物理模型。

3. **event AC audit 当前仍是 mark/audit，不是 reject。**
   即使发现中间 AC 不可行，目前只标记，不会回退动作、拒绝动作或修正恢复曲线。

4. **rolling MILP 仍缺少电力约束。**
   当前不是 LinDistFlow / DistFlow / AC-constrained MILP，不能宣称为完整电力约束优化。

5. **算法优势尚未充分确立。**
   h10 样本显示差距较小，必须通过 h20/h50、paired win-rate、high-mismatch subset、temporary/critical 诊断进一步判断方法贡献强度。

---

## Immediate Next Tasks

1. 本地运行 clean-progress 版：

```bash
python run_h10.py
```

2. 检查输出：

```text
raw/power_case_validation_report.csv
raw/ac_event_validation_report.csv
raw/ac_validation_report.csv
raw/rolling_milp_solver_report.csv
summary/realistic_policy_summary.csv
summary/realistic_paired_report.csv
```

3. 优先判断：

* CPLEX 是否全 optimal；
* event AC audit 本地是否 available；
* event AC violation 是否为 0；
* temporary_unmodeled_rate 在 high/medium/low 中多高；
* power-case validation error 是否需要修复数据；
* clean-progress 版性能是否与 v0.8.5 / v0.8.6 保持一致。

4. 若 h10 通过，再运行 h20。
   h20 稳定后再考虑 h50。

5. 在仿真可信性闭环前，暂缓大规模算法调参。
