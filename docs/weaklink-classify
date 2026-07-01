# 当前弱联指标体系说明：结构基础、灾害画像与统计弱联分类

## 1. 指标体系设计目标

本项目中的“弱联”不是山区、农村、偏远地区等地理标签，也不是某一次灾害后恢复变慢的结果，而是交通–配电–资源耦合系统在结构上表现出的低冗余、低替代性、高瓶颈依赖和资源可达性错配特征。项目 overview 已经明确指出，弱联的本质是系统关键恢复功能对少数供电路径、少数通达道路、少数抢修资源或少数交通–电网–资源耦合瓶颈高度依赖；由于替代路径和替代资源不足，局部故障可能显著放大全局恢复延迟。

因此，当前 v0.5 指标体系的目标不是简单判断“道路坏了多少”或“某次灾害恢复慢不慢”，而是回答三个问题：

1. 这个交通–配电–资源结构本身是否低冗余、低替代、高瓶颈依赖；
2. 在同一标准随机灾害分布作用下，该结构是否更容易显化出可达延迟、资源能力释放延迟和临时/永久恢复窗口；
3. 如何基于结构基础和多灾害响应画像，对弱联程度进行可计算、可比较、可分类的度量。

因此，当前弱联指标体系分为三层：

```text
结构弱联基础 Structural Weak-Link Basis
+
单次灾害显化 Single-Hazard Manifestation
+
多灾害统计弱联程度 Statistical Weak-Link Degree
```

其中，结构弱联基础描述系统固有属性；单次灾害显化描述某个灾害场景下弱联瓶颈是否被激活；统计弱联程度则通过多个随机灾害样本的响应分布，综合刻画某一结构的弱联类型。

---

## 2. 第一层：结构弱联基础指标

结构弱联基础指标只依赖灾前结构和资源配置，不使用道路灾后开放时间、不使用调度算法结果，也不使用恢复曲线指标。

它对应当前代码中的：

```text
weaklink_structural_index
```

也可理解为：

```text
structural_weaklink_basis
```

它回答的问题是：

> 在不考虑某一次具体灾害的前提下，这个交通–配电–资源耦合结构本身是不是低冗余、低替代、高瓶颈依赖？

当前结构弱联基础由四个子指标组成：

```text
weaklink_structural_index
=
0.22 × power_structural_weakness
+ 0.25 × traffic_structural_weakness
+ 0.28 × coupling_structural_weakness
+ 0.25 × resource_structural_weakness
```

其中，交通–电网耦合结构弱联的权重最高，因为本项目关注的不是单独的电网弱联或单独的交通弱联，而是交通–配电–资源耦合系统中的高代价恢复决策问题。overview 中也明确将研究重点放在瓶颈排序、资源匹配、阶段协同、等待/转移和恢复方式选择上，而不是主打低自由度路径优化。

---

## 2.1 电网结构弱联 `power_structural_weakness`

电网结构弱联描述电气恢复价值是否集中在少数任务、关键负荷或严重故障上。

当前计算逻辑为：

```text
power_structural_weakness
=
0.55 × value_concentration_norm
+ 0.30 × critical_value_concentration
+ 0.15 × severe_value_share
```

各组成部分含义如下：

| 指标                             | 含义                                     |
| ------------------------------ | -------------------------------------- |
| `value_concentration_norm`     | 恢复价值是否集中在少数任务上。若少数故障任务承担大部分恢复价值，则该值较高。 |
| `critical_value_concentration` | 一级/关键任务的恢复价值占总恢复价值的比例。                 |
| `severe_value_share`           | 严重故障任务的恢复价值占总恢复价值的比例。                  |

该指标的基本含义是：

> 如果少数关键故障任务承载了大量恢复价值，那么这些任务一旦受阻，整体恢复过程就更容易被放大拖延。

当前版本中，电网结构弱联仍然是基于任务恢复价值、优先级和严重度的 proxy 指标，还不是完整的配电网拓扑指标。后续可以进一步加入配电网割边、关键负荷到电源的拓扑距离、备用供电路径、孤岛化风险和网络重构可用性等更电力系统化的指标。

