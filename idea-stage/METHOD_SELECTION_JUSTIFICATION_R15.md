# R15 Method Selection Justification

## Decision

选择 **Route A / contact-conditioned mean–deviation impedance MPC（CMI-MPC）** 作为唯一 Final Method；淘汰 Route B 作为主线；不启用 Route C 的 learning residual。

算法无关的核心原则是：对每个 nominal action 与最小 normal–tangential impedance，用未来软接触状态同时预测 nominal interaction 和 feedback-conditioned deviation outcome，并只发布在 task、contact/wrench、actuator、impedance/reference-modulation energy 与 deadline 上由同一关系授权的动作。T-RO 的 DDP/IK/Projection ADMM 是该原则的具体 realization，而不是 novelty 本身。

## 为什么不是 R14 的机制组合

R15 只修改 T-RO 的一个失败接口：原方法预测未来 deformable contact，却使用 predefined impedance。接触几何替换、mean--deviation rollout、最小增益参数化、约束投影和 feedback energy authorization 都沿着同一个 `contact prediction → closed-loop outcome → applied action` 关系传播，没有第二个并行控制器。

## Route A 的决定性理由

1. **主线完整保留**：contact dynamics → robot dynamics → DDP + IK + Projection → ADMM-MPC 不变。
2. **控制权可证明**：`τ_ff` 改变 nominal mean；`K_n,K_t` 通过 `A_cl` 改变 covariance 和 robust contact margins。即使 nominal error 为零，阻抗仍有独立一阶作用。
3. **参数化最小**：只优化 `K_n,K_t`；`D_n,D_t` 由局部有效质量、环境斜率和阻尼比导出。四增益只有在 matched comparison 中改变 action/outcome 才升级。
4. **求解形式更明确**：DDP 只更新 nominal torque；每节点两个 log-stiffness 由 future covariance/contact-margin sensitivities 构造的小型 convex QP 更新，candidate 再经 exact nonlinear Projection 验证。
5. **目标约束自然兼容**：T-RO 的 Projection block 可承载 wrench、slip、contact retention、actuator、gain-rate、tank 和 robust tightening。
6. **Source Mechanism 局部且必要**：AuSoScan 只修探头几何；NNBO 只提供 projected EW-RLS；Fu TIM 2024 只提供医疗 stiffness-QP realization；Fu T-RO 2025 只提供 moving-reference energy accounting；MPVIC 只提供预测增益 authority。Beber ICRA 2024 不新增模块，而是证明 ultrasound 中 tissue model + stiffness QP + physical/tank constraints 已是 prior，因而收紧 novelty boundary。

## 直接医疗/超声 VIC priors 是否应替代 R15

Beber et al., ICRA 2024 已把 HC 黏弹组织参数图、在线 Cartesian stiffness QP、force/maximum-penetration bounds 和 tank energy/power constraints 用于 ultrasound dummy 扫描，并测试 contact loss。因此“tissue-aware ultrasound VIC”“QP + physical constraints + tank”均不能算 R15 novelty。其决策来自当前点的离线组织图与当前 impedance wrench，重点仍是法向 force；没有 future sliding-contact rollout、`k_t` decision 或 horizon slip/contact-retention relation，故不能获得本任务目标能力。

不应整体替代，但必须修改 R15。Fu TIM 2024 已在 KUKA、六维力传感器和线阵超声探头上证明：以当前 force error 为目标的 `N=1` bounded QP 可以实时优化法向刚度，并显著改善软/硬转换扫描。因此“online stiffness optimization + QP + ultrasound validation”是 closest prior，不是 R15 的贡献。

该机制仍固定切向高刚度，没有 future deformable-contact rollout、slip/contact-loss prediction、uncertainty tightening 或 `k_t` decision。它只能在材料/力误差出现后修正 `k_n`，不能处理当前测量相同而未来几何或 friction capacity 不同的扫描状态。若直接采用它，核心能力“利用未来软接触状态选择法向--切向阻抗”消失。

Fu T-RO 2025 的关键增量不是 shared control 本身，而是证明 moving reference 与 stiffness variation 的主动功率为

