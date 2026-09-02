# R9 Method Selection Justification（修订前审查）

> 性质：`principle_human_selection` pending 阶段的非正式 Method 选择论证。本文只审查 R9，不修改 `METHOD_DESIGN_PACKET.json`、`METHOD_SYNTHESIS_COMPLETE_R9.md`、Controller State、Harness 或任何 Gate。结论不表示尚未执行的实验、覆盖、稳定性或实时性已经成立。

## 0. 结论先行

R9 的**问题方向是对的，方法骨架也具有顶会顶刊潜力，但按新增的三层标准，目前还不能判定为三项同时成立**：

| 层次 | 当前判断 | 核心理由 |
|---|---|---|
| Problem fit | **成立** | R9 直接作用于 accepted RCA/RMC：历史/动作混叠导致的错误接触决策，以及性能选择与可行施加脱节；并覆盖校准外材料、几何、滑动、历史变化和全部目标/约束。 |
| Method fit / necessity | **部分成立，尚未闭合** | `decision-sufficient predictive contact operator`、显式 calibration-out authority、带约束的多步阻抗决策具有问题驱动的必要性；但纯 learned nominal model、GRU/TCN、EW-RLS、RTI-SQP 尚不能证明“为什么必须是它们”。更自然的名义接触表示很可能是 **structured hybrid / grey-box**，而非默认纯神经模型。 |
| Scientific strength | **有条件地足够，尚不能最终通过** | “learned/identified interaction model → MPC/VIC”已被 2020、2022、2023、2025 工作覆盖。全文核查显示，2025 工作的 MPC 优化法向 jerk，而阻抗由安全模式函数调节，并未覆盖 R9 的法向–切向四增益联合优化。R9 仍可能有强 scientific delta，但必须锚定 continuous-scan decision sufficiency、有限面积 n–t capacity、校准外决策恢复和 total applied-action relation。 |

因此，本轮正确结论不是“保留或否定 R9”，而是：**保留 R9 的核心因果链；撤销对若干具体实现的必要性暗示；在下一轮 Method revision 前完成 hybrid contact operator、calibration-out adaptation 和 horizon necessity 的最小竞争比较。**三篇 MPVIC 全文已经足以完成当前 Method-selection 层面的 closest-prior 定位；最终新颖性阶段仍需更广的系统检索，但不是本轮任务。

## 1. 审查基准：什么必须由研究问题决定

accepted problem 要求在连续软接触扫描中，根据在线观测选择时变法向–切向阻抗，在 calibration-out 的材料、几何、滑动条件和接触历史变化下，联合优化力调节、轨迹跟踪、失接触/滑移，同时满足 wrench、交互能量、执行器和实时约束。

accepted RCA/RMC 进一步规定两处必须修复的因果结构：

1. **contact-state aliasing**：相同或相近的瞬时量可能对应不同松弛、接触面积、压力分布和剩余切向/扭转载荷能力；接触表示必须保留会改变可行阻抗或其排序的历史与动作信息。
2. **selection–feasibility decoupling**：先按性能选择、再由外部 guard 修正，会使实际施加动作不再是原优化关系的解；性能、硬约束、计算结果和撤离必须闭合在同一 applied-action relation 中。

这两点决定的是**信息接口和决策关系**，并不直接决定“必须用神经网络、GRU、EW-RLS 或 SQP”。Method-fit 审查必须保持这一层次区别。

## 2. 为什么需要 predictive contact representation，但不等于为什么需要纯 learned model

### 2.1 三类路线的正面比较

