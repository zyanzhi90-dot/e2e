# Human Selection 前论文级 Method synthesis（R8）

> 状态：非正式决策材料；不是 `FINAL_METHOD_PACKET`，不表示实验、覆盖率、稳定性或实时性已经成立。两条 active Candidate 共享同一重新锚定的科学核心，只在第二个 RMC 的 totality 机制上不同。

## 1. 科学主线

最小且自然的路线是：

**支持状态可判定的 decision-sufficient 软接触算子 → 校准外但物理包络内的主动在线适应 → 后验与有效裕度共同约束的法向–切向阻抗优化 → 总能返回实际可执行动作的闭环关系。**

存活路线为：

- `P-ADAPTIVE-CONTACT-FEASIBLE-INCUMBENT@1`：verified incumbent persistence；
- `P-ADAPTIVE-CONTACT-VIABILITY-RELATION@1`：capacity-conditioned viability relation 与同集合 backup。

原 R5 A/B 没有被删除，而是分别被吸收到这两个 integrated Candidate；原始正式记录仍由 archive 和 `METHOD_SYNTHESIS_HUMAN_SELECTION_DRAFTS.md` 保留。

## 2. 相对 NNBO 必须同时改变什么

NNBO 先识别 trial-fixed 标量 Hunt–Crossley 关系，再由 critic 逼近修正 HJB，最后以 Newton 反演形成力对应的位置参考。其边界内合理，但无法表示当前问题中动作相关的切向、有限接触面、力矩、滑移/失接触与历史容量，也没有把全部硬约束和 deadline 写入同一个 applied-action relation。

| 因果位置 | NNBO | R8 改变 | 为什么优化必须随之改变 |
|---|---|---|---|
| 接触表示 | 固定试次的标量接触参数 | 历史与候选阻抗共同条件化的接触结果后验 | 同一瞬时压入量可对应不同摩擦容量、力矩、松弛和失接触风险 |
| 校准外处理 | 突变/时变参数未解决 | `SUPPORTED / ADAPTING-IN-ENVELOPE / OUTSIDE-ENVELOPE` | 固定模型的动作排序不再可信，需边运行边更新可行集和成本 |
| 阻抗决策 | 一维、未含当前问题全部硬约束 | 对 \((k_n,d_n,k_t,d_t)\) 的短时域后验约束优化 | 新表示输出动作条件不确定性和耦合裕度，不能由原标量 HJB 原样消费 |
| 实际输出 | 条件性内环实现 | incumbent 或 viability 保证 deadline 输出 | 研究问题约束实际施加阻抗，不是理想求解器的名义解 |

## 3. 共同核心 Method

### 3.1 决策充分接触算子

阻抗动作为

\[
a_k=[k_{n,k},d_{n,k},k_{t,k},d_{t,k}]^\top .
\]

历史 \(h_k=\{y_{0:k},a_{0:k-1}\}\) 包含位姿/压入量及速度、六维力/力矩、扫描切向与法向、接触/滑移指标、既往阻抗和交互能量账本。编码器产生

\[
z_k=E_\psi(h_k),
\]

但 \(z_k\) 不表示材料类别或完整本构参数，而被定义为：对声明动作类而言，足以保持未来可行集与阻抗排序的最小预测状态。对候选序列 \(a_{k:k+H-1}\)，算子输出

\[
p_\Theta(o_{k:k+H}\mid z_k,a_{k:k+H-1}),
\]

其中 \(o\) 联合包含法向力误差、切向轨迹误差、接触保持、滑移/摩擦容量、六维 wrench、交互能量增量和执行器负载。有限接触面、局部几何和黏弹历史只在改变这些动作条件结果时才必须被保留。

### 3.2 离线目标不是拟合最全数字孪生

\[
\mathcal L=\mathcal L_{\mathrm{multi-step}}
+\lambda_F d(\widehat{\mathcal F}_\Theta,\mathcal F^{\mathrm{teacher}})
+\lambda_R[J(\hat a_\Theta)-J(a^{\mathrm{teacher}})]_+ .
\]

