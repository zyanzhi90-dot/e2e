# R15 Final Method：顶会顶刊级创新性审计与贡献重构

## 0. 审计结论

R15 的核心 novelty **未被 closest prior 击穿，但必须重新分层**。

不能再作为创新单列的内容包括：robotic ultrasound 中的 impedance control、online stiffness optimization、QP、energy tank、组织黏弹参数估计、MPC/MPVIC、joint trajectory--impedance planning、covariance/chance constraints、normal--tangential 坐标分解，以及 moving-reference energy accounting。这些机制均已有直接或相邻高水平 prior。

R15 真正可守住的 Scientific Delta 是一个此前尚未在目标领域闭合的整体关系：

> **将未来 probe-specific normal--tangential deformable-contact trajectory 从 nominal force/motion planner 的预测量，提升为 feedback impedance 的决策因果量；在同一 T-RO contact/robot optimization 中，未来接触 Jacobian、deviation propagation、contact constraints 与 modulation-energy authority 共同决定 \((\tau_{ff},k_n,k_t)\) 的可发布首节点。**

因此，R15 应被包装为 **future-contact-conditioned closed-loop impedance co-design**，而不是“contact MPC + variable impedance + uncertainty + energy tank”。前者是 scientific mechanism；后者只是 realization ingredients 的列表。

最终判断：

- **Problem fit：PASS。** 宽泛的“超声在线变阻抗”已被 Beber ICRA 2024、Fu TIM 2024/Fu T-RO 2025 覆盖；R15 应只声明“当前测量尚相同而未来滑动接触不同”时的 anticipatory normal--tangential impedance selection。
- **Method fit / necessity：CONDITIONAL PASS。** 该结构是 T-RO predefined impedance 缺口的最小闭合方式；但只有 matched replay 能证明 horizon 与 \(k_t\) 改变 action/outcome 时，复杂度才成立。
- **Scientific strength：strong formulation/algorithmic potential，moderate current theory。** 它足以形成顶级机器人会议/期刊候选，但目前不是一篇以新普适稳定性定理为主的 control-theory contribution。顶刊强度取决于三项正式命题和 killer-baseline 实验是否成立，见第 7 节。

---

## 1. Novelty claim 的正确分析单位

### 1.1 不能按组件判断

R15 中每个名词都能找到 prior：

| 组件 | 已有 closest prior | 审计结论 |
|---|---|---|
| deformable-contact-aware MPC | Wijayarathne et al., T-RO 2023 | 不是 novelty；是 \(M_0\) 主干 |
| cylinder probe/sliding deformation model | AuSoScan, TMECH 2025 | 不是 novelty；是 target geometry correction |
| arbitrary-surface impedance coordinates | Dyck et al., RA-L 2022 | 不是 novelty；说明 surface-frame n/t control 已存在 |
| ultrasound variable impedance + tissue model + QP + tank | Beber et al., ICRA 2024 | 不是 novelty；是最强 target-domain prior 之一 |
| medical online stiffness QP + tank | Fu et al., TIM 2024 | 不是 novelty；是 strongest reactive baseline |
| medical shared control + moving-reference energy accounting | Fu et al., T-RO 2025 | 不是 novelty；提供完整 robot-side power term |
| future impedance sequence optimization | Anand et al., RAS 2023; Jin et al., TMECH 2023 | 不是 novelty；MPVIC 已存在 |
| mean/covariance impedance optimization | Haninger et al., RAS 2023 | 不是 novelty；covariance 受 impedance 影响已被利用 |
| constrained stiffness/damping planning | Pollayil et al., T-RO 2023 | 不是 novelty；扰动响应约束下在线选 gains 已存在 |
| learned predictive impedance + Lyapunov constraints | Roveda et al., Artificial Intelligence 2022 | 不是 novelty；预测、uncertainty 与 stability-constrained gain optimization 已存在 |
| soft-environment optimal impedance + critic/HJB | Kong et al., IEEE TSMC 2025 | 不是 novelty；HC 环境上的 optimal impedance 已存在 |
| constrained predictive impedance QP | Jin et al., ICRA 2022; Ducaju et al., TASE 2025 | 不是 novelty；QP/SCBF/LPV prediction 已存在 |
| soft-environment estimator + MPC/VIC + tank passivity | Xue et al., RAS 2025 | 不是 novelty；组合本身也已存在 |