---

## 2.2 交通结构弱联 `traffic_structural_weakness`

交通结构弱联描述道路网络的替代路径是否不足，特别是仓库到高价值抢修任务点是否依赖少数道路。

当前计算逻辑为：

```text
traffic_structural_weakness
=
0.35 × structural_bridge_ratio
+ 0.35 × value_weighted_path_scarcity
+ 0.20 × structural_bottleneck_road_ratio
+ 0.10 × weighted_path_length_score
```

各组成部分含义如下：

| 指标                                 | 含义                                  |
| ---------------------------------- | ----------------------------------- |
| `structural_bridge_ratio`          | 交通网络中割边比例。割边越多，说明道路网络越树状、绕行能力越弱。    |
| `value_weighted_path_scarcity`     | 按任务恢复价值加权的路径稀缺性。高价值任务如果可选路径少，则该值较高。 |
| `structural_bottleneck_road_ratio` | 被识别为结构瓶颈道路的比例。                      |
| `weighted_path_length_score`       | 按恢复价值加权的路径长度得分。高价值任务越远，交通恢复代价越高。    |

其中，路径稀缺性主要基于仓库到任务点之间的边连通度计算。若仓库到某任务点只有一条边不相交路径，则路径稀缺性高；若存在多条替代路径，则路径稀缺性低。

该指标的基本含义是：

> 如果高价值任务点交通上缺少替代路径，或者必须经过少数结构瓶颈道路，则交通结构弱联程度更高。

---

## 2.3 交通–电网耦合结构弱联 `coupling_structural_weakness`

交通–电网耦合结构弱联是当前指标体系中最核心的一项。它描述电气上重要的任务是否恰好交通上也难以到达。

当前计算逻辑为：

```text
coupling_structural_weakness
=
0.55 × value_weighted_bottleneck_overlap
+ 0.45 × value_weighted_path_scarcity
```

各组成部分含义如下：

| 指标                                  | 含义                      |
| ----------------------------------- | ----------------------- |
| `value_weighted_bottleneck_overlap` | 高价值任务的最短交通路径是否经过结构瓶颈道路。 |
| `value_weighted_path_scarcity`      | 高价值任务的交通路径是否稀缺。         |

该指标表达的是：

> 不是道路本身少就一定构成强弱联，而是越重要的电网恢复任务越交通受限，才真正构成交通–配电耦合弱联。

换句话说，高弱联场景的典型特征不是简单“路少”，而是：

```text
电气上越关键的故障任务
越依赖少数交通通道
越容易形成交通–电网耦合瓶颈
```

这一点直接对应项目 overview 中对交通–电网耦合弱联的设想，即弱联不只是电网弱或交通弱，而是电网关键元件、交通瓶颈和资源能力限制在空间和功能上显著耦合。

---

## 2.4 资源结构弱联 `resource_structural_weakness`

资源结构弱联描述任务需求和资源能力之间的替代性是否不足。

当前计算逻辑为：

```text
resource_structural_weakness
=
0.55 × value_weighted_resource_scarcity
+ 0.25 × severe_resource_scarcity
+ 0.20 × value_weighted_path_scarcity
```

各组成部分含义如下：

| 指标                                 | 含义                                       |
| ---------------------------------- | ---------------------------------------- |
| `value_weighted_resource_scarcity` | 按恢复价值加权的资源能力稀缺性。高价值任务若只能由少数资源类型完成，则该值较高。 |
| `severe_resource_scarcity`         | 严重故障任务对少数高能力资源的依赖程度。                     |
| `value_weighted_path_scarcity`     | 高价值任务的交通路径稀缺性，用于反映资源到达与任务价值之间的耦合。        |

对每个任务，当前代码会根据任务所需维修能力等级和车辆/资源类型的维修能力等级，计算有多少类资源能够完成该任务。能力稀缺性可以简化理解为：