| 路线 | 能自然表达的内容 | 主要优点 | 对当前问题的关键不足 | Method-fit 结论 |
|---|---|---|---|---|
| 更完整的物理 contact model | 有限接触面积、曲率、压力分布、黏弹松弛/蠕变、摩擦与扭转载荷极限面 | 可解释；在参数、几何和接触模式正确时有较强外推结构；可直接导出物理裕度 | 需要在线获得难辨识的材料、几何、压力/接触面积和摩擦参数；安全连续扫描通常没有充分激励；模型失配会系统性移动 active constraints；覆盖多材料、速度、历史与摩擦状态后状态和参数复杂度上升 | **不能排除，且必须作为强竞争路线**；直接从 HC 升级到 richer physics 是合理而非稻草人替代 |
| 纯 learned predictive model | 从观测–动作历史直接预测未来力、运动、接触/滑移、capacity 和负载 | 可以吸收难以显式参数化的耦合与传感代理；预测目标可对齐实际阻抗决策；固定计算图便于统一多输出 | “神经网络会泛化”不成立；calibration-out 时缺少物理外推保证；高数据需求、不可辨识与不确定性失真会直接危及约束；可能学习与决策无关的细节 | **目前不足以证明是最自然路线**；不能作为 R9 的必要性结论 |
| structured hybrid / grey-box predictive operator | 已知机器人/阻抗动力学、接触几何与能量/容量结构 + 学习难以辨识的 residual、latent memory 或未知 constitutive map | 保留物理可解释约束与外推骨架；把学习容量集中在 calibration-out 真正未知且影响决策的部分；仍可直接输出 decision-relevant joint outcomes | 必须决定哪些结构足够可靠；错误物理先验也会造成偏差；需要验证 residual class 对所声明变化的可表示性 | **当前最自然的首选**，但仍需与纯 physics、纯 learned 做 matched-budget 判别 |

### 2.2 为什么不直接采用 finite-area、geometry/friction/history-aware 完整物理模型

有限面积黏弹接触证据确实表明：接触半径和压力分布随时间演化，法向力变化会使切向力–法向力矩极限面收缩或扩张；软接触扫描中的滑移能力不是一个固定摩擦锥，point-contact HC 会遗漏真实影响阻抗决策的状态。因此，升级物理模型可以直接修复一部分 aliasing。

但“存在更高保真物理模型”不等于“它在当前在线控制条件下形成了可识别、可实时、可跨 calibration-out 变化使用的决策模型”。完整物理路线要成立，至少需要同时在线获得或可靠观测：局部几何/曲率、接触面积或压力形态、黏弹松弛谱、摩擦状态及其随速度和历史的变化。现有软接触 MPC 先例能够在**已知材料性质和特定球–平面/滑动假设**下实时使用高保真模型，却没有证明这些量可在安全连续扫描中、跨新材料与几何被充分辨识。软组织建模研究也明确指出模型选择必须在准确度、可解释性、稳定性与计算可行性之间权衡；甚至在线辨识常因缺乏持续激励而只能更新最敏感的少数参数。

所以 learned component 的真正理由不是“网络更强”，而是：

1. 当前决策需要的是候选阻抗作用下的未来 joint outcome/capacity，不是恢复唯一的材料本构真值；
2. 多个物理参数对现有传感可能不可分别辨识，却可以通过其对 active margin 和动作排序的合成作用被预测；
3. calibration-out 的变化轴会改变难以在线测量的 residual relation，必须允许观测–动作数据直接修正该 relation；
4. 只学习低维 decision-relevant residual，可减少对完整场变量重建和在线高维参数辨识的依赖。

这些理由支持**学习型 residual / latent predictive component**，不支持默认的纯 learned nominal head。R9 当前形式

\[
\hat o=\bar o_\psi(z,A)+\Phi_\psi(z,A)\theta
\]

本身允许向 hybrid 解释发展：把可靠的机器人动力学、阻抗映射、几何可观测量、能量平衡和有限面积 capacity 结构显式写入 nominal operator，只由学习模型补足 constitutive/memory residual。下一轮应比较并固定这一分工，不能继续把 `\bar o_\psi` 的纯神经实现默认为科学核心。

### 2.3 最终判断

- **必要的是**：action-conditioned、history-aware、直接面向 constrained impedance decision 的预测接触算子。
- **有充分理由加入的是**：学习型 residual 或潜在记忆，以处理无法由安全在线物理辨识唯一恢复的 calibration-out 差异。
- **尚未证明必要的是**：纯 learned nominal contact model。
- **当前最自然的候选形态是**：structured hybrid decision-sufficient contact operator。