### 1.2 可以成立的分析单位

R15 的创新单位不是任一单独组件，而是以下 **target-specific closed-loop relation**：

\[
\underbrace{\{\bar\delta_j,\bar v_{t,j},\partial F_{n,t}/\partial(p,v)\}_{j=k}^{k+H},\mathcal E}
_{\text{future probe--tissue contact}}
\rightarrow
\underbrace{A_{cl,j}(k_n,k_t),P_j}_{\text{feedback outcome}}
\rightarrow
\underbrace{\mathcal M_{force,slip,loss,\tau,E}}_{\text{joint future margins}}
\rightarrow
(\tau_{ff},k_n,k_t)^*.
\]

该关系同时改变 problem formulation、action semantics、dynamics、constraints 和 solver decomposition。它不是把完整 MPC、VIC 和 tank 串起来，而是把 T-RO 中“future contact 只影响 nominal torque”的单向关系改写为“future contact 同时决定 nominal action 与 feedback mechanics”的闭环关系。

---

## 2. Target-domain closest-prior matrix

| Prior | 已有能力 | 对 R15 的直接 novelty threat | 尚未覆盖的 R15 relation | 结论 |
|---|---|---|---|---|
| T-RO 2023 deformable-contact-aware MPC | smooth normal/sliding contact dynamics；DDP/IK/Projection ADMM；future force/motion planning | 击穿 contact-aware MPC、sliding contact prediction 与 real-time distributed solver novelty | low-level impedance predefined；future contact 不选择 feedback gains | **backbone prior** |
| Dyck RA-L 2022 | arbitrary surface task coordinates；signed-distance/normal alignment；passivity-based ultrasound impedance | 击穿 surface-frame impedance、normal alignment 和 passive ultrasound contact novelty | fixed classical impedance；无 future contact-conditioned gain optimization | **geometry/control-coordinate prior** |
| Duan et al. RA-L 2022 | 多任务 constrained QP 协调 force、position、orientation、energy 与 posture；由医生示范学习 variable-impedance gains；真实 scoliosis ultrasound scans | 击穿 ultrasound optimization-based control、demonstration-derived variable impedance 与多目标扫描 novelty | QP 优化任务控制而非在线 gain；gain schedule 来自示范而非 future contact mechanics | **demonstration-based target-domain prior** |
| Beber ICRA 2024 | HC tissue map；online stiffness QP；force/penetration/energy constraints；tank；dummy-torso ultrasound and contact-loss tests | 击穿“组织参数驱动的超声 VIC”、QP、tank、force/penetration constraints | current-point/offline map relation；重点为 normal force；无 sliding n/t dynamics、horizon contact evolution 或 \(k_t\) decision | **strongest target-domain mechanism prior** |
| Fu TIM 2024 | closed-loop online stiffness QP；energy tank；linear probe；soft/hard transitions and continuous scan | 击穿 medical online stiffness optimization、QP real-time feasibility 与 ultrasound validation novelty | \(N=1\)、current force-error、normal-only decision、fixed high tangential stiffness | **strongest simple reactive baseline** |
| Fu T-RO 2025 | QP VIC + shared control；global tank；gain-change and moving-reference power；medical contact experiments | 击穿 global tank、moving-reference power accounting 和 shared-control packaging novelty | bilateral/haptic objective；single-step normal stiffness；无 autonomous future n/t contact relation | **energy-accounting prior** |
| AuSoScan TMECH 2025 | cylinder--plane normal/sliding contact model inside torque MPC；real ultrasound scanning | 击穿 probe geometry、soft-contact MPC 和 normal/sliding model novelty | action is joint torque；impedance is not optimized from future contact | **closest target-task model prior** |
| Xue et al. RAS 2025 MPVIC | HC estimator；multistep MPC；mode-based variable impedance；constraints；tank passivity；human-arm/ultrasound-like validation | 击穿 “MPC+VIC+soft model+constraints+passivity” 作为整体组合的 novelty | one-dimensional normal interaction；target impedance comes from safety/performance modes rather than probe sliding-contact horizon | **strongest adjacent integrated prior** |
| Hammoud et al. RA-L 2021 | risk-sensitive contact optimization jointly generates state/control trajectories and local feedback gains；unknown contact-location uncertainty produces anticipatory stiffness/damping schedules | 击穿一般的“未来接触/不确定性使阻抗提前变化” novelty | legged contact；无 continuous probe sliding、soft-tissue constitutive n/t state、force/slip/retention constraints | **closest contact-anticipation prior** |
| Haninger et al. RAS 2023 GP-MPIC | online trajectory/impedance optimization；belief mean/covariance；chance force and contact-stability constraints | 击穿 covariance propagation、uncertainty-conditioned impedance 与 joint trajectory/impedance planning novelty | learned force-over-state model；无 probe-specific deformable n/t contact、slip/retention relation或 T-RO decomposition | **closest predictive-authority prior** |
| Pollayil et al. T-RO 2023 | online stiffness/damping planning；perturbation-induced tracking bounds；gain constraints；analytic/numerical real-time solution | 击穿 constrained optimal impedance planning 与独立 stiffness/damping 选择 novelty | 不建模 contacted environment 或 probe sliding mechanics；无 force/slip/contact-loss decision | **closest model-based gain-planning prior** |
| Roveda et al. Artificial Intelligence 2022 Q-LMPVIC | learned pHRC dynamics/uncertainty；MPC objective；online setpoint/damping optimization；Lyapunov constraints；Q-learning approximation | 击穿 learned predictive impedance、online approximation 与 Lyapunov-constrained planning novelty | pHRC vertical interaction；无 physical probe/tissue constitutive state、n/t scanning constraints 或 tank relation | **closest learning-based predictive prior** |
| Kong et al. IEEE TSMC 2025 NNBO | nonlinear HC environment identification；HJB/single critic；optimal impedance；uncertainty compensation | 击穿 soft-environment optimal impedance 与 critic/HJB novelty | local one-direction contact；no future n/t scan constraints；full constrained reformulation destroys its closed-form main line | **closest optimal-control prior** |
| Jin et al. TMECH 2023 / ICRA 2022 | multistep variable impedance MPC；state constraints；bounded gains；coupled QPs；tank correction | 击穿 multistep gain optimization、bounded stiffness/damping 与 QP decomposition novelty | generic impedance dynamics；不从 future soft-contact mechanics jointly determine n/t gains | **closest optimization prior** |
| Ducaju et al. TASE 2025 | LPV impedance model；predictive impedance variation；SCBF safe-set constraints；convex QP | 击穿 “predictive constrained impedance QP” 和 modeled safe-set novelty | obstacle avoidance/free motion；无 soft contact constitutive state、force/slip/contact-retention constraints | **closest constrained-prediction prior** |