```text
capability_scarcity = (4 - capable_resource_count) / 3
```

如果一个任务只有重型专项资源能够完成，则资源稀缺性高；如果轻型、小型、常规、重型资源都能处理，则资源稀缺性低。

该指标对应 overview 中提出的资源可达性弱联和交通–资源耦合弱联，即灾后恢复中存在“能到的不一定能修，能修的不一定能到”的错配问题。

---

## 3. 第二层：单次灾害显化指标

结构弱联基础描述的是“系统本身是否容易弱联”，但灾害是否真正击中瓶颈、是否造成车辆类型相关可达延迟，还需要通过灾害场景来体现。

因此，v0.5 中在每个固定结构上生成多个标准随机灾害。每个灾害会产生道路开放时间、不同车辆类型的最早到达时间、临时/永久恢复窗口等信息。

单次灾害显化指标对应当前代码中的：

```text
weaklink_manifestation_index
```

它回答的问题是：

> 某个固定结构在这一次随机灾害下，弱联瓶颈是否被激活？如果被激活，显化强度有多大？

当前计算逻辑为：

```text
weaklink_manifestation_index
=
0.20 × traffic_recovery_weakness_score
+ 0.25 × resource_accessibility_delay_score
+ 0.30 × coupled_bottleneck_delay_score
+ 0.25 × temporary_permanent_mismatch_score
```

注意：该指标仍然是算法无关的。它不使用任何调度策略的 AUC、恢复曲线或完成时间，而是只使用灾害后道路开放时间、车辆最早到达时间、任务价值、任务严重度和临时/永久恢复能力窗口。

---

## 3.1 交通恢复弱联显化 `traffic_recovery_weakness_score`

该指标描述灾害后道路恢复过程是否明显拖延，特别是重型车辆通行能力是否释放较晚。

当前计算逻辑为：

```text
traffic_recovery_weakness_score
=
0.35 × norm(avg_open_time_heavy, 48h)
+ 0.30 × norm(p90_open_time_heavy, 96h)
+ 0.20 × norm(max_open_time_heavy, 168h)
+ 0.15 × bottleneck_road_delay_score
```

其中：

| 指标                            | 含义                  |
| ----------------------------- | ------------------- |
| `avg_open_time_heavy_h`       | 所有道路对重型资源的平均开放时间。   |
| `p90_open_time_heavy_h`       | 重型资源道路开放时间的 90 分位值。 |
| `max_open_time_heavy_h`       | 重型资源最晚道路开放时间。       |
| `bottleneck_road_delay_score` | 结构瓶颈道路对重型资源的开放延迟得分。 |

该指标体现的是：

> 道路最终都会恢复，但如果重型资源通行能力释放很晚，恢复过程仍然会出现明显延迟。

---

## 3.2 资源可达性延迟 `resource_accessibility_delay_score`

该指标描述不同资源类型到达故障任务点的时间差，尤其关注重型资源到严重故障和高价值任务的延迟。

当前计算逻辑为：

```text
resource_accessibility_delay_score
=
0.30 × norm(avg_heavy_ea_severe, 96h)
+ 0.22 × norm(p90_heavy_ea_severe, 144h)
+ 0.25 × value_weighted_heavy_access_delay
+ 0.23 × value_weighted_light_heavy_access_gap
```

其中：

| 指标                                             | 含义                         |
| ---------------------------------------------- | -------------------------- |
| `avg_heavy_earliest_arrival_to_severe_tasks_h` | 重型资源到严重故障任务的平均最早到达时间。      |
| `p90_heavy_earliest_arrival_to_severe_tasks_h` | 重型资源到严重故障任务最早到达时间的 90 分位值。 |
| `value_weighted_heavy_access_delay`            | 按任务恢复价值加权的重型资源到达延迟。        |
| `value_weighted_light_heavy_access_gap`        | 按任务恢复价值加权的轻型与重型资源到达时间差。    |

该指标体现的是：

