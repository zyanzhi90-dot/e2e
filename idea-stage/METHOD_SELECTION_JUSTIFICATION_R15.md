# R15 Method Selection Justification

## Decision

选择 **Route A / contact-conditioned mean–deviation impedance MPC（CMI-MPC）** 作为唯一 Final Method；淘汰 Route B 作为主线；不启用 Route C 的 learning residual。

算法无关的核心原则是：对每个 nominal action 与最小 normal–tangential impedance，用未来软接触状态同时预测 nominal interaction 和 feedback-conditioned deviation outcome，并只发布在 task、contact/wrench、actuator、gain-energy 与 deadline 上由同一关系授权的动作。T-RO 的 DDP/IK/Projection ADMM 是该原则的具体 realization，而不是 novelty 本身。

## 为什么不是 R14 的机制组合

R15 只修改 T-RO 的一个失败接口：原方法预测未来 deformable contact，却使用 predefined impedance。接触几何替换、mean--deviation rollout、最小增益参数化、约束投影和 feedback energy authorization 都沿着同一个 `contact prediction → closed-loop outcome → applied action` 关系传播，没有第二个并行控制器。

## Route A 的决定性理由

1. **主线完整保留**：contact dynamics → robot dynamics → DDP + IK + Projection → ADMM-MPC 不变。
2. **控制权可证明**：`τ_ff` 改变 nominal mean；`K_n,K_t` 通过 `A_cl` 改变 covariance 和 robust contact margins。即使 nominal error 为零，阻抗仍有独立一阶作用。
3. **参数化最小**：只优化 `K_n,K_t`；`D_n,D_t` 由局部有效质量、环境斜率和阻尼比导出。四增益只有在 matched comparison 中改变 action/outcome 才升级。
4. **目标约束自然兼容**：T-RO 的 Projection block 可承载 wrench、slip、contact retention、actuator、gain-rate、tank 和 robust tightening。
5. **Source Mechanism 局部且必要**：AuSoScan 只修探头几何；NNBO 只提供 projected EW-RLS；MPVIC 只提供阻抗作为预测决策及能量授权思想。

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
4. energy tank 必须保守覆盖 gain-change injection；否则只能冻结/降低 gains，不能发布时变 impedance。

## 最小原则测试

- matched replay：fixed impedance、naive nominal A2-II、CMI-MPC；检查 joint force/path outcome、slip/contact-loss、gain action order。
- reduction fidelity：full vs contact-reduced covariance。
- hard authority：held-out parameter envelope、abrupt change、timeout/infeasibility。
- WCET：固定 horizon/iterations 的 99.9% latency 和 verified publication。

只有 Route A 同时满足 problem fit、method necessity、scientific strength 和原主线可实现性，因此本轮不保留并列候选。