### 2.1 新补入的关键 prior：Beber ICRA 2024

Beber et al. 是本轮最重要的补检索结果。其 QP 直接优化 Cartesian stiffness，并把 HC 黏弹参数图转换成 force/maximum-penetration constraints，同时把 tank energy 与 extraction-rate constraints 写入优化；实验覆盖胸腔 dummy、肋骨/软组织差异、外扰和 contact loss。因此以下表述全部必须删除：

- “首次在 robotic ultrasound 中在线优化 stiffness”；
- “首次用 tissue mechanics 决定 ultrasound impedance”；
- “首次把 QP、physical contact constraints 与 energy tank 统一”；
- “首次在 contact loss 下保持 passive variable impedance”。

它没有出现 `prediction horizon`、future contact rollout、sliding/friction dynamics 或 tangential stiffness decision。其组织图是离线触诊后建立，QP 在当前点根据当前 impedance wrench 和能量状态做决策。因此它把 R15 的 novelty 边界压缩得更准确，却没有替代 R15 的核心因果链。

### 2.2 2025 integrated MPVIC 的反例压力

Xue et al. 已经把 nonlinear soft-environment estimation、multistep MPC、mode-based VIC、constraints 与 tank passivity 放在同一框架，并以 ultrasound-like surface interaction 说明应用。因此 R15 不能把“多个成熟机制形成统一框架”笼统写作 novelty。可区分之处必须落到公式：Xue 的 gain target 由当前 force/force-rate safety mode决定；R15 的 \((k_n,k_t)\) 由未来 probe sliding-contact trajectory 对 \(A_{cl},P\) 及 slip/retention margins 的 sensitivity 决定。