> 弱联不是重型资源永远到不了，而是高能力资源释放得晚；轻型资源可以先到，但真正完成永久修复的资源往往滞后。

---

## 3.3 耦合瓶颈延迟 `coupled_bottleneck_delay_score`

该指标描述电气恢复价值、任务严重度、关键负荷优先级和资源可达延迟是否叠加。

当前可简化理解为：

```text
coupled_bottleneck_delay_score
=
Σ value_share_i
× normalized(max(heavy_arrival_i, permanent_capable_arrival_i))
× priority_factor_i
× severity_factor_i
```

其中：

| 项                             | 含义                    |
| ----------------------------- | --------------------- |
| `value_share_i`               | 任务 i 的恢复价值占比。         |
| `heavy_arrival_i`             | 重型资源到任务 i 的最早到达时间。    |
| `permanent_capable_arrival_i` | 能够永久修复任务 i 的最早资源到达时间。 |
| `priority_factor_i`           | 关键负荷任务权重更高。           |
| `severity_factor_i`           | 严重故障任务权重更高。           |

该指标的核心含义是：

> 越重要、越严重的任务，如果越需要等待高能力资源晚到，则交通–电网–资源耦合弱联显化越强。

这是当前指标体系中最能体现“高代价恢复决策”的单次灾害显化指标。

---

## 3.4 临时/永久恢复错配 `temporary_permanent_mismatch_score`

该指标描述轻型资源早到但只能临时恢复、常规或重型资源晚到才能永久修复之间的时间窗口。

当前计算逻辑为：

```text
temporary_permanent_mismatch_score
=
0.50 × temporary_recovery_opportunity_index
+ 0.25 × norm(avg_temporary_window_h, 72h)
+ 0.25 × permanent_capability_delay_score
```

其中：

| 指标                                     | 含义                                   |
| -------------------------------------- | ------------------------------------ |
| `temporary_recovery_opportunity_index` | 高价值任务是否存在“轻型资源先到、可临时恢复、永久修复资源晚到”的机会。 |
| `avg_temporary_window_h`               | 平均临时/永久恢复时间窗口。                       |
| `permanent_capability_delay_score`     | 能够永久修复任务的资源到达延迟。                     |

该指标对应项目 overview 中的临时恢复与永久恢复机制：轻型资源不应被假设为完整永久修复资源，而应主要用于早期进入、巡检、隔离、临时处置和部分恢复；真正永久修复则依赖小型、常规或重型资源。

---

## 4. 第三层：多灾害响应画像

单次灾害显化指标仍然具有偶然性。一个结构可能本身弱联，但某一次灾害没有击中关键瓶颈，因此显化不明显；也可能某个结构本身不算弱联，但遇到极端灾害后恢复困难。

因此，v0.5 不使用单次灾害结果定义弱联程度，而是在同一个固有结构上生成多个标准随机灾害，形成响应画像。

对应输出文件为：

```text
weaklink_response_profile.csv
```

对于每个固定结构，响应画像包括：

| 指标                           | 含义                            |
| ---------------------------- | ----------------------------- |
| `mean_manifestation_index`   | 多个随机灾害下的平均显化强度。               |
| `p90_manifestation_index`    | 显化强度的 90 分位值，表示高风险灾害下的弱联显化水平。 |
| `cvar90_manifestation_index` | 最差 10% 灾害下的平均显化强度，表示尾部风险。     |
| `activation_probability`     | 弱联显化指标超过阈值的概率，表示瓶颈被随机灾害激活的概率。 |

其中，当前默认的激活阈值为：

```text
weaklink_manifestation_index ≥ 0.30
```

如果某一结构在 20 个随机灾害中有 8 个灾害的 `weaklink_manifestation_index` 超过 0.30，则：

```text
activation_probability = 8 / 20 = 0.40
```

该指标回答的问题是：

> 这个结构在标准随机灾害分布下，有多大概率表现出明显弱联瓶颈激活？

---

## 5. 最终统计弱联程度 `statistical_weaklink_degree`