## 3. 如果采用 learned component，为什么需要 temporal representation，为什么还不能固定 GRU/TCN

### 3.1 任务真正要求的历史表示

当前任务不需要“尽量重建全部过去”，而需要一个因果状态 \(z_k\)，使得给定候选未来阻抗序列 \(A_k\) 后，能够预测会改变以下对象的历史效应：

\[
\big(\mathcal F_k(A),\; J_k(A),\; g^{\rm contact/slip/wrench/energy}_k(A)\big).
\]

换言之，history representation 的最低要求是：

1. **causal**：部署时只用当前及过去观测；
2. **action-conditioned**：区分相同观测但不同历史施加阻抗导致的未来反应；
3. **fading-memory 或显式内部状态**：覆盖松弛、蠕变、摩擦预滑/滞回和接触面积演化的任务时间尺度；
4. **decision-sufficient**：只保留会移动可行集、active margin 或候选阻抗排序的信息；
5. **fixed-cost recursive update**：满足实时滚动优化；
6. **uncertainty-compatible**：表示误差能进入支持判定或鲁棒裕度，而非只给点预测。

### 3.2 GRU/TCN 与替代方案

GRU 和 causal TCN 都能实现有限计算的非线性 fading memory，因此是合理实现：GRU 用递归隐状态自然适配流式更新；TCN 可并行训练并用有限感受野控制 WCET。但现有证据不足以证明二者相对于下列方案具有 Method-level necessity：

- 由广义 Maxwell/Prony/QLV 模型产生的显式黏弹内部变量；
- 线性或非线性 state-space model、compact neural state-space model；
- predictive-state representation；
- 低阶 basis filters + learned residual；
- 面向混合接触/摩擦状态的 switching state estimator。

因此 R9 中“small GRU or causal TCN”应继续是**implementation freedom**。科学主张应停留在“可递归、动作条件化、决策充分的有限维因果状态”。只有 matched-budget 测试证明某个架构在相同数据、传感、时延和模型容量下对 feasible-set disagreement / decision regret 有稳定优势，才能进一步固定。

## 4. 为什么要 calibration-out adaptation；为什么 set-membership 合适，而 EW-RLS 不是核心

### 4.1 不能只做 detection 或 conservative refusal

研究问题明确要求在 calibration-out 材料、几何、滑动和接触历史变化下继续改善联合性能。仅检测 OOD、扩大固定裕度或长期 withdrawal，最多证明风险管理，不能回答“怎样在线选择阻抗”。因此，R9 将 `SUPPORTED`、`ADAPTING-IN-ENVELOPE` 和 `WITHDRAWAL-ONLY` 分权，并要求新观测实际更新预测/可行关系，这一点是 Problem/RMC 直接要求的 Method core。

### 4.2 set-membership 的 Method-fit

bounded set-membership 与当前问题匹配的地方不是“算法简单”，而是它把**硬约束所需的语义**直接保留下来：若 drift、残差与表示包络成立，则参数集合应包含所有仍与数据一致的决策关系；硬约束对整个集合取最坏值，而估计中心只影响性能排序。这比单纯 covariance 或点估计更自然地回答 wrench、energy、actuator、contact/slip margin 是否对当前不确定性仍成立。

它同时具有重要边界：

- 若材料/几何变化是突变或接触模式切换，单一 bounded random-walk 扩张可能过慢、过大或直接交空；
- 多个变化轴可能不满足同一个低维 affine residual class；
- 安全动作可能缺乏识别 active directions 所需的 persistent excitation；
- worst-case set 会在高维迅速保守，导致“安全但无法连续扫描”。

因此，**set-valued authority 是必要语义，当前具体的单模型漂移集合更新仍需与 change-point / multiple-model set、robust MHE 或结构化 Bayesian filter 比较**。比较目标不是平均参数误差，而是 containment、feasible scan retention、active-margin error 和 decision recovery time。