---

## 3. Adjacent predictive-impedance prior 的 novelty knockout

### 3.1 已被击穿的旧 claim

旧 contribution 2 暗示“mean--deviation/covariance 使 nominal zero-error 时 gains 获得 authority”本身是新贡献。该表述不能保留。Hammoud et al. 已由 risk-sensitive contact optimization 联合生成 state/control trajectory 与 local feedback gains，并在不确定接触前后形成 anticipatory impedance schedule；Haninger et al. 已显式令 impedance-dependent dynamics 传播 mean/covariance，并以 covariance cost、chance force constraint 和 contact-stability constraint优化 impedance；Anand et al. 已用 learned dynamics预测 stiffness sequence 的未来结果；Roveda et al. 已将 learned uncertainty、MPC、Lyapunov constraints 与在线 impedance action 结合；Pollayil et al. 也已在扰动响应约束下在线选择 stiffness/damping。

### 3.2 可保留的新 claim

R15 可声明的不是一般 covariance authority，而是其在 **T-RO feedforward-contact architecture 中的必要重构**：

1. nominal rollout 只由 \(\tau_{ff}\) 驱动，避免把 feedback gains 伪装成 nominal force action；
2. \(K_c(k_n,k_t)\) 只通过 probe-contact closed-loop Jacobian进入 \(A_{cl}\)；
3. 同一 \(A_{cl}\) 同时决定 force variance、slip、contact loss、feedback torque 与 tank envelope；
4. 当该 sensitivity 消失时，算法冻结 gains，而不是由 regularization 人为制造 variable impedance。

这是一条 architecture-specific non-redundancy relation，不是新的 stochastic-control 基本公式。

### 3.3 Gain-authority 命题应如何表述

令 \(\ell=[\log k_n,\log k_t]\)，预测统计量 \(y_j=y(\bar x_j,P_j)\)，硬约束 margin \(g_j=g(\bar x_j,P_j,E_{T,j})\)。R15 的 gain decision 非装饰性的充分条件为

\[
\exists j\le H,\ r\in\{n,t\}:\quad
\begin{bmatrix}
\partial y_j/\partial\ell_r\\
\partial g_j/\partial\ell_r
\end{bmatrix}\ne0,
\]

其中 \(\partial P_j/\partial\ell_r\) 由 R15 的 covariance-sensitivity recursion 产生。若该条件对全部节点和两个方向均不成立，则 variable impedance 对任务目标和 hard feasibility 无 control authority，最小方法必须冻结 \(\ell\)。该 proposition 应作为理论结果，而不是把 covariance propagation 本身列作 novelty。

---

## 4. 顶会顶刊论文的贡献包装规律

对直接 priors 的逆向分析显示，高水平论文通常不把工具名词逐项列成创新，而采用以下层级：

