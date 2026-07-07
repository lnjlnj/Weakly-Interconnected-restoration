## Current Project Position

项目当前处于 **正式仿真可信性已基本通过、方法机制继续筛选阶段**。

当前已完成并验证过的主要版本包括：

- v0.8.8 Data & Reproducibility Patch
- v0.8.9 Scenario Diagnosis Patch
- v0.9 Weaklink-Adaptive Rolling Prototype
- v0.9.1 Pressure Diagnosis Patch
- v0.9.2 Structural Adaptive Patch

当前结论是：**仿真平台、数据校验、求解器状态、基础 AC audit 和诊断输出已经基本可信；但 adaptive 方法路线尚未形成足够强、足够优雅的最终主方法。**

当前不应继续简单堆叠 adaptive 规则，也不应把 v0.9.2 structural adaptive 直接作为最终论文方法。更合理的下一步是将其中有效思想重新整理为：

**Unified Weaklink-Parameterized Rolling Objective**  
**弱联参数化的统一滚动优化目标 / 软约束模型**

---

## Confirmed Research Direction

当前研究方向保持为：

**弱联交通—配电耦合灾后恢复中，面向资源可达性错配的临时—永久协同恢复调度研究。**

当前论文主线仍应围绕：

1. 弱联结构量化；
2. 资源可达性错配；
3. 轻型/重型资源能力与通达性不匹配；
4. temporary / permanent 恢复协同；
5. 高弱联、高错配场景下的整体恢复效率；
6. 弱联指标如何参与恢复决策，而不是只作为场景标签。

当前不把方法表述为完整电力约束 MILP。当前 rolling MILP 仍应谨慎表述为：

**power-aware rolling assignment / restoration scheduling model**

而不是完整 AC / DistFlow 约束恢复优化模型。

---

## Key Conclusions

1. **v0.8.8 已通过正式仿真可信性基础审计。**  
   power-case validation error 已清零；CPLEX 在 h10/h20 中全 optimal；fallback、invalid solve、no-action violation、resource overlap violation 均为 0；event-level AC audit 无 violation。

2. **v0.8.9 诊断补丁有效。**  
   已新增 scenario-feature performance diagnosis、weaklink-feature correlation、oracle policy portfolio 等输出，支持分析“什么样的弱联场景中哪类策略更强”。

3. **fixed rolling 主体方法是当前最稳定的核心方法。**  
   h20 显示 rolling 在 AUC168 / 整体恢复进程上稳定较强，尤其在 high weaklink 和 high access mismatch 场景中优势更明显。

4. **power_wtla_conservative_temp 是当前最强竞争 baseline。**  
   它在 early AUC、critical AUC48、critical T80 上经常优于 rolling，不能被当作普通 baseline 轻描淡写处理。

5. **浅层权重 adaptive 收益过小。**  
   v0.9 的 adaptive_critical / adaptive_mismatch / adaptive_full 只带来极小平均提升，说明单纯通过弱联指标调整 score 权重不足以形成显著方法优势。

6. **pressure 场景验证了 rolling 框架价值，但没有显著放大 adaptive 权重价值。**  
   v0.9.1 pressure 场景明显提高了任务数量、outage load、access mismatch、heavy-light gap 和 temporary-only 任务比例；rolling 相比弱 baseline 优势扩大，但 adaptive 相比 fixed rolling 的边际收益仍然很小。

7. **v0.9.2 structural adaptive 有工程效果，但不适合作为最终主方法。**  
   structural_full 在 pressure h10 中 AUC168 第一，并使 T80 相比 fixed rolling 快约 6 小时；但 AUC168 只小幅提升，critical 仍不占优，temporary unmodeled rate 更高，且 structural rules 存在规则堆叠、不够数学统一的问题。

8. **下一步应从“规则 adaptive”转向“统一目标函数 / 软约束”建模。**  
   有价值的思想包括 early critical loss、temporary-permanent coupling penalty、heavy waiting opportunity cost、bottleneck unlock value；应将它们统一进入 rolling objective，而不是写成多个 if-else 触发规则。

---

## Current Risks

1. **temporary restoration 仍是最大概念风险。**  
   high 和 pressure 场景下 temporary_unmodeled_rate 很高，pressure 中甚至超过 70%。temporary 当前仍主要是 proxy restoration，尚未形成可审计的物理 AC 模型。

2. **adaptive 方法收益不足。**  
   v0.9 浅层 adaptive 和 v0.9.2 structural adaptive 对 AUC168 的提升都很小，不能作为强算法贡献直接写入论文。

3. **structural adaptive 缺少数学统一性。**  
   critical window、temporary gate、anti-wait guardrail 等规则虽然直觉合理，但像工程规则堆叠，容易被质疑阈值来源、后验调参和模型不优雅。

4. **critical load early restoration 仍是 rolling 短板。**  
   rolling / structural_full 在整体 AUC168 和 T80 上较强，但 critical AUC48 / critical T80 经常不如 power_wtla_conservative_temp 或 power-critical 类策略。

5. **AUC168 可能接近策略组合上限。**  
   pressure oracle portfolio 显示 AUC168 的 oracle 提升空间很小，继续围绕 AUC168 做策略微调可能边际收益有限。

6. **若继续使用 pressure 场景，temporary 物理语义风险会被放大。**  
   pressure 场景能放大弱联压力，但也会显著增加 temporary proxy 依赖，不能直接作为最终论文核心结果而不补充解释。

---

## Immediate Next Tasks

1. 暂停继续堆叠 v0.9.2 structural adaptive 规则。

2. 设计 v0.9.3：**Unified Weaklink-Parameterized Rolling Objective**。

3. v0.9.3 的核心统一项包括：

   - early critical loss
   - temporary-permanent coupling penalty
   - heavy waiting opportunity cost
   - bottleneck unlock value

4. 将 v0.9.2 中有效但规则化的思想转化为目标函数项或软约束，而不是继续使用 critical gate / temporary gate / anti-wait if-else 作为主方法表述。

5. 实现 v0.9.3 后优先在 h20 和 pressure h10 上比较：

   - fixed rolling
   - score-level adaptive
   - structural adaptive
   - unified weaklink-parameterized rolling
   - power_wtla_conservative_temp
   - power_critical_marginal_greedy

6. 同步设计 temporary_ac_model 的最低可解释版本，优先考虑：

   **temporary_ac_model = load_pickup**

7. 若 v0.9.3 仍无法形成明显收益，应考虑将论文主方法回退为：

   **fixed rolling + 弱联机制诊断 + temporary/permanent 协同分析**

   而不是继续追求 adaptive 算法增强。
