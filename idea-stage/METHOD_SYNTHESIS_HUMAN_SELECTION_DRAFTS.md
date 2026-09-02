# Human Selection 前的双路线论文级 Method Synthesis

Run: `impedance-control-landscape-e2e`

状态边界：本文是假设每个 Candidate 分别被选择后所形成的非正式论文级方法草案，只用于 `principle_human_selection` 决策。它不是 `FINAL_METHOD_PACKET`，不构成 Principle Test Plan，也不声称下述覆盖率、递归可行性、前向不变性、实时性或性能已经得到证明或实验验证。ARIS machine authority 仍以当前已评审的 R5 Candidate packet 为准。

比较对象：

- `P-CAPACITY-CONDITIONED-FEASIBLE-INCUMBENT@3`
- `P-CAPACITY-CONDITIONED-VIABILITY-RELATION@3`

本文不重新进行 Principle search。现有 Evidence 和 Source Mechanism 已足以确定两条方法的主要关系；以下关于网络结构、在线求解器、校准残差、动作离散化和 certificate 形式的具体选择，属于从 Target constraints 与 Candidate 出发所作的 method synthesis 选择，而不是被既有文献唯一规定的事实。

## 1. 两条路线共同继承的研究对象

### 1.1 接触坐标与被在线选择的阻抗

在连续扫描轨迹的第 $k$ 个阻抗更新时刻，以在线估计的表面法向 $n_k\in\mathbb R^3$ 和切平面基 $T_k\in\mathbb R^{3\times2}$ 建立接触坐标系 $R_{c,k}=[T_k,n_k]$。令末端相对参考扫描轨迹的位置、速度误差为

\[
e_k^c=R_{c,k}^{\top}(p_k-p_k^r),\qquad
\dot e_k^c=R_{c,k}^{\top}(v_k-v_k^r).
\]

两条路线均在线选择四维动作

\[
a_k=[k_{n,k},k_{t,k},d_{n,k},d_{t,k}]^{\top},
\]

并形成

\[
K(a_k)=\operatorname{diag}(k_{t,k},k_{t,k},k_{n,k}),\qquad
D(a_k)=\operatorname{diag}(d_{t,k},d_{t,k},d_{n,k}).
\]

内层 operational-space 控制器按

\[
w_k^{\rm cmd}=w_k^r-K(a_k)e_k^c-D(a_k)\dot e_k^c,
\qquad
\tau_k=J_k^{\top}R_{c,k}w_k^{\rm cmd}+\tau_k^{\rm dyn}
\]

渲染该动作。这里 (w_k^r) 含期望法向力与扫描切向参考，
$\tau_k^{\rm dyn}$ 是机器人已知动力学补偿。论文方法研究的是 $a_k$ 如何产生；它不把内层阻抗控制器本身包装成贡献。

法向与切向各用一个刚度和阻尼，而不是学习完整 $6\times6$ 阻抗矩阵，是一项有意的 Target adaptation：它对应研究问题中的法向-切向耦合选择，保留最关键的接触自由度，同时把在线 horizon verification 或 action-set certification 控制在固定低维空间。若后续证据表明切平面各向异性不可忽略，动作可扩展为两个切向特征值和一个有界方向角，但这不是当前草案默认结构。

### 1.2 决策充分的历史状态

每个高速伺服周期产生安全可观测量

\[
o_t=[e_t^c,\dot e_t^c,w_t^c,\dot w_t^c,
v_{\rm slip,t},A_{\rm patch,t},\kappa_t,
E_t,a_{t-1},r_t],
\]

其中 (w_t^c) 为六轴接触 wrench 在接触坐标中的表达，
$v_{\rm slip,t}$ 是由切向微动/视觉或触觉获得的相对滑动代理，
$A_{\rm patch,t}$ 与 $\kappa_t$ 是可用时的接触面积和局部曲率描述，
(E_t) 是交互能量账本，(r_t) 包含扫描速度、方向和期望法向力。缺失的可选传感量以 mask 输入，不能由未来信息补齐。

长度为 (L) 的因果历史

\[
h_k=\{(o_t,a_t)\}_{t=k-L}^{k-1}\cup\{o_k\}
\]

由一个小型 gated recurrent encoder 产生

\[
z_k=E_\phi(h_k).
\]

选择 GRU 而不是无条件堆叠原始窗口，是因为材料松弛、加载-卸载滞回、驻留时间和滑动历史都具有不同时间尺度，而更新预算要求状态递推的计算量与历史长度解耦。GRU 不是科学贡献；它只是实现 Candidate 所要求的“最小安全可观测历史状态”的具体载体。