| 论文 | 真正的 contribution unit | 可借鉴的包装方式 | R15 对应写法 |
|---|---|---|---|
| T-RO 2023 | differentiable soft-contact dynamics 与 constrained TO 的新因果关系，再给 distributed solver 与 hardware evidence | mechanism → solver → validation | future contact → feedback mechanics；sensitivity QP/ADMM；matched scan evidence |
| Dyck RA-L 2022 | surface geometry 被重参数化为可实施、可解释的 impedance task coordinates | 以新的表示关系为主，不宣称 classical impedance 新 | 以 future-contact-conditioned closed-loop relation 为主 |
| Beber ICRA 2024 | tissue mechanics → physical bounds → online stiffness/tank decision | 目标领域的物理量如何改变优化问题 | future sliding mechanics → n/t gain authority and margins |
| Haninger RAS 2023 | uncertain interaction model 与 impedance 在同一 belief MPC 中的关系 | 统一 formulation + optimization capability + constraints | T-RO contact belief 与 feedback gains 的 architecture-specific closure |
| Fu T-RO 2025 | active compliant and passive shared control 的系统级关系 | 已有组件可以因新 interconnection 成为贡献，但 theorem scope 要准确 | n/t contact prediction、gain decision、energy authority 的统一 interconnection |
| Xue RAS 2025 | safety-priority modes、environment adaptation、MPC 与 passivity 的任务级闭合 | 不以 MPC/tank 为新，以新的 control objective and logic 为新 | 不以 QP/tank 为新，以 future-contact-conditioned decision semantics 为新 |

据此，R15 最强的叙事顺序应是：

1. **Problem formulation:** reactive normal compliance 无法区分 current-state-aliased future contacts；
2. **Mechanism:** future probe contact changes feedback dynamics and feasible impedance, not only nominal torque；
3. **Algorithm:** a two-variable contact-horizon sensitivity QP closes this relation inside the original T-RO decomposition；
4. **Assurance:** the same predicted envelope authorizes contact margins and modulation energy before publication；
5. **Evidence:** matched baselines demonstrate action-changing benefit and deadline feasibility。

---

## 5. 最强 Scientific Delta

### 5.1 一句话主张

> **R15 introduces future-contact-conditioned closed-loop impedance co-design for continuous robotic ultrasound: future probe-specific normal--tangential deformable contact is not merely predicted for feedforward force/motion planning, but is made the common cause of feedback-gain selection, contact-risk margins, and energy-authorized action publication.**

### 5.2 与 strongest priors 的最小差异

\[
\begin{array}{ll}
\text{T-RO / AuSoScan:} & \text{future contact}\rightarrow\tau_{ff},\quad K\text{ predefined},\\
\text{Beber / Fu:} & \text{current force or mapped tissue point}\rightarrow k_n,\\
\text{generic MPVIC:} & \text{generic/learned interaction rollout}\rightarrow K,D,\\
\textbf{R15:} & \textbf{future probe n/t contact}\rightarrow
\textbf{joint mean/deviation margins}\rightarrow(\tau_{ff},k_n,k_t).
\end{array}
\]

最后一行的 novelty 不来自箭头中的任一名词，而来自此前未在连续超声软接触领域建立的完整映射及其可证伪能力：在相同当前 force/error 下，仅因未来 curvature、indentation slope、sliding resistance 或 friction capacity 不同而提前选择不同 n/t impedance。

### 5.3 不能使用的夸张措辞

- 不使用 “the first model-predictive variable impedance controller”；
- 不使用 “the first online stiffness optimization for robotic ultrasound”；
- 不使用 “the first uncertainty-aware impedance MPC”；
- 不使用 “the first passive/tank-based medical VIC”；
- 不使用 “guarantees patient safety” 或 “global passivity of the autonomous scanner”；
- 在完成定向系统检索前，不使用绝对的 “the first”；优先使用 “to our knowledge, the first formulation that makes ...” 并限定目标任务与具体关系。

---

## 6. Reconstructed Scientific Contributions

最终只保留三条主贡献。三条的层级依次是 **target-domain capability → closed-loop mechanism → real-time constrained realization**；它们不是三个可拆卸模块，而是同一因果链的 formulation、mechanics 与 solver 三层。

### 6.1 中文论文式表述

1. **利用未来探头--组织接触状态，在线联合选择法向--切向阻抗。** 现有机器人超声方法已具有示范变阻抗、当前力/组织估计驱动的在线法向刚度 QP，以及预测软接触的 torque MPC，但尚未让未来 probe-specific deformable normal--tangential contact 联合决定 \(k_n,k_t\)。R15 建立这一目标领域关系，使相同当前测量、不同未来接触演化能够产生不同阻抗决策。