第一项保证多步结果可预测；第二项惩罚预测可行集与高信息 teacher 的差异；第三项直接惩罚表示错误导致的 constrained impedance regret。嵌套状态消融删除不改变 held-out 可行集或动作排序的坐标。若校准良好的 memoryless/scalar 表示在相同动作、裕度和算力下决策等价，则新表示的科学增量被反证。

### 3.3 三种支持状态与权威裕度

每个 pre-outcome context–action pair 被分为：

1. `SUPPORTED`：属于预声明 calibration population，支持与残差审计仍适用；
2. `ADAPTING-IN-ENVELOPE`：初始 calibration 已不适用，但仍处于声明的材料、几何、速度、载荷、wrench、能量和执行器物理包络内；
3. `OUTSIDE-ENVELOPE`：扫描适应不再可辩护，但状态仍在另行声明的 withdrawal envelope；控制器内部返回预验证 withdrawal action。超出 withdrawal envelope 的事故域不属于声明系统域。

在 `SUPPORTED` 状态，优化器使用独立校准残差形成的可行集 \(\mathcal F^{\mathrm{cal}}\)。在 `ADAPTING-IN-ENVELOPE` 状态，先独立定义

\[
\mathcal F_k^{\mathrm{env}}
=\{a:g^{\mathrm{env}}_j(z_k,a,w)\ge0,\quad
\forall j,\forall w\in\mathcal W_k^{\mathrm{env}}\},
\]

再令

\[
\mathcal F_k^{\mathrm{adapt}}
=\mathcal F_k^{\mathrm{env}}\cap\mathcal F_k^{\mathrm{veto}}
\subseteq\mathcal F_k^{\mathrm{env}}.
\]

\(\mathcal F^{\mathrm{veto}}\) 只有在非空且包含声明 fallback 时才由 posterior lower margin 产生；否则取整个动作集。posterior 只能增加 veto 或影响 cost/information value，绝不能缩小 \(\mathcal W^{\mathrm{env}}\)、放松 \(g_j^{\mathrm{env}}\) 或让动作离开 \(\mathcal F^{\mathrm{env}}\)。covariance inflation 只表示 epistemic uncertainty 扩大，绝不等于 coverage guarantee。

因此校准外变化不是“发现后拒绝”：只要 \(\mathcal F^{\mathrm{adapt}}\) 非空，系统仍选择性能动作、积累观测，并执行有信息量但 admissible 的阻抗变化。

### 3.4 在线适应与信息动作

冻结高维表示主干，只在线更新低维 Bayesian/RLS readout。令 \(\phi_k=\phi(z_k,a_k)\)：

\[
\hat o_k=W_k\phi_k,\quad P_k^-=P_{k-1}/\lambda_k,
\]

\[
K_k=P_k^-\phi_k(\Sigma_\epsilon+\phi_k^\top P_k^-\phi_k)^{-1},
\]

\[
W_k=W_{k-1}+(o_k-W_{k-1}\phi_k)K_k^\top,\quad
P_k=(I-K_k\phi_k^\top)P_k^- .
\]

持续 normalized innovation 触发更强 forgetting 和 covariance inflation。若 uncertainty 可能改变 active constraint 或动作排序，则加入有界信息价值

\[
\mathcal I_k(a)=\operatorname{tr}\!\left(S_k[P_k-P_{k+1}(a)]S_k^\top\right),
\]

其中 \(S_k\) 只选取当前活跃裕度或动作排序有关的参数方向。信息动作必须先属于 \(\mathcal F^{\mathrm{adapt}}\)；若不存在 admissible informative action，方法保持最保守可行动作，并明确承认当前不能恢复最优性。

### 3.5 OUTSIDE-ENVELOPE 也是 total relation 的动作分支

控制输出扩展为 \(u_k=(m_k,a_k,r_k)\)，其中 \(m_k\in\{\mathrm{SCAN},\mathrm{WITHDRAW}\}\)。一旦 pre-outcome monitor 判定离开 adaptation envelope，性能优化和信息获取在同一 update 被旁路，常数时间查表返回

\[
u_k^{\mathrm{wd}}=(\mathrm{WITHDRAW},a^{\mathrm{wd}},r_k^{\mathrm{wd}}).
\]