当前 v0.5 中，最终用于弱联分类的综合指标是：

```text
statistical_weaklink_degree
```

它不是纯结构指标，也不是单次灾害指标，而是由结构基础和多灾害响应画像共同组成：

```text
statistical_weaklink_degree
=
0.40 × structural_weaklink_basis
+ 0.25 × mean_manifestation_index
+ 0.20 × activation_probability
+ 0.15 × cvar90_manifestation_index
```

各组成部分含义如下：

| 组成部分                         |   权重 | 含义                   |
| ---------------------------- | ---: | -------------------- |
| `structural_weaklink_basis`  | 0.40 | 结构本身的低冗余、低替代和瓶颈耦合程度。 |
| `mean_manifestation_index`   | 0.25 | 标准随机灾害下的平均弱联显化强度。    |
| `activation_probability`     | 0.20 | 弱联瓶颈被随机灾害激活的概率。      |
| `cvar90_manifestation_index` | 0.15 | 最差 10% 灾害下的尾部显化风险。   |

该公式体现当前阶段的核心判断：

> 弱联程度应由结构基础和随机灾害响应画像共同刻画。结构指标保证弱联具有固有系统属性，灾害画像刻画这种结构在灾后恢复过程中的统计显化倾向。

换句话说，弱联不是某一次灾害导致的恢复慢，也不是纯静态网络拓扑指标，而是：

```text
固有结构瓶颈
+
标准灾害扰动下的平均显化
+
瓶颈激活概率
+
尾部高代价风险
```

共同构成的统计恢复脆弱性。

---

## 6. 弱联分类：设计等级与测量等级

当前 v0.5 中存在两个弱联等级概念。

### 6.1 设计等级 `designed_structure_level`

这是场景生成器输入的标签：

```text
designed_structure_level ∈ {low, medium, high}
```

它表示我们在生成结构时希望得到低、中、高三类结构弱联场景。

但是，设计等级本身不能直接作为最终结论。否则会变成“我们说它是 high，它就是 high”。

---

### 6.2 测量等级 `measured_weaklink_class`

测量等级由 `statistical_weaklink_degree` 计算得到：

```text
measured_weaklink_class ∈ {low, medium, high}
```

当前原型中采用经验分位数分类方法，即将所有结构的 `statistical_weaklink_degree` 按三分位划分：

| 统计弱联程度位置 | 测量等级   |
| -------- | ------ |
| 底部 1/3   | low    |
| 中间 1/3   | medium |
| 顶部 1/3   | high   |

这样可以避免纯粹依赖生成器标签，并检验设计结构和测量分类是否一致。

如果某个结构设计为 high，但在标准随机灾害画像中长期显化不明显，它的 `measured_weaklink_class` 可能会被测为 medium；反之，如果某个 medium 结构在随机灾害中频繁激活耦合瓶颈，也可能被测为 high。

这正是我们引入随机灾害画像的原因。

---

## 7. 当前指标体系的文件输出

v0.5 中与弱联指标相关的主要输出文件包括：

```text
results/summary/structural_metrics.csv
results/summary/hazard_manifestation_metrics.csv
results/summary/weaklink_response_profile.csv
results/summary/weaklink_classification.csv
```

各文件含义如下：

| 文件                                 | 内容                              |
| ---------------------------------- | ------------------------------- |
| `structural_metrics.csv`           | 每个固有结构的结构弱联基础指标。                |
| `hazard_manifestation_metrics.csv` | 每个结构–灾害组合下的单次弱联显化指标。            |
| `weaklink_response_profile.csv`    | 每个结构在多个随机灾害下的均值、分位数、CVaR 和激活概率。 |
| `weaklink_classification.csv`      | 每个结构的最终统计弱联程度和测量分类结果。           |

其中，最重要的是：

```text
weaklink_classification.csv
```

它汇总了：

```text
structure_id
designed_structure_level
structural_weaklink_basis
mean_manifestation_index
activation_probability
cvar90_manifestation_index
statistical_weaklink_degree
measured_weaklink_class
```