### 4.3 projected EW-RLS 的正确角色

在 R9 的 affine low-dimensional residual 假设下，projected EW-RLS 具有固定成本、递归更新和 tracking drift 的现实优势，适合计算 posterior/nominal center。但它没有单独提供 hard-set containment，也未解决 abrupt mode change、非线性 residual 或有限激励。因此：

- **Method core**：calibration-out 后由 admissible interaction 更新 decision relation，并以 set-valued uncertainty 约束动作；
- **合理实现**：projected EW-RLS 更新用于 expected cost 和排序的中心；
- **尚待比较**：single drifting set vs change-aware/multiple-model set；EW-RLS vs MHE/Bayesian/other fixed-cost center estimator。

若下一轮保留 EW-RLS，应将其写成满足固定接口的默认实现，而不是 Contribution。

## 5. 为什么是 receding-horizon constrained optimization；为什么 RTI-SQP 不是科学必然

### 5.1 MPC 与当前决策结构的匹配

R9 的阻抗动作会通过黏弹记忆、gain-rate、energy tank、接触保持和未来滑移能力跨多个时刻产生效果。目标又同时包含法向力、切向路径、失接触/滑移和增益变化，并有 wrench、energy、actuator、gain-rate 等硬约束。对这种“当前动作改变未来状态和未来可行性”的问题，receding-horizon constrained optimization 相比下列路线更自然：

- 固定增益表或 gain scheduling：不能针对在线历史和 active constraints 重排动作；
- 一步 QP：只有在未来效应可忽略或被等价终端项概括时才足够；
- 直接 actor/critic 或 end-to-end RL：可能加速执行，但难以原生保持新支持集合下的显式 hard constraints，且每次目标/约束改变会引入策略逼近误差；
- Bayesian optimization：适合慢速 trial-level 调参，不适合每个 gain update 的多步、硬约束、强时限决策；
- 单独 robust/adaptive control law：可以给出某类稳定/跟踪保证，但未必直接解决四增益、多目标、混合 scan/withdraw 的在线有限时域选择。

2022 Q-LMPVIC 与 2023 deep MPVIC 已直接说明“学习交互动力学 → MPC/其策略近似在线优化 variable-impedance parameters”是成熟可行的机器人控制范式；2025 MPVIC 则说明“在线 HC 辨识 → 模式化 VIC + 线性化 MPC + passivity”适用于未知软环境的法向交互。三者证明相关 predictive-control 范式**可以用**；R9 的 Method-fit 则来自当前问题确有 horizon coupling、四维 n–t impedance action 与显式多硬约束，而不是来自先例数量。

MPC 必要性的最强、也可证伪的竞争者是**matched-model one-step constrained optimization**。若在任务时间尺度内，一步方法加适当终端/能量项能得到同样的 feasible set、阻抗动作与联合性能，那么多步 MPC 的复杂度就不必要。因此下一阶段必须保留 one-step QP/NLP ablation。

### 5.2 RTI-SQP 的角色

RTI-SQP 对短时域、可微非线性 hybrid-relaxed 模型、warm start 和固定迭代预算很合适；相较 CEM，它更容易利用梯度、active constraints 和局部可行恢复；相较在线 actor-critic，它不把每次 constraint change 隐藏在策略近似中。但 RTI-SQP 并非唯一方案：线性化充分时可用 QP，非光滑/混合性强时可能需要 SCP、multiple shooting、sampling MPC 或显式分层求解。

R9 真正需要固定的是：

1. 求解器能够在 deadline 内给出已完整核验的 feasible iterate；
2. 未收敛时只损失性能，不失去可施加动作；
3. hard margins 使用当前 uncertainty authority，而非事后 guard；
4. WCET 和 withdrawal reserve 可实测。

所以 **constrained receding-horizon decision 是 Method-level 选择；RTI-SQP 是当前最合理的默认实现，但应降级为 implementation choice**。

## 6. 真正的 baseline / closest prior

### 6.1 NNBO 的正确位置