其切向参考速度置零，阻抗在 gain-rate 限制内转为预验证的 dissipative withdrawal setting，法向参考沿最后可信法向卸载，直到实测接触力低于 release threshold。\(u^{\mathrm{wd}}\) 只在单独声明的 withdrawal envelope 内预验证 robot-side wrench、energy、actuator 和 gain-rate feasibility，并必须在同一 deadline 内输出。它不声称未知接触下继续优化或任意漂移安全，只使声明域内三态 map 对 applied action 闭合。

### 3.6 决策恢复与校准恢复是两个命题

online readout 可先恢复预测和动作排序，但不能自动恢复 calibration applicability。由 `ADAPTING-IN-ENVELOPE` 回到 `SUPPORTED` 必须满足：

- 建立不与当前 readout update 重用的 delayed residual buffer；
- 达到预声明 effective sample size；
- 通过局部平稳性/依赖条件审计；
- 一侧 empirical calibration audit 达到预声明目标。

审计通过前始终以 \(M^{\mathrm{env}}\) 为权威。若决策改善但审计不通过，只能主张 envelope-conservative adaptation，不能主张已恢复覆盖。

### 3.7 后验约束的法向–切向阻抗选择

\[
\min_{a_{k:k+H-1}}\mathbb E\!\sum_i
\left(w_f\lVert e^f_{k+i}\rVert^2+
w_t\lVert e^t_{k+i}\rVert^2+
w_s\rho^{\mathrm{slip/loss}}_{k+i}+
w_\Delta\lVert\Delta a_{k+i}\rVert^2\right)
-\beta_k\mathcal I_k(a_k),
\]

`SUPPORTED` 时使用 \(\mathcal F^{\mathrm{cal}}\)；`ADAPTING` 时必须先无条件满足 \(\mathcal F^{\mathrm{env}}\)，posterior 只可额外限制或改变目标：

\[
g_j(o_{k+i},a_{k+i})\ge0,\quad
j\in\{\text{wrench, contact, friction, energy, actuator, gain/rate}\},
\]

以及 Candidate 特有的 terminal/recursive-feasibility 条件。posterior scenario、upper-confidence bound 或 CVaR 只在直接对应 active margin 或 loss/contact event 时进入；robust optimization 是求解语义，不是独立 Principle。

## 4. 两条闭环 Candidate

### 4.1 Candidate A：Adaptive Contact + Feasible Incumbent

RTI-SQP 以上一时刻 verified sequence 的 shift 为初值。新 iterate 只有在当前 \(\mathcal F^{\mathrm{cal}}\) 或 \(\mathcal F^{\mathrm{adapt}}\) 下完成验证后才原子替换 incumbent：

\[
\bar a_k^+=
\begin{cases}
a_k^{\mathrm{cand}},&a_k^{\mathrm{cand}}\in\mathcal F_k^{\mathrm{act}},\\
\bar a_k,&\text{otherwise or deadline}.
\end{cases}
\]

BOX-FDDP 只能作为 warm-start/feasibility-restoration precedent，不能被宣称为 interruption totality 证明。`OUTSIDE-ENVELOPE` 优先于 incumbent 逻辑并直接返回 \(u^{\mathrm{wd}}\)。核心风险是 scan-mode support-state 切换或容量快速变化时，shifted incumbent 是否持续可行或能在 deadline 前保守修复。

### 4.2 Candidate B：Adaptive Contact + Viability Relation

把能量账本和 gain-rate state 并入增广状态 \(\xi_k\)，在当前权威裕度下构造 robust inner viability set：

\[
\mathcal V_k=\{\xi:\exists a\in\mathcal A_k^{\mathrm{act}},
f(\xi,a,w)\in\mathcal V_{k+1},\ \forall w\in\mathcal W_k\}.
\]

性能优化只在 \(\mathcal V_k\) 内进行；中断或 nominal infeasibility 时使用同集合定义的 certified backup。`OUTSIDE-ENVELOPE` 优先于 viability solve 并直接返回 \(u^{\mathrm{wd}}\)。它不依赖前一条优化轨迹持续可行，但必须证明 posterior 和 margin authority 改变时 \(\mathcal V_k\) 仍非空且不过度保守。

A 的 totality 来自“过去已验证动作的持续存在”，B 来自“当前状态存在保持未来可行的同集合动作”；当前证据不能合法消掉任一条。