该文件是当前弱联场景分类体系的核心输出。

---

## 8. 当前指标体系的解释逻辑

当前弱联指标体系可以用一句话概括：

> 先用结构指标描述系统是否低冗余、低替代、高瓶颈耦合；再用标准随机灾害集下的响应画像刻画这种结构瓶颈是否容易被激活、平均显化多强、尾部风险多大；最后将结构基础、平均显化、激活概率和尾部风险综合为统计弱联程度，用于划分弱联场景类型。

因此，弱联程度不是：

```text
某一次灾害恢复慢
```

也不是：

```text
道路坏得多
```

也不是：

```text
某个调度算法表现差
```

而是：

```text
结构上低冗余、低替代、高瓶颈耦合
并且在标准随机灾害集下更容易表现出可达延迟、
资源能力释放延迟、临时/永久恢复窗口和尾部高代价风险。
```

---

## 9. 当前指标体系的优点

当前 v0.5 指标体系相比早期版本有四个优点。

第一，它避免了把弱联等同于地理背景或单一灾害结果。弱联被定义为结构基础与随机灾害响应共同形成的统计属性。

第二，它避免了循环论证。弱联分类不使用调度算法的 AUC、完成时间或恢复曲线结果，因此不会出现“用算法结果定义场景，再证明算法有效”的问题。

第三，它保留了灾后调度问题的现实性。仅看静态结构不足以说明灾后恢复困难，因此引入标准随机灾害画像，使弱联程度能够体现可达延迟、资源释放延迟和临时/永久恢复窗口。

第四，它与后续调度算法研究兼容。当前指标体系可以先定义和筛选弱联场景，后续再在不同 `measured_weaklink_class` 下比较调度策略表现。

---

## 10. 当前指标体系的局限与后续改进方向

当前指标体系仍然是原型版本，后续还需要继续增强。

第一，电网结构弱联仍然偏任务价值 proxy。后续应加入更电力系统化的拓扑指标，例如配电网割边、备用供电路径、关键负荷到电源路径依赖、孤岛化风险和拓扑重构能力。

第二，交通–电网耦合弱联目前主要基于最短路径是否经过结构瓶颈。后续可以升级为多路径、割集或可靠性视角下的瓶颈依赖指标。

第三，资源结构弱联目前主要基于资源类型能力是否满足任务需求。后续可以加入资源数量、仓库位置、资源覆盖半径、资源可替代性和多资源并行能力。

第四，`statistical_weaklink_degree` 的权重目前是经验设定。后续需要做权重敏感性分析，验证弱联分类是否对权重变化保持稳定。

第五，当前分类采用三分位数方法，适合原型阶段。后续如果指标稳定，可以考虑固定阈值分类，或者基于聚类方法识别弱联类型。

---

## 11. 当前阶段的结论

截至 v0.5，项目中的弱联指标体系已经从“单次灾害恢复困难”推进为“结构基础 + 标准随机灾害响应画像”的统计分类框架。

当前弱联程度由四部分构成：

```text
statistical_weaklink_degree
=
0.40 × structural_weaklink_basis
+ 0.25 × mean_manifestation_index
+ 0.20 × activation_probability
+ 0.15 × cvar90_manifestation_index
```

其中：

```text
structural_weaklink_basis
```

描述系统固有低冗余、低替代和瓶颈耦合；

```text
mean_manifestation_index
```

描述标准随机灾害下的平均弱联显化强度；

```text
activation_probability
```

描述弱联瓶颈被灾害激活的概率；

```text
cvar90_manifestation_index
```

描述最不利灾害条件下的尾部高代价风险。

因此，当前的弱联场景分类不再依赖单次灾害结果，而是通过结构指标和多灾害响应分布共同定义。这为后续研究瓶颈感知、资源匹配、阶段协同、等待/转移和临时/永久恢复调度算法提供了更科学、更可重复的场景基础。