NNBO 仍然重要，因为它是本研究的原始起点与 genealogy：标量 point-contact Hunt–Crossley 环境参数通过 EWRLS 识别，随后用 critic/HJB 导出单轴软环境最优阻抗/力–位置关系。它非常适合回答“R9 相比原路线究竟改变了什么”，也适合作为表示、法向单轴和 unconstrained decision 的机制 ablation。

但 R9 已经变成“预测/辨识交互关系 → 在线优化 variable impedance → 约束与适应”的体系。按方法结构而非项目历史，NNBO **不再是唯一或主要 closest prior**。

### 6.2 MPVIC 谱系的直接程度

| Prior | 与 R9 的直接重叠 | 与 R9 的关键差距 | 在比较中的位置 |
|---|---|---|---|
| Roveda et al., 2020 MBRL-VIC | ensemble ANN 学习 interaction dynamics，CEM-MPC 在线调 stiffness/damping，并周期在线更新模型 | pHRC effort objective；无连续软接触有限面积/滑移问题；约束只被说明“可加入”，未形成 R9 的 hard authority | learned-model MPC genealogy / baseline |
| Roveda et al., 2022 Q-LMPVIC | ensemble interaction model + MPC；Lyapunov constraints；actor–critic 逼近 exact MPC 以实时执行 | pHRC 垂直单轴；优化 setpoint/damping；主要目标是 human effort；不处理 R9 的 calibration-out soft-contact capacity 与联合 n–t constraints | learned dynamics + constrained/fast MPC closest architectural prior |
| Anand et al., 2023 deep MPVIC | probabilistic ensemble generalized Cartesian impedance model；CEM-MPC 在线调 stiffness；跨任务 transfer | 学的是机器人 free-space generalized impedance dynamics；部署 horizon/频率有限；没有 soft-contact calibration-out adaptation、显式 passivity/hard constraints 或 n–t finite-area capacity | learned generalized model + MPC closest architectural prior |
| Xue et al., 2025 MPVIC | 单轴法向 HC 参数在线估计；安全/过渡/性能模式调节刚度和阻尼；energy tank/passivity；线性化 MPC、位置约束、100 Hz；硅胶与人臂 ultrasound-like 扫描及 bounded position shifts | 初始接触位置/方向已知且环境方向不变；安全主要定义为力与力变化率；忽略切向滑移、有限面积 capacity、wrench/actuator/deadline；HC 为瞬时压入–速度模型；MPC 输入是位置误差 jerk，四维 n–t impedance 不是 MPC 决策变量；估计器给参数点估计而非 constraint-authority set | **任务域最接近的 integrated prior；不是 R9 阻抗决策结构的完全同构 prior** |
| Jin et al., model-predictive VIC | 直接以 impedance parameters 为控制输入；一/多步 MPC；impedance/state constraints；energy tank/passivity correction | 不学习/适应连续软接触关系 | constrained/passivity component baseline |
| deformable-contact MPC | 显式法向/滑动摩擦软接触动力学进入实时约束优化 | 材料性质已知、特定接触几何；阻抗不是在线主要决策变量 | richer-physics contact-planning baseline |

### 6.3 推荐的 baseline 结构

不存在一个单独 baseline 能回答全部 Method-fit。后续比较应形成最小而有诊断力的 baseline set：

1. **Xue 2025 MPVIC**：主要 task-domain integrated closest prior，检验 R9 相对 HC estimator、mode-based VIC、passivity 和 ultrasound disturbance handling 的增量；
2. **Anand 2023 deep MPVIC**：actual impedance-parameter optimization 的主要 architectural closest prior；
3. **Roveda 2022 Q-LMPVIC**：learned interaction dynamics、Lyapunov constraint 与实时近似求解基线；
4. **NNBO**：原始 genealogy 与 scalar HC / critic mechanism baseline；
5. **richer-physics deformable-contact MPC 或 finite-area grey-box variant**：检验 learned/hybrid representation 的必要性；
6. **R9 one-step constrained variant**：检验多步 MPC 的必要性。

### 6.4 R9 相对 closest priors 仍可能成立的 scientific delta