## 5. 必需但尚未成立的分析

- **Decision equivalence**：operator-equivalent histories 在声明容差内诱导相同 robust feasible set 与动作排序。
- **Local adaptation**：bounded drift、低维 readout 可辨识且存在 persistent admissible excitation 时，readout error 有界；不自动推出 calibrated re-entry。
- **Margin-state validity**：support state 在 outcome 前决定；每个 adapting action 必属 \(\mathcal F^{\mathrm{env}}\)，posterior 不可放松该约束。
- **Withdrawal totality**：withdrawal envelope 中每个 OUTSIDE-ENVELOPE state 的 \(u^{\mathrm{wd}}\) 必须 robot-side feasible、按时输出并单调卸载，否则三态 total relation 被反证。
- **A totality**：初始 incumbent 可行、support switch 可保守更新且验证满足 deadline 时，中断只降低性能。
- **B recursive feasibility**：robust inner set 非空且 backup successor membership 成立时，nominal 中断不破坏递归可行性。

## 6. 机制来源的诚实边界

| 来源 | 分类 | 实际迁移/借鉴 | Target 新增、不可倒算给 Source 的部分 |
|---|---|---|---|
| VES + SPO | 正式 Source migration | 以 downstream value/decision regret 定义表示损失 | 接触可行集、阻抗动作与在线适应 |
| PSR | Evidence-informed derivation | action–observation predictions 的 state 形式 | PSR 学习保证、接触安全与覆盖保证 |
| MPVIC | Evidence-informed derivation | probabilistic model、离线 uncertainty seeking、部署 stiffness MPC | 连续接触在线更新、同裕度 safe information action、normal–tangential hard constraints |
| finite-area QLV | Evidence-informed physical basis | 历史同时改变法向力、切向容量与 moment limit | 在线拟合完整本构模型 |
| Predict-then-Calibrate / BOX-FDDP | bounded implementation precedent | split calibration / warm-start feasibility restoration | post-shift coverage、非线性 contact guarantee、incumbent totality |

真正的 Source migration 是“决策相关性如何塑造表示”；PSR/MPVIC 不再被写成已通过 formal alignment 的 Source Mechanism。

## 7. Contributions

共同贡献：

1. **Decision-sufficient soft-contact operator**：以 constrained feasible-set 与 decision regret 训练和裁剪表示，将 NNBO 固定标量环境参数改为历史/动作条件的 joint contact posterior。
2. **Support-aware active calibration-out adaptation**：以三状态 margin authority 在校准失效但仍在物理包络时继续运行和取信息，并用 delayed audit 分离 decision recovery 与 calibrated re-entry。
3. **Posterior-aware constrained normal–tangential impedance selection**：由同一算子直接生成 \((k_n,d_n,k_t,d_t)\)，使性能与信息动作共同满足 wrench、contact/friction、energy、actuator 和 gain/rate constraints。
4. **Closed three-state applied-action relation**：将 adaptation-envelope loss 映射成同 deadline 的预验证 dissipative withdrawal tuple，而不是委托给未定义的外部 action。

Candidate A 增加 atomic verified-incumbent replacement，使 deadline 输出来自同一裕度关系；Candidate B 增加 capacity-conditioned viability relation，使 nominal 与 backup 共享 invariant admissible set。

## 8. 关键技术风险

- \(M^{\mathrm{env}}\) 可能过于保守，导致无 informative action 或性能空间；
- 闭环依赖样本可能无法支持 defensible delayed local calibration audit；
- frozen features 可能不覆盖真正新的变形/摩擦机制；
- 四维阻抗可能不足，而完整矩阵会损害可辨识性与实时性；
- A 可能在快速容量变化时失去 incumbent；B 可能因 viability set 为空或过保守而失败；
- withdrawal envelope 若定义不足，或 \(u^{\mathrm{wd}}\) 无法按时、单调卸载，三态 totality 失败；
- posterior propagation、margin 检查与 totality certification 可能超过 update deadline。

这些风险留给后续正式 Principle Test Design；本轮没有把任何验证写成已经成立。Human Selection 需要决定的是：在共同自适应接触核心之后，押注“保留一个已验证解”，还是“维护一个始终有 backup 的可生存关系”。