\[
P_{mod}=\tfrac12e^T\dot Ke+\dot p_{ref}^TKe.
\]

这暴露了旧 R15 只对 \(\tfrac12e^T\Delta Ke\) 记账的不完整性。最终 R15 因此采用 Fu-style 小型 QP 作为 impedance block，并以 predicted deviation envelope 上的最坏 `P_mod` debit 修正 tank；bilateral teleoperation、haptic feedback 与 human-control loop 不迁移。

Beber/Fu 型控制器共同成为 killer baselines：若 current tissue-map QP 或 `N=1` normal QP 在 matched ultrasound transitions 上与 R15 的 joint force--path、slip/contact-loss 和 safety-margin outcome 等效，则 horizon covariance 与 `k_t` 没有必要，Final Method 应简化为更简单的 target-domain 方案。

## 为什么淘汰 A2-I

当 nominal tracking/contact error 为零时，gain-only action 满足 `∂τ/∂θ_I=0`，从而无 nominal control authority。若把 reference/feedforward torque 隐藏进 impedance policy 才恢复作用，就破坏了 T-RO torque 主干和 impedance 语义。因此它不成立为最终 action interface。

## 为什么不是朴素 A2-II

只在 nominal rollout 中增加 gains 同样退化。R15 的必要改动是显式预测 closed-loop deviation/covariance，使 `∂P_{k+1}/∂k_i ≠ 0`。这不是额外模块，而是 feedback gain 被优化所必需的结果变量。

## 为什么淘汰 Route B

用 probe normal–tangential contact 替换 HC 后：

- 若 HJB action 仍是 wrench，`w*=k e+d e_dot` 对 gains 非单值，并在零误差时奇异；
- 若 HJB action 改为四个独立 gains，固定 error 下虽可控制仿射，但 gain-input columns 在零 error 时消失，且 positivity/rate/contact/energy constraints 把 closed form 变成 state-dependent constrained minimizer；若采用最小二刚度并由局部极点导出 damping，dynamics 对 gains 一般非仿射，single-critic closed form 同样消失；
- 再加入 wrench/slip/actuator/rate/energy/deadline constraints 后，需要 constrained online solver。继续加入 actor、projection 或 MPC 就不再保留 NNBO 主线。

所以 Route B 可作 single-axis baseline/estimator source，但不是该任务最自然的 Final Method。

## 为什么不加入 learning residual

当前没有证据证明 cylinder-probe physical model 在相同信息、相同约束下留下持续且 action-changing 的 residual。只有残差改变最优 gains、约束活跃集或 SCAN/WITHDRAW decision，且简单物理包络不能包含时，learning 才被授权进入。该条件未满足。

## Fatal assumptions

1. contact-reduced covariance 必须保留 full formulation 的 gain action order 和 hard-constraint decisions；否则实时简化无效。
2. target hardware 必须在 deadline 内产生并验证 incumbent，或可靠切换到 preverified WITHDRAW；否则不能声称实时扫描能力。
3. local probe-contact slopes 和 robot-side measurements 必须足以形成 containing prediction；否则只能 WITHDRAW，不能用 estimator confidence 放宽 hard margins。
4. energy tank 必须在 containing deviation envelope 上保守覆盖 gain-change 与 moving-reference elastic work；否则只能冻结/降低 gains，不能发布时变 impedance。
5. sequential QP 的 candidate 必须回代 exact nonlinear contact/covariance/tank equations；否则局部线性可行不构成 applied-action authority。

## 最小原则测试

- matched replay：fixed impedance、Fu 式 `N=1` reactive normal QP、naive nominal A2-II、CMI-MPC；检查 joint force/path outcome、slip/contact-loss、gain action order。
- reduction fidelity：full vs contact-reduced covariance。
- hard authority：held-out parameter envelope、abrupt change、timeout/infeasibility。
- WCET：固定 horizon/iterations 的 99.9% latency 和 verified publication。

只有 Route A 同时满足 problem fit、method necessity、scientific strength 和原主线可实现性，因此本轮不保留并列候选。
