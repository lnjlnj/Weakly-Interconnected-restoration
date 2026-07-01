# Project_Status.md

## Current Project Position

当前项目处于 **弱联场景分类指标验证阶段**，暂不进入恢复调度算法设计阶段。

当前核心任务不是证明某个调度算法更优，而是验证：

> 我们提出的弱联场景分类指标，是否能够科学、稳定、可解释地区分 low / medium / high 弱联场景。

最新代码版本为：

```text
v0.5.1: Power-topology-aware weak-link scenario validation
```

该版本相较 v0.5 的核心变化是：

* 引入可插拔电网拓扑接口 `PowerCase`；
* 将电网拓扑信息正式接入弱联指标体系；
* 区分候选故障池与实际 active fault set；
* 支持随机 active fault set 生成；
* 引入 moderate / severe 两级弱联激活概率；
* 新增场景指标验证报告；
* 新增权重敏感性分析脚本。

当前默认实验仍使用 `feeder_51` 作为基础电网，但代码结构已支持后续扩展 IEEE33、case69 或真实馈线。

---

## Confirmed Research Direction

本项目不是对已有 EPSR 论文进行简单约束叠加、车辆类型扩展或算例规模放大。

当前确认的研究方向是：

> 将“弱联”从农村/山区等背景性描述，提升为交通–配电–资源耦合系统中的可计算、可比较、可生成、可验证的结构性与灾害响应性属性。

弱联应理解为以下系统特征的组合：

* 低冗余；
* 低可替代性；
* 高瓶颈依赖；
* 关键资源可达延迟；
* 资源机动性与维修能力错配；
* 临时恢复与永久修复之间存在阶段性断裂。

后续算法阶段不应被表述为“低自由度路径优化”，而应聚焦于低冗余系统中的高代价恢复决策，包括：

* 瓶颈排序；
* 资源匹配；
* 阶段协同；
* 等待与转移决策；
* 临时恢复与永久修复方式选择。

当前阶段的研究重点是：**验证弱联场景分类指标是否合理，而不是优化恢复调度算法。**

---

## Key Conclusions

1. v0.5 在 hazard seeds 增加到 50 后，low / medium / high 三类弱联场景能够明显分开，说明交通弱联、资源可达延迟和受限耦合显化指标具有初步有效性。

2. 但 v0.5 的随机性主要集中在交通层；电网拓扑、故障点集合、电网–交通基础映射均固定。因此，v0.5 只能部分验证弱联场景分类指标，不能完整证明整个弱联场景体系合理。

3. 当前已确认：若研究目标是验证场景分类指标合理性，则代码必须至少覆盖：

   * 交通结构变化；
   * 交通灾害随机性；
   * 随机 active fault set；
   * 电网拓扑感知的故障重要性；
   * 电网侧故障重要性与交通侧可达瓶颈共同构成的耦合指标。

4. v0.5.1 已引入 `PowerCase` 接口，将电网边、电网节点、候选故障池和故障–交通任务映射统一组织到 power case 目录中。

5. v0.5.1 已加入拓扑感知电网指标，包括：

   * 故障边深度；
   * 下游负荷占比；
   * 下游关键负荷占比；
   * 电网瓶颈依赖度；
   * power fault importance；
   * topology-based restoration value。

6. v0.5.1 已支持 `random_subset` active fault generation，使每个 hazard seed 不再必须使用同一组固定故障点。

7. 两级激活概率比原先单一 0.30 阈值更合理：

   * low 场景基本不激活；
   * medium 场景出现 moderate activation；
   * high 场景同时出现 moderate 和 severe activation。

8. 当前不急于随机生成大量电网拓扑。更稳妥的路线是：先完成可插拔电网拓扑接口与拓扑感知指标，再逐步扩展 IEEE33、case69 或真实馈线作为泛化验证。

---

## Current Risks

1. **单一电网拓扑风险**
   当前默认实验仍只使用 `feeder_51`。虽然电网拓扑已进入指标计算，但跨电网拓扑泛化能力尚未验证。

2. **场景分类被生成器驱动的风险**
   low / medium / high 的区分可能仍部分来自场景生成器设定。需要通过随机故障集合、不同故障数量、权重敏感性和后续多电网拓扑实验进一步验证。

3. **空间相关灾害不足风险**
   当前 `spatial_correlated` 仍处于预留或简化状态，尚未形成严格的道路损伤与电网故障共享空间灾害场。

4. **耦合映射固定风险**
   默认模式下，电网故障边到交通任务点的映射仍是固定的。后续需要支持空间相关映射或瓶颈对齐映射，但不宜立即使用完全随机映射作为主实验。

5. **电网结构弱联仍可能不区分**
   在单一 `feeder_51` 下，结构级 `power_structural_weakness` 可能仍然相同。这不是当前阶段的致命问题，但后续若要声称电网弱联也参与 low / medium / high 主分类，需要接入多个 power cases 或生成不同电网弱联变体。

---

## Immediate Next Tasks

1. 本地运行 v0.5.1，确认 `run_quick_check.py`、`run_v051.py`、`run_validate_scenario_metrics.py`、`run_weight_sensitivity.py` 均能正常运行。

2. 分析 v0.5.1 默认 h50 结果，重点检查：

   * low / medium / high 是否仍保持有序；
   * random active fault set 是否破坏分类稳定性；
   * moderate / severe activation 是否符合预期；
   * active fault count 是否合理变化；
   * power topology metrics 是否真正进入 active fault 与 manifestation 指标；
   * 权重敏感性是否稳定。

3. 下一步优先补充不同故障数量实验，检验弱联指标是否被灾害规模主导。

4. 后续再推进更真实的 spatially correlated disaster generation。

5. 在 v0.5.1 稳定后，再考虑接入第二个 power case，例如 IEEE33 或 case69，用于检验指标体系的跨电网泛化能力。