2. **把未来软接触预测从 nominal force/motion planning 扩展为 feedback-impedance decision state。** 一般 predictive/risk-sensitive impedance 已能根据未来交互与不确定性调节 gains；R15 的新增不是“预测阻抗”本身，而是对连续软组织滑动接触重新推导 mean--deviation dynamics：未来几何与接触参数既改变 nominal contact trajectory，也通过 \(A_{cl}(k_n,k_t)\) 改变 feedback deviation，二者共同决定 force、slip、contact-retention 与 actuator margins；zero-authority 时冻结 gains。

3. **推导面向连续扫描的 probe-contact-horizon sensitivity impedance QP。** 现有 ultrasound QP、online stiffness QP 与通用 multistep MPVIC 均不足以单独构成创新；R15 的算法差异是将整段 future deformable-contact 与 closed-loop deviation 对 \((k_n,k_t)\) 的敏感度压缩为两变量约束 QP，同时处理 future force、slip、contact retention、actuator、gain/damping 与 energy-admissibility margins，并由原非线性模型复验候选动作。

这里“在线联合选择法向--切向阻抗”的**最小充分参数化**是独立优化 \((k_n,k_t)\)，而 \((d_n,d_t)\) 由局部闭环极点/阻尼比关系导出；因此不应暗示四个 gains 均独立在线优化。第 2 条也不应写成所有未来量都“经过 \(A_{cl}\)”：nominal contact rollout 直接决定均值项，而 \(A_{cl}\) 专门赋予 feedback gains 对偏差与风险包络的独立作用。

### 6.2 Paper-ready English wording

以下英文版本逐条保持“closest prior → remaining gap → added relation → value”，且每条少于 60 个英文单词。

1. **Robotic-ultrasound methods learn impedance from demonstrations, adapt normal stiffness from current contact, or predict soft contact only for motion/torque. We formulate future-contact-conditioned normal–tangential impedance: predicted probe–tissue evolution jointly selects \(k_n,k_t\), allowing identical current measurements but different future geometry, deformation, or sliding conditions to produce different feedback mechanics.**

2. **Predictive impedance priors anticipate uncertain interaction, but do not close feedback-gain selection with probe-specific deformable sliding contact. We derive coupled nominal and deviation dynamics in which future contact shapes mean interaction while \(A_{cl}(k_n,k_t)\) shapes force, slip, retention, and actuator margins; an authority test freezes gains when this coupling cannot change outcomes.**

3. **Ultrasound stiffness QPs are reactive, whereas generic MPVIC lacks probe-specific sliding-contact constraints. We derive a two-variable contact-horizon sensitivity QP whose coefficients encode future deformable-contact and closed-loop-deviation effects on force, slip, retention, actuator, gain/damping, and energy margins, followed by exact nonlinear revalidation for real-time publication of a feasible impedance action.**

Energy tank 不列为第 4 条贡献。Beber 2024 和 Fu 2025 已分别覆盖 ultrasound/medical VIC 中的 tank-constrained modulation 与 global moving-reference energy accounting。R15 的 predicted-envelope debit 作为第 3 条的 safety/assurance mechanism；只有未来形成超出现有工作的 robustness/passivity theorem，才可升级为独立理论贡献。

---

## 7. 顶会顶刊强度判定与必要证据

### 7.1 当前强度

| 维度 | 判定 | 理由 |
|---|---|---|
| target-domain novelty | **strong conditional** | 未发现 prior 让 future probe-specific n/t deformable-contact trajectory共同决定 torque 与 n/t impedance |
| problem formulation | **strong** | current-state aliasing 给出清晰、可证伪且超出 reactive normal VIC 的问题 |
| algorithmic novelty | **moderate-to-strong** | sensitivity QP inside T-RO ADMM 是特定闭合，不是通用 SQP/QP 新算法 |
| theoretical novelty | **moderate** | 已有显式 dynamics/authority/energy equations，但尚缺正式 theorem/proof 与 recursive-feasibility result |
| real-time/deployment evidence | **open** | reduced covariance fidelity、exact acceptance 与 99.9% latency 尚待目标硬件验证 |