“learned model + MPC”“online HC environment estimator”“mode-based VIC”“energy-tank passivity”均不能再单独作为 R9 的创新主张。三篇全文同时说明，当前不存在一个单一 prior 与 R9 完全同构：Xue 2025 最接近任务域，Anand 2023 最接近由 MPC 直接选 impedance，Roveda 2022 最接近 learned dynamics + constrained/fast predictive decision。R9 的可辩护增量应收缩并强化为：

1. **continuous-scan decision sufficiency**：以候选法向–切向阻抗下的 future feasible set、active capacity margin 和 decision regret 定义 contact representation，而不是预测一般机器人动力学或单一环境参数；
2. **calibration-out decision recovery**：显式区分 supported statistical authority 与包络内 adaptive set authority，使新材料、几何、滑动与历史变化实际更新可行阻抗关系，而不是只处理 bounded position disturbance 或检测后拒绝；
3. **finite-area n–t joint decision**：联合选择 \((k_n,d_n,k_t,d_t)\)，把接触保持、滑移和法向力矩 capacity 与 force/path trade-off 放入同一 horizon；
4. **realized-action closure**：scan、withdrawal、energy、actuator、wrench、gain-rate 与 computation outcome 属于同一 applied-action relation。

这四项目前是**潜在科学增量**，不是最终新颖性结论。全文核查表明 Xue 2025 没有覆盖 history/action decision sufficiency、有限面积 n–t capacity、四增益 MPC action 或 deadline-total relation；但它已经覆盖在线 HC 参数估计、模式化安全/性能阻抗变化、约束可加入性和能量罐无源性，因此 R9 不得把这些单项机制重复包装成贡献。

## 7. 哪些选择已被充分支持，哪些应降级或重比

### 7.1 已有充分 Method-fit justification

- 预测对象应直接覆盖候选阻抗下的 joint outcomes 与 constraint margins，而不是只拟合材料标签或 scalar normal force law；
- history/action conditioning 应由 feasible-set 与 action-ranking 等价定义；
- calibration-out 必须更新 decision relation，且其 authority 不得冒充 supported calibration coverage；
- hard constraints 应对 set-valued uncertainty 而非点估计成立；
- 多时刻影响确实存在时采用 constrained receding horizon；
- scan 与 withdrawal、求解结果和实际施加动作在同一 relation 中闭合；
- energy、wrench、actuator、gain-rate 和 deadline 是 accepted Target Constraints 的必要条件，不是装饰性模块。

### 7.2 只是合理实现，不能写成“为什么是它”

- GRU 或 causal TCN；
- projected EW-RLS 作为 residual center estimator；
- ellipsoid 或 zonotope 外逼近；
- RTI-SQP；
- 50–100 Hz、具体 horizon、latent/adapter 维数和网络宽度；
- leverage score 的具体支持度量，只要替代方案仍满足 pre-outcome、episode-aware support contract。

### 7.3 应重新比较或可能替换的部分

1. **纯 learned nominal head**：优先与 structured hybrid/grey-box nominal operator 比较；当前证据偏向 hybrid 是更自然起点。
2. **单一 bounded-drift affine residual**：与 abrupt change / mode-switch aware multiple-model set 或 robust MHE 比较；否则材料切换与摩擦模式改变可能只能 withdrawal。
3. **多步 MPC**：与 one-step constrained rival 做必要性检验；若 horizon coupling 不产生决策差异，应简化。
4. **RTI-SQP**：保留为默认工程实现，不进入 Method contribution；若非光滑 mode/capacity 使局部线性化 remainder 过保守，应换求解形式而非维护既定选择。
5. **2025 prior overlap**：全文已确认“未知软环境 HC 估计 + robust/passive interaction + safety switching + ultrasound-like disturbance experiment”属于 prior；R9 的区别必须明确是 set-valued calibration-out decision authority、n–t joint impedance action 和 realized-action closure，而不是笼统的 adaptive/passive MPVIC。

## 8. 最终三层判定