表示学习不以材料类别、Young 模量或完整未来观测重建为主要目标。两个历史 (h,h') 被视为决策等价，当它们在允许动作下诱导的鲁棒可行阻抗、动作排序和联合 outcome 差异不超过预声明容差。这个定义直接承接 VES 的 downstream equivalence，并把 value/policy 等价改造为 contact-capacity/impedance-decision 等价。

### 1.3 动作条件接触容量模型

共同的 capacity model 是一个受动作条件化的 latent state-space model：

\[
z_{j+1}=F_\theta(z_j,a_j,r_j),\qquad
(\mu_{i,j},s_{i,j})=G_{\theta,i}(z_j,a_j,r_j),
\]

其中 $G_\theta$ 不预测“材料是什么”，而是预测第 $j$ 步、约束 $i$ 的 signed capacity margin 及尺度。学习的接触 margins 至少包括

\[
\begin{aligned}
m^{\rm keep}&=f_n-f_n^{\min}, &
m^{\rm load}&=f_n^{\max}-f_n,\\
m^{\rm slip}&=v_{\rm slip}^{\max}-\|v_{\rm slip}\|, &
m^{\rm moment}&=M_{\rm patch}^{\max}-\|M_{t}\|.
\end{aligned}
\]

这些 labels 均来自动作实施后的传感结果或已声明的物理阈值，不要求识别唯一摩擦系数或材料参数。未来 wrench 和位姿误差也由 head 输出，以计算法向力误差、轨迹误差与关节力矩 margin。若某平台没有可靠的 slip 或 contact-patch 观测，则对应 margin 不能被宣称为直接可校准；必须改为较弱的可观测 surrogate，并相应缩小论文 claim。

能量、gain-rate 和可由机器人模型直接计算的执行器约束不交给神经网络“学习”：

\[
\begin{aligned}
E_{j+1}&=E_j-\Delta t[P_j]_++\eta\Delta t[-P_j]_+,\\
P_j&=(w_j^{\rm int})^{\top}v_j^c,\qquad E_{j+1}\ge E_{\min},\\
\tau_{\min}&\le J_j^{\top}R_{c,j}w_j^{\rm cmd}+\tau_j^{\rm dyn}\le\tau_{\max},\\
a_{\min}&\le a_j\le a_{\max},\qquad
|a_j-a_{j-1}|\le\Delta a_{\max}.
\end{aligned}
\]

其中 $w_j^{\rm int}$ 是按“机器人向环境注能为正”的端口符号表达的实测或预测交互 wrench。能量账本把净注能计为消耗、可回收耗散按 $0\le\eta\le1$ 记账。它是研究问题明确要求的同一可行关系的一部分，不是求解完成后的 passivity correction。该离散账本只有在实际端口功率测量、参考项处理和内层跟踪误差满足所声明条件时才支持 passivity/energy claim。

### 1.4 训练目标、校准与支持域

训练数据由声明 operating envelope 内的材料、几何、速度、方向、驻留和加载历史构成，并包含硬约束以内的安全 gain probing；否则 action-conditional model 对未探测阻抗没有可辩护的支持。数据分为 representation/model training、residual calibration 和最终比较三部分，后两者不得反馈给 $E_\phi,F_\theta,G_\theta$。

训练损失为

\[
\mathcal L=
\mathcal L_{\rm margin}
+\lambda_z\mathcal L_{\rm latent}
+\lambda_{\rm set}\mathcal L_{\rm feas}
+\lambda_{\rm reg}\mathcal L_{\rm decision}.
\]

- $\mathcal L_{\rm margin}$ 拟合多步 signed margins 与 wrench/误差量。
- $\mathcal L_{\rm latent}$ 约束 rollout consistency，防止 latent transition 只拟合单步。
- $\mathcal L_{\rm feas}$ 惩罚预测可行集与由观测 rollout 标注的可行集发生 false-feasible/false-infeasible 变化，并对 false feasible 赋更高代价。
- $\mathcal L_{\rm decision}$ 是 SPO-style downstream regret：在相同 episode state 上，比较预测容量所选动作与 observed/counterfactual rollout 中最低联合代价动作的真实差值。它扩展了固定线性 feasible set 下的 SPO，因为误差在这里还会改变非线性可行集。

对冻结后的模型，在独立 calibration episode-action sequences 上计算联合单侧残差

\[
R_\ell=max_{i,j}
\frac{\mu_{i,j}^{(\ell)}-m_{i,j}^{(\ell)}}
{s_{i,j}^{(\ell)}+\epsilon},
\]

取 split-conformal 分位数 $q_{1-\alpha}$，得到控制器实际使用的下界

\[
\underline m_{i,j}=\mu_{i,j}-q_{1-\alpha}(s_{i,j}+\epsilon).
\]

用最大残差而不是分别校准每个 margin，是为了让声明的 $1-\alpha$ 对象与“一个 sequence 中所有被控制器消费的 margins 同时被下界覆盖”一致。该保证仍只是针对预声明、交换性成立的 calibration population 的 marginal sequence-level coverage；它不是任意分布漂移下的逐状态保证。

支持域在 outcome 发生前确定。对冻结的 covariate-action embedding
$u=[z,a,r]$，定义 calibration set 的 $k_s$-NN 半径
$d(u)$，并以 calibration 距离的预声明分位数 $\tau_s$ 为界。若一条动作或动作序列中任一 $d(u)>\tau_s$，则使用按 envelope cell 预计算的最坏观测 margin 下界
$\underline m^{\rm env}$，而不是继续使用 conformal residual law。若 envelope cell 没有物理或数据下界，方法必须把该动作判为不可行；它不能用“保守模式”掩盖知识缺失。

这套 calibration/support 设计迁移 Predict-then-Calibrate 的独立校准与 optimizer-consumed uncertainty set，但 action-conditional nonlinear margins、联合残差、支持域检查和 envelope replacement 都是 Target adaptation。它们尚未经过本 run 的 Principle Evaluation。

---

## 2. Method A：Decision-Calibrated Capacity MPC with Verified Incumbent Rendering

对应 Candidate：`P-CAPACITY-CONDITIONED-FEASIBLE-INCUMBENT@3`

### 2.1 论文核心命题

Method A 建立的核心对象不是“预测器加 MPC 加 fallback”，而是一个具有唯一命令身份的 anytime relation：只有在当前 contact-capacity binding 下通过完整非线性验证的 horizon sequence 才能进入 incumbent register，机器人只能渲染该 register 的首个阻抗。计算时间影响当前 incumbent 的性能，但不改变命令来自同一已验证关系这一事实。

其闭环关系为

\[
h_k\rightarrow z_k\rightarrow
\mathcal P_k(\mathcal C_k)\rightarrow
\operatorname{Verify}_k(A)\rightarrow
A_k^{\rm inc}\rightarrow a_k^{\rm applied}.
\]

### 2.2 容量条件鲁棒 horizon relation

在阻抗更新周期 $T_u$ 上，优化长度为 $H$ 的阻抗序列

\[
A_k=\{a_{k|k},\ldots,a_{k+H-1|k}\}.
\]

机器人局部线性化动力学与 capacity latent rollout 联合形成预测状态
$\chi_j=[e_j^c,\dot e_j^c,z_j,E_j]$。在所有支持域绑定和 calibrated uncertainty realization
$\xi_j\in\mathcal U_k(z_j,a_j)$ 下，求解

\[
\begin{aligned}
\min_{A_k}\quad
J_k(A_k)=&\sum_{j=0}^{H-1}\bigl[
w_f(f_{n,j}-f_n^d)^2+w_x\|e_{t,j}\|_2^2\\
&+w_s\,\ell_{\rm slip}(\underline m_j)
+w_c\,\ell_{\rm keep}(\underline m_j)
+\|a_j-a_{j-1}\|_{W_a}^2\bigr]
+V_f(\chi_H)\\
\text{s.t.}\quad
&\chi_{j+1}=\widehat F(\chi_j,a_j,\xi_j),\\
&\underline m_{i,j}(z_j,a_j)\ge0,\quad i\in\mathcal I_c,\\
&E_{j+1}\ge E_{\min},\quad
\tau_{\min}\le\tau_j\le\tau_{\max},\\
&a_{\min}\le a_j\le a_{\max},\quad
|a_j-a_{j-1}|\le\Delta a_{\max},\\
&\chi_H\in\mathcal X_f.
\end{aligned}
\]

$\mathcal I_c$ 包括 retention、overload、slip、contact moment/wrench 等 contact-capacity margins；force regulation 和 tangential tracking 是主要 performance terms，但它们的 overload/contact-loss 边界仍是硬约束。权重 $w_f,w_x,w_s,w_c,W_a$ 在部署前固定并报告，不随比较结果调节。

终端集合 $\mathcal X_f$ 定义为在 envelope-conservative capacity 下重复一个 terminal impedance
$\kappa_f(\chi)$ 仍满足一步容量、能量、执行器和 gain-rate 约束的状态集合。这里 $\kappa_f$ 不是独立安全控制器，而是同一 horizon relation 的终端 completion rule；若无法构造非空 $\mathcal X_f$，Method A 不具备其所需的 recursive-feasibility 起点。

### 2.3 Feasibility-first 求解与完整验证

具体在线求解器采用 warm-started trust-region sequential convex programming：

1. 围绕当前 incumbent sequence 线性化机器人预测、latent rollout 和 margin heads。
2. 在 trust region 内解一个固定上限迭代的 QP，优先降低约束 violation，再降低 $J_k$。
3. 得到的 trial sequence 不具有命令资格；它必须回到冻结的原始非线性模型、当前 calibration/support binding 和原始能量/力矩约束中做 full rollout verification。
4. 只有 `Verify = true` 且 $J_k(A^{\rm trial})<J_k(A^{\rm inc})-\epsilon_J$ 时才允许提交。

验证是“相对于论文所定义的模型与 calibrated bounds 完整”，并不等于已经证明真实世界安全。选择 SCP 是因为动作维度固定、horizon 较短且可 warm start；BOX-FDDP 仅提供 feasibility-driven warm-start update 的机制先例，并不提供 interruption totality 或 verified-incumbent identity。

### 2.4 Incumbent register 与 deadline-total 命令

incumbent register 是一个原子记录

\[
\mathfrak I_k=(A_k^{\rm inc},B_k,\rho_k,t_k),
\]

其中 $B_k$ 包含状态快照、capacity model 版本、calibration quantile、support/envelope binding 和约束版本，$\rho_k$ 是逐 margin verification certificate。没有匹配 $B_k$ 的 sequence 不能被渲染。

每次更新执行：

**Step A - bind and seed.** 冻结 $B_k$。把上一时刻已验证序列左移一位，并以 $\kappa_f$ 完成末端，得到

\[
\widetilde A_k=\operatorname{shift}(A_{k-1}^{\rm inc})\oplus\kappa_f.
\]

在预留的 verifier budget $T_v<T_u$ 内首先对
$\widetilde A_k$ 按当前 $B_k$ 重新验证。支持域从 calibrated 变为 unsupported 时，验证自动使用
$\underline m^{\rm env}$；上一 binding 的证书不会沿用。

**Step B - improve.** 若 seed 通过，它立即成为本周期 incumbent。随后 SCP 在剩余预算内产生任意数量 trial；每个改善只有在 full verification 完成后才通过 compare-and-swap 原子替换 register。

**Step C - render.** 在 $T_u-T_r$ 截止时刻停止产生新 trial，读取最后一次完整提交的
$\mathfrak I_k$，输出

\[
a_k^{\rm applied}=A_k^{\rm inc}[0].
\]

超时、QP infeasible、数值异常、trial verification 失败或未完成，都只意味着没有新 commit，不会触发另一条控制链。

如果 seed 在当前 binding 下不能按时验证，Method A 必须报告 `NO_VALID_INCUMBENT`；它不得渲染 stale action 后仍声称 deadline-total。这正是 Candidate A 的关键可证伪假设，而不是待工程补丁。

### 2.5 论文所需的分析对象

若选择 Method A，论文理论部分至少需要给出以下条件命题，而非提前宣称它们成立：

1. **Applied-action identity.** 由原子提交与只读 renderer 可直接证明：在存在当前 binding 的 incumbent 时，任何被施加动作都是某个完整验证序列的首项；trial、超时结果和 stale binding 不可达 renderer。
2. **Conditional recursive feasibility.** 若初始 incumbent 存在，capacity uncertainty set 包含真实 margin evolution，$\mathcal X_f$ 在 $\kappa_f$ 下鲁棒不变，且 shifted seed 每周期在 $T_v$ 内验证，则下一周期仍存在 current-binding incumbent。
3. **Population-scoped capacity validity.** 在 split-conformal 的交换性和 frozen-pipeline 条件下，联合 residual 给出声明 population 上的 sequence-level marginal coverage；它不推出逐状态或 arbitrary-shift 保证。
4. **Conditional physical constraint implication.** 只有在模型已知部分、inner-loop tracking error bound、sensor latency 和 calibrated lower margins 的假设同时成立时，verification relation 才能蕴含真实 wrench、contact、energy 与 actuator constraints。
5. **Anytime monotonicity.** 在 binding 固定时，额外完成的 verified commits 只能非增 incumbent objective；没有 commit 时 feasibility identity 不变。

### 2.6 Contributions

**A1. Decision-calibrated action-capacity state for soft-contact impedance.** 方法不再识别一个与决策间接相关的材料模型，而是学习由安全历史和候选阻抗共同决定的未来 contact-capacity margins；表示误差按其对 robust feasible sequence 与联合 force-motion-contact 决策的改变计价。这里真实迁移了 VES 的 downstream equivalence 与 SPO 的 decision regret，并把它们改造成含非线性 action-dependent constraints 的 sequential impedance decision。

**A2. Joint, support-aware lower bounds for the exact margins consumed by control.** 方法对一个 horizon sequence 中的多 margin 最大残差做独立单侧校准，并在 pre-outcome support 失败时用 envelope lower relation 取代，而不是继续外推或切换到外部 guard。迁移来源是 Predict-then-Calibrate 的 split discipline；联合 contact margins、动作条件化与 support replacement 是当前 Target 的必要改造。

**A3. Verified-incumbent rendering as the applied impedance relation.** 性能、contact/wrench、slip、energy、actuator、gain-rate 与 deadline 不再由 nominal optimizer 和事后 fallback 分别处理；只有 current-binding full verification 后的 horizon sequence 才拥有命令身份。BOX-FDDP 只贡献 feasibility-first update 的 solver precedent；原子 incumbent、stale-binding exclusion 和 interruption semantics 是从 RMC-JOINT-UPDATE-TIME-FEASIBILITY 推导出的 Target mechanism。

**A4. A falsifiable recursive-feasibility boundary for contact shifts.** 方法把“支持域变化后 shifted incumbent 能否在 deadline 内重新成立”暴露为核心科学假设，而不是在失败时静默依赖未知 emergency controller。该边界使路线是否能覆盖研究问题可以被明确否证。

### 2.7 关键技术风险

- **Incumbent existence risk：** 初次接触、突发失接触或 support binding 改变时，可能没有可及时验证的 seed；一旦发生，方法的 totality 主张失败。
- **Horizon model risk：** latent rollout 的误差会随 $H$ 累积，联合 conformal bound 可能快速变宽并使优化不可行。
- **Terminal-set risk：** 对材料、几何和滑动 envelope 同时鲁棒的非空 $\mathcal X_f$ 可能不存在，或者只允许性能很差的阻抗。
- **Verifier latency risk：** 原始 nonlinear rollout、uncertainty evaluation 和 torque/energy checks 必须具有可测的 worst-case runtime；平均速度不能支持 deadline claim。
- **Action coverage risk：** 安全 probing 不足会使最有价值的 impedance sequence 落入 unsupported cells，导致 envelope replacement 过度保守或空集。
- **Inner-loop implication risk：** 即使 sequence 对预测关系可行，未建模的 inner-loop tracking、frame estimation delay 或 wrench bias 仍可能破坏真实约束含义。

---

## 3. Method B：Decision-Calibrated Capacity Barrier Relation with Same-Set Performance and Backup

对应 Candidate：`P-CAPACITY-CONDITIONED-VIABILITY-RELATION@3`

### 3.1 论文核心命题

Method B 不保存一个跨周期 horizon policy 作为 totality 来源。它建立一个每周期重新计算的、由接触容量参数化的 robust viable action relation；性能动作与 deadline-ready backup 都是该 relation 的成员。只要 relation 的 bounded-time core 非空，optimizer 冷启动或中断不会把命令交给关系外的控制器。

闭环对象为

\[
s_k=(x_k,z_k,E_k,a_{k-1})
\rightarrow\mathcal A_v(s_k)
\rightarrow
\{a_k^{\rm backup},a_k^{\rm perf}\}\subseteq\mathcal A_v(s_k)
\rightarrow a_k^{\rm applied}.
\]

这里 $x_k$ 含当前 contact-frame motion、wrench、slip 与 patch observables；
$z_k$ 含历史引起的 action-capacity 差异；$E_k$ 和 $a_{k-1}$ 使能量与 gain reachability 成为 Markov state 的组成部分。

### 3.2 从 capacity margin 构造离散时间 barrier relation

Method B 选择 discrete-time robust control-barrier 形式，而不是在高维 latent state 上声称已经求得精确 Hamilton-Jacobi kernel。依据是：当前 Target 的 action 只有四维，接触 margins 可以被直接预测和单侧校准，而完整 latent reachability kernel 在数据和实时预算下没有证据支持。

对每个需要持续非负的当前 margin，记

\[
b_i(s_k)\in
\{m^{\rm keep},m^{\rm load},m^{\rm slip},m^{\rm moment},
E-E_{\min},m^{\rm torque}\}.
\]

capacity model 给出候选动作下的最坏下一步下界
$\underline b_i^+(s_k,a)$。定义 barrier residual

\[
\psi_i(s_k,a)=
\underline b_i^+(s_k,a)-(1-\gamma_i)b_i(s_k),
\qquad 0<\gamma_i\le1.
\]

于是连续动作空间中的 capacity-conditioned viable relation 为

\[
\mathcal A_v(s_k)=
\left\{a:
\psi_i(s_k,a)\ge0\ \forall i,
a_{\min}\le a\le a_{\max},
|a-a_{k-1}|\le\Delta a_{\max}
\right\}.
\]

对 energy、gain-rate 与 model-computable torque，
$\underline b_i^+$ 使用显式方程或 interval robot model；对 contact retention、overload、slip 与 patch moment 使用 calibrated capacity lower bounds。在当前 $b_i\ge0$、真实 successor 被 uncertainty set 覆盖的条件下，
$\psi_i\ge0$ 蕴含下一时刻 $b_i^+\ge0$。反复选择
$a_k\in\mathcal A_v(s_k)$ 因而形成一个条件性的 forward-invariance argument。

$\gamma_i$ 是允许 margin 向边界收缩的速率，在部署前固定。小
$\gamma_i$ 保留更多 reserve 但可能导致空集，大 $\gamma_i$ 接受更快接近边界；它不能按最终结果事后选择。

### 3.3 Bounded-time core action set

仅定义一个连续非线性集合仍不能解决 deadline totality，因为“求集合中的任意点”本身可能超时。Method B 因而把机器人实际 gain command resolution 与 gain-rate bound 用于构造固定大小的 reachable core：

\[
\mathcal G_k=
\left\{\operatorname{clip}
(a_{k-1}+D_{\Delta}q):
q\in\{-1,0,1\}^4\right\},
\]

其中 $D_\Delta$ 是每周期允许的法向/切向 stiffness、damping 基本增量，重复项删除后最多有 81 个动作。这个选择意味着阻抗以有界小步变化；跨多个周期仍可覆盖完整允许区间。若硬件分辨率或任务动态要求更密网格，可增加 level 数，但必须重新给出 worst-case inference time。

所有 $\mathcal G_k$ actions 在一次固定形状的 batched capacity inference 中完成评估，得到

\[
\mathcal A_v^{\rm core}(s_k)=\mathcal G_k\cap\mathcal A_v(s_k).
\]

该 core 不是额外安全层，而是同一 continuous viable relation 的一个可在 deadline 前穷举的子集。若 core 为空，Method B 报告 `EMPTY_VIABLE_CORE`；它不能调用关系外的 fixed damping 或 emergency gain 后继续宣称 same-set totality。

### 3.4 同集合 backup 与 performance selection

首先在 core 中计算 normalized certificate reserve

\[
\rho(s_k,a)=
\min_i\frac{\psi_i(s_k,a)}{\sigma_i+\epsilon}
-\lambda_\Delta\|a-a_{k-1}\|_2^2.
\]

bounded-time backup 为

\[
a_k^{\rm backup}=
\arg\max_{a\in\mathcal A_v^{\rm core}(s_k)}\rho(s_k,a).
\]

它优先最大化最弱 constraint reserve，并在 margin 相同的情况下减少 gain jump。该动作与 support/calibration binding 一起存入只读 deadline register。

在 backup 已就绪后，用剩余预算在 continuous
$\mathcal A_v(s_k)$ 内进行 performance refinement：

\[
a_k^{\rm perf}=\arg\min_{a\in\mathcal A_v(s_k)}
\left[
w_f\widehat e_f^2(a)+w_x\|\widehat e_t(a)\|^2
+w_s\ell_{\rm slip}(a)+w_c\ell_{\rm keep}(a)
\right].
\]

具体求解采用从 core 中最低 predicted cost member 出发的 bounded-iteration SQP。只有完成原始
$\psi_i$ membership check 的解才可覆盖 backup。若 refinement 超时、数值失败、解不在当前
$\mathcal A_v$ 或 support binding 已变化，则渲染
$a_k^{\rm backup}$。因此 performance 与 backup 的差别只在同一集合中的选择准则，不在是否遵守约束。

这一路线不要求用一个 horizon sequence 维持可行性；其关键假设改为每个声明状态都有一个按时可求的 nonvacuous
$\mathcal A_v^{\rm core}$。短 horizon 可以只用于 performance score，但不能决定 action membership，否则会重新引入未完成 horizon 求解导致的 totality 依赖。

### 3.5 在线算法

每个 $T_u$ 周期执行：

1. 更新 $z_k=E_\phi(h_k)$，冻结当前 calibration/support binding。
2. 从 $a_{k-1}$ 生成最多 81 个 reachable core actions。
3. 一次 batched rollout 计算每个动作的
$\underline b_i^+$、显式 energy/torque/gain margins 和
$\psi_i$。
4. 删除不属于 $\mathcal A_v$ 的动作；若 core 非空，立即存储最大
$\rho$ member 作为 deadline backup。
5. 在剩余预算内进行 continuous performance refinement；通过 current-binding membership check 才可提交。
6. deadline renderer 输出最后一个 current-binding member：优先 completed performance member，否则输出 stored backup。

由于 core size 固定，步骤 2-4 的 worst-case latency 可以用网络 batch 推理、显式 margin 计算和固定 81 元素 reduction 的上界表达；是否真的满足目标频率仍需后续测量，不能由算法形式直接宣称。

### 3.6 论文所需的分析对象

1. **Conditional forward invariance.** 若当前 $b_i(s_k)\ge0$，真实 successor 位于 calibrated/physical uncertainty set，且 $a_k\in\mathcal A_v(s_k)$，则 barrier inequality 给出 $b_i(s_{k+1})\ge0$。归纳可得到声明条件下的 nonnegative-margin persistence。
2. **Same-set applied-action identity.** renderer 只有两种输入，而 performance 和 backup 都必须携带 current-binding membership record；因此任何被施加动作都属于同一
$\mathcal A_v(s_k)$。
3. **Bounded backup evaluation.** 在 encoder 与 capacity batch 的已测 worst-case execution time 加上固定 reduction 小于 $T_u-T_r$ 时，非空 core 的 backup 可在 deadline 前得到。
4. **Calibration scope.** joint split-conformal 只为声明 population 中 capacity lower bounds 提供 marginal coverage；barrier invariance 仍条件于这些 lower bounds 实际覆盖 successor。
5. **Grid-to-continuous relation.** core 是
$\mathcal A_v$ 的子集，因此 core member 的 certificate 不依赖 action interpolation。网格非空性不能由 continuous set 非空自动推出；需要 margin Lipschitz 与 grid resolution 条件，或直接作为可证伪假设。

### 3.7 Contributions

**B1. Viability-defined decision-sufficient contact capacity.** 历史表示只保留会改变 calibrated barrier margins、viable impedance membership 或联合 outcome 的信息。VES 的 downstream equivalence 与 SPO 的 decision regret 被改造成针对 action-conditioned viability relation 的表示学习准则，而不是用于完整环境重建。

**B2. A capacity-conditioned barrier relation coupling all applied-action constraints.** contact retention/slip、wrench、energy、actuator 与 gain-rate 通过同一个下一步 robust barrier residual 定义可用阻抗；性能只能在该 relation 内选择。HJ controlled-invariance 与 predictive barrier work 提供数学形式基础，但没有被宣称为完整 Source migration，因为已有 nominal/supervisor 或 post-hoc correction 结构没有被保留。

**B3. A bounded-time viable core containing the deadline backup.** 利用低维 normal-tangential action 和真实 gain resolution，方法在固定大小的 reachable set 中先计算最大最弱 margin 的 backup，再允许 continuous performance refinement。它把 computation availability 变成 action relation 的组成部分，而不是 optimizer 失败后的外部控制模式。

**B4. Support-aware certificate semantics.** Predict-then-Calibrate 的独立 residual-set discipline被迁移到 certificate 实际消费的 nonlinear next-margin lower bounds；unsupported action 只能使用同一 barrier relation 的 envelope lower bounds，无法被证明时则从集合中删除。

### 3.8 关键技术风险

- **Empty-set risk：** 在接触跃迁、低 energy、gain-rate 限制或保守 support replacement 下，
$\mathcal A_v^{\rm core}$ 可能为空；这直接否定路线的 deadline-total claim。
- **One-step certificate risk：** discrete barrier condition可能局部成立却把系统带到下一周期几乎空集的边界；较小 $\gamma_i$ 或 terminal reserve 可缓解，但会加重保守性。若必须依赖长 horizon 才避免该问题，B 的 bounded-time优势会缩小。
- **Learned-successor risk：** barrier 的物理含义完全依赖 calibrated lower bounds 覆盖真实 successor；population-level coverage 不能自动升级为 deterministic safety certificate。
- **Grid conservatism risk：** 81-action core 可能错过窄的 continuous viable channel；加密网格又会增加 worst-case inference budget。
- **Frame/history discontinuity risk：** 法向估计跳变、contact loss 或 slip sensor aliasing 会同时改变 $x_k,z_k$ 和 margins，使前一步 certificate 不再有意义。
- **Performance-collapse risk：** 最大 reserve backup 可能长期选择极低刚度或高阻尼，虽留在 relation 中却无法保持扫描精度与速度；这会使方法满足约束但失去研究问题中的联合性能价值。
- **Certificate terminology risk：** 在没有模型误差 bound、sensor/inner-loop bound 与 nonempty-core 证据前，只能称“calibrated conditional certificate relation”，不能写成已经成立的 unconditional safety guarantee。

---

## 4. Human Selection 所需的直接比较

| 比较维度 | Method A：verified incumbent MPC | Method B：capacity barrier relation |
|---|---|---|
| 实际建立的核心数学对象 | current-binding 下完整验证的 impedance horizon sequence 与原子 incumbent register | current state 下 robust barrier-defined impedance action set 与 bounded viable core |
| 时变阻抗如何产生 | 对短 horizon 联合优化 (k_n,k_t,d_n,d_t)，仅渲染已验证 incumbent 的首项 | batched 评估 reachable action core，先得 max-min-margin backup，再在同集合连续优化 performance action |
| totality 的来源 | shifted/terminal-completed incumbent 在每周期按时重新验证 | 每个声明状态的 viable core 非空且可在固定时限内穷举 |
| 对 contact history 的使用 | 预测多步 capacity，历史差异改变 horizon feasibility 与排序 | 预测下一步 robust margins，历史差异改变 barrier membership |
| 对计算中断的处理 | 未完成 trial 从未取得命令身份，保留最后一次完整 commit | backup 先于 refinement 完成；refinement 中断时渲染 same-set backup |
| 主要理论负担 | initial/recursive feasibility、terminal set、horizon uncertainty 与 verifier WCET | calibrated successor validity、barrier forward invariance、core nonemptiness 与 batch WCET |
| 主要性能潜力 | 显式预见扫描速度、曲率和接触演化，较容易优化联合 horizon cost | 更快、更直接，但 one-step viability 和 coarse core 可能保守或短视 |
| 对冷启动的脆弱性 | 高；没有 current-binding incumbent 就没有合法命令 | 较低；只要当前 viable core 非空即可生成 backup |
| 最可能的科学 killer | support/capacity 跳变后 seed 无法及时重新验证 | 声明状态存在 continuous 可行动作但 bounded core 为空，或 barrier relation 无用/不成立 |
| 与最接近 Target priors 的主要差异 | 不采用“先优化、再 tank/filter/fallback”；命令身份绑定 full verification 和 interruption semantics | 不采用 nominal action 加 supervisor；performance 与 backup 都由 capacity-conditioned relation生成 |

两条路线共享 capacity representation 并不等于方法拼接，因为它直接修复同一个 accepted history-aliasing RMC；它们真正互斥的部分是第二个 RMC 的解决机制。A 把跨周期保存的可行 horizon policy 作为 totality 载体，B 把当前状态的非空 viable action relation 作为载体。Human Selection 不应把二者简单组合成“B 给 A 做 backup”，除非未来能够证明 terminal/incumbent 与 viability core 是同一关系的必要分解；当前没有这样的因果必要性证据。

## 5. 当前草案的 claim 边界

- 尚未执行 Principle Test Design、Principle Evaluation 或任何 method refinement。
- 尚未证明 history state 比 memoryless contact state 更有决策价值。
- 尚未验证 joint conformal margins 在部署策略诱导的 action distribution 上达到声明覆盖率。
- 尚未证明 Method A 的 terminal set/current-binding incumbent 始终存在，也未测 verifier worst-case latency。
- 尚未证明 Method B 的 barrier relation 真实前向不变、viable core 非空或 batch inference 满足实时周期。
- 尚未验证任一路线相对固定阻抗、调度、自适应/学习 VIC、constrained MPC、energy-tank MPC 或 safety-filter baselines 的性能优势。
- Contributions 是由方法结构自然提炼的 prospective contributions，不是最终 novelty verdict。

因此，本文只回答 Human Selection 前的一个问题：如果分别把两个已评审 Candidate 当作论文 Principle，它们能否自然长成因果闭合、数学对象明确、在线执行路径完整且风险可证伪的核心 Method。正式选择之后，所选路线仍必须按 Harness 进入 Principle Test Design，而不是把本草案当作已经验证的最终方法。