结论不是“创新不足”，而是：**R15 已具有顶级机器人 venue 所需的独立问题 formulation 和方法关系；其稿件应定位为 contact-control methodology，而不是 general MPC/passivity theory。** 若三项关键证据通过，scientific contribution 足以对标 T-RO/TMECH/RA-L/ICRA 类型论文；若要把“理论创新”本身作为顶刊主卖点，还必须把下述条件正式化。

### 7.2 三个应形成 proposition/theorem 的现有关系

1. **Gain-authority proposition**：证明上述 sensitivity 非零条件何时使 \((k_n,k_t)\) 对目标或 hard feasibility 有独立一阶作用，并给出 zero-uncertainty/fixed-gain limiting case。
2. **Robust modulation-energy proposition**：证明 containing deviation/contact envelope 上的 supremum debit 足以上界 gain-change 与 moving-reference 正功，因此被发布的 modulation channel 不超支；明确不覆盖主动 \(\tau_{ff}\)。
3. **Deadline-total publication theorem**：若 verified incumbent 与 preverified WITHDRAW 对 admissible envelope 始终属于 \(\mathcal A_{safe}\)，则任何 timeout/infeasibility 下发布策略均有定义；该结论不等于 recursive scan feasibility 或 patient safety。

这些命题都已存在于 R15 的公式关系中，不要求发散设计新模块；需要的是完整 assumptions、proof 和 failure boundary。

### 7.3 Novelty killer experiments

必须在同一真实扫描 transition 上比较：

1. fixed impedance；
2. Beber-style current tissue-map/normal-stiffness QP；
3. Fu-style \(N=1\) current-force-error normal-stiffness QP；
4. generic MPVIC without probe n/t contact state；
5. R15 full method。

核心统计不是单一 force RMSE，而是：相同当前 observation、不同 future contact trajectory 下的 gain-action separation；force/path joint error；slip/contact-loss events and margins；constraint/tank interventions；full/reduced action agreement；99.9% solve latency and forced-timeout outcome。

淘汰规则保持不变：若 R15 相对 Beber/Fu 的 horizon 与 \(k_t\) 不改变 action order、joint outcome 或 hard-margin behavior，则应退化为更简单的 current-step normal QP。

---

## 8. 审计后是否修改 Final Method

**不修改 R15 的 state、dynamics、action、objective、constraints 或 solver。** 新增 prior 改变了 novelty boundary 和 baseline hierarchy，但没有提供更简单机制来获得“future probe n/t contact → online impedance”这一目标能力：

- Beber/Fu 更成熟、更简单，但只闭合 current normal compliance；
- Haninger/Jin/Xue 已有 prediction/uncertainty/constraints/passivity，但缺 probe sliding-contact decision relation；
- T-RO/AuSoScan 有 future probe contact，却不在线选择 impedance。

因此保留当前 Final Method，但将原四条 contributions 压缩为三条，并把 Beber ICRA 2024 与 Xue RAS 2025 纳入 strongest priors 和 killer baselines。R15 的论文主标题、摘要和 introduction 都应围绕 **future-contact-conditioned closed-loop impedance co-design**，避免以 CMI-MPC 缩写或组件列表掩盖核心关系。

---

## 9. Primary-source basis