### 8.1 Problem fit：通过

R9 没有偏离 accepted problem。它不仅处理预测准确度，还明确处理校准外适应、法向–切向联合目标、接触/滑移、wrench/energy/actuator/实时约束，并直接闭合 accepted 两条 RCA/RMC。相比“换一个更复杂环境模型”，它更准确地把问题定位为 representation–adaptation–decision relation 的联合失配。

### 8.2 Method fit / necessity：条件通过，当前整体未完成

核心路线

`decision-sufficient predictive contact relation → authority-aware calibration-out adaptation → constrained receding-horizon normal–tangential impedance decision → total applied action`

具有较强必要性，而且比把 incumbent 或 viability 提升为论文主线更自然。但 R9 尚未证明：纯 learned nominal head 优于 hybrid、单一 drift set 足以覆盖所声明变化、多步 MPC 相对一步 constrained rival 确有不可替代价值。故本层只能判 **PARTIAL / REVISION REQUIRED**。

### 8.3 Scientific strength：有强潜力，但须在 Method fit 闭合后重判

如果 hybrid/learned 分工、calibration-out adaptation class 与 horizon necessity 得到竞争性论证，R9 的 decision-sufficient finite-area n–t contact representation、authority-correct adaptation 和 realized-action closure 可以形成足够集中的论文级科学主张。反之，如果 richer physics + simpler estimator + one-step constrained control 已能同样解决问题，R9 当前复杂度会失去必要性。Xue 2025 已覆盖 adaptive HC estimation、robust/passive normal interaction 和安全模式切换，但没有覆盖上述 R9 核心增量；所以 scientific strength 的主要未决项已经从“closest-prior 身份不明”转为“这些新增结构相对更简单竞争路线是否必要”。

**综合判定：R9 当前不满足“Problem fit + Method fit/necessity + Scientific strength 三项同时成立”。**它不是方向错误，而是 Method selection justification 尚未闭合。最小后续工作不是增加模块，而是完成三项判别：

1. pure physics vs pure learned vs structured hybrid 的 decision-level comparison；
2. single drifting set vs change-aware adaptive set 的 calibration-out fit；
3. one-step constrained decision vs multi-step MPC 的 horizon necessity；

三篇 MPVIC 全文已完成本轮所需的 closest-prior overlap 核定。完成上述三项 Method-fit 判别之前，应维持 `principle_human_selection` pending，不进行 Human Selection，也不进入测试设计。

## 9. 本次证据边界

- 已重读 NNBO、2020 MBRL-VIC、2023 deep MPVIC、model-predictive VIC/energy-tank，以及当前 Evidence 中 finite-area viscoelastic contact、deformable-contact MPC、GP predictive force control 和软组织模型选择/在线辨识材料。
- Roveda 2022、Anand 2023 和 Xue 2025 均使用用户提供的出版版全文核对；关键公式、优化变量、适应机制、约束、分析、实现频率与实验边界均已直接检查。
- 本轮是 Method-selection necessity 审查，不是最终系统文献新颖性审查；“潜在 scientific delta”仍须在后续正式 novelty 阶段接受更广泛 closest-work 检索。

三篇直接 MPVIC 比较文献为：

1. L. Roveda, A. Testa, A. A. Shahid, F. Braghin, D. Piga, “Q-Learning-based model predictive variable impedance control for physical human-robot collaboration,” *Artificial Intelligence*, 312, 103771, 2022, DOI: 10.1016/j.artint.2022.103771.
2. A. S. Anand, J. T. Gravdahl, F. J. Abu-Dakka, “Model-based variable impedance learning control for robotic manipulation,” *Robotics and Autonomous Systems*, 170, 104531, 2023, DOI: 10.1016/j.robot.2023.104531.
3. J. Xue, W. Liang, Y. Wu, T. H. Lee, “Model predictive variable impedance control towards safe robotic interaction in unknown disturbance-rich environments,” *Robotics and Autonomous Systems*, 189, 104961, 2025, DOI: 10.1016/j.robot.2025.104961.