- L. Wijayarathne et al., *Real-Time Deformable-Contact-Aware Model Predictive Control for Force-Modulated Manipulation*, IEEE T-RO, 2023. DOI: [10.1109/TRO.2023.3286070](https://doi.org/10.1109/TRO.2023.3286070).
- M. Dyck et al., *Impedance Control on Arbitrary Surfaces for Ultrasound Scanning Using Discrete Differential Geometry*, IEEE RA-L, 2022. DOI: [10.1109/LRA.2022.3184800](https://doi.org/10.1109/LRA.2022.3184800).
- A. Duan et al., *Ultrasound-Guided Assistive Robots for Scoliosis Assessment With Optimization-Based Control and Variable Impedance*, IEEE RA-L, 2022. DOI: [10.1109/LRA.2022.3186504](https://doi.org/10.1109/LRA.2022.3186504).
- L. Beber et al., *A Passive Variable Impedance Control Strategy with Viscoelastic Parameters Estimation of Soft Tissues for Safe Ultrasonography*, ICRA, 2024. DOI: [10.1109/ICRA57147.2024.10610167](https://doi.org/10.1109/ICRA57147.2024.10610167).
- J. Fu et al., *Optimization-Based Variable Impedance Control of Robotic Manipulator for Medical Contact Tasks*, IEEE TIM, 2024. DOI: [10.1109/TIM.2024.3372209](https://doi.org/10.1109/TIM.2024.3372209).
- J. Fu et al., *Human-Inspired Active Compliant and Passive Shared Control Framework for Robotic Contact-Rich Tasks in Medical Applications*, IEEE T-RO, 2025. DOI: [10.1109/TRO.2025.3548493](https://doi.org/10.1109/TRO.2025.3548493).
- A. Duan et al., *AuSoScan: Automatic Scoliosis Assessment by Ultrasound Scanning With Soft Contact Control*, IEEE/ASME TMECH, 2025. DOI: [10.1109/TMECH.2025.3583041](https://doi.org/10.1109/TMECH.2025.3583041).
- B. Hammoud et al., *Impedance Optimization for Uncertain Contact Interactions Through Risk Sensitive Optimal Control*, IEEE RA-L, 2021. DOI: [10.1109/LRA.2021.3068951](https://doi.org/10.1109/LRA.2021.3068951).
- K. Haninger et al., *Model Predictive Impedance Control With Gaussian Processes for Human and Environment Interaction*, Robotics and Autonomous Systems, 2023. DOI: [10.1016/j.robot.2023.104431](https://doi.org/10.1016/j.robot.2023.104431).
- M. J. Pollayil et al., *Choosing Stiffness and Damping for Optimal Impedance Planning*, IEEE T-RO, 2023. DOI: [10.1109/TRO.2022.3216078](https://doi.org/10.1109/TRO.2022.3216078).
- L. Roveda et al., *Q-Learning-Based Model Predictive Variable Impedance Control for Physical Human-Robot Collaboration*, Artificial Intelligence, 2022. DOI: [10.1016/j.artint.2022.103771](https://doi.org/10.1016/j.artint.2022.103771).
- H. Kong et al., *Neural-Network-Based Optimal Impedance Control for Robots in Physical Interaction With Soft Environments*, IEEE TSMC: Systems, 2025. DOI: [10.1109/TSMC.2025.3579017](https://doi.org/10.1109/TSMC.2025.3579017).
- Z. Jin et al., *Model Predictive Variable Impedance Control of Manipulators for Adaptive Precision-Compliance Tradeoff*, IEEE/ASME TMECH, 2023. DOI: [10.1109/TMECH.2022.3204350](https://doi.org/10.1109/TMECH.2022.3204350).
- Z. Jin et al., *Constrained Variable Impedance Control Using Quadratic Programming*, ICRA, 2022. DOI: [10.1109/ICRA46639.2022.9812210](https://doi.org/10.1109/ICRA46639.2022.9812210).
- J. M. S. Ducaju et al., *Model-Based Predictive Impedance Variation for Obstacle Avoidance in Safe Human–Robot Collaboration*, IEEE TASE, 2025. DOI: [10.1109/TASE.2024.3508718](https://doi.org/10.1109/TASE.2024.3508718).
- J. Xue et al., *Model Predictive Variable Impedance Control Towards Safe Robotic Interaction in Unknown Disturbance-Rich Environments*, Robotics and Autonomous Systems, 2025. DOI: [10.1016/j.robot.2025.104961](https://doi.org/10.1016/j.robot.2025.104961).

审计优先使用仓库中的原论文、decision-grade Evidence Cards 与 Active Field Map；只对 Beber ICRA 2024、Duan RA-L 2022、Hammoud RA-L 2021 及“future probe n/t contact-conditioned impedance”缺口进行了定向补核。上述 priors 已足以压实 target-domain 与 anticipatory-impedance novelty 边界，故停止扩展，未重新开启领域综述。
