# R15 Method Synthesis：以 T-RO 为主线的预测闭环阻抗 MPC

## 0. 结论先行

本轮收敛到唯一首选方法：**contact-conditioned mean–deviation impedance MPC（CMI-MPC）**。

它不是 T-RO、MPVIC、NNBO 与 passivity 模块的并列组合，而是对 T-RO 主线中一条具体失效关系的最小修改：

\[
\underbrace{\text{T-RO 预测未来接触，但执行器阻抗预定义}}_{M_0\text{ 的具体 failure}}
\Longrightarrow
\underbrace{\text{在同一预测问题中，让接触坐标系阻抗塑造未来闭环偏差}}_{M_1}
\Longrightarrow
\underbrace{\text{由未来软接触状态在线选择法向--切向阻抗}}_{M_*}.
\]

最终保留 T-RO 的 contact dynamics、robot dynamics、DDP + IK + Projection、ADMM-MPC 和 torque feedforward；只把原来预定义的 feedback impedance 改为短时域、接触状态驱动的在线决策。为了避免 A2-II 在 nominal tracking error 为零时失去作用，预测器显式分为：

1. nominal mean rollout，由 \(\tau_{\mathrm{ff}}\) 决定；
2. closed-loop deviation/covariance rollout，由 \((K_n,K_t)\) 通过 \(A_{\mathrm{cl}}\) 决定。

阻尼不是四个自由增益中的额外搜索维，而由接触局部动力学和选定刚度导出。Fu et al. 的医疗接触 QP 不替代这条关系：它是当前测力误差驱动的单步法向刚度优化，缺少 future normal--tangential contact、slip/contact-loss 与 uncertainty authority；但它提供了两个应当迁移的成熟机制：把 impedance update 实现为小型 QP，以及用能量账本约束时变刚度。最终 R15 因此把原先含糊的“DDP 同时搜索 gains”收敛为 **DDP nominal block + future-sensitivity impedance QP**，并把 tank 从仅计算 gain-change energy 修正为同时计算 moving-reference work。学习 residual 在本轮没有满足进入条件，故不纳入 Final Method。

---

## 1. 证据边界与 Source Mechanism

| 作用 | 已有工作/正式 Evidence | 本方法只迁移的机制 | 明确不迁移的部分 |
|---|---|---|---|
| 主干 | T-RO deformable-contact-aware MPC，`ZDySGaNsMn8J` | \(x=[q,\dot q,F_e]\)、接触--机器人耦合、DDP/IK/Projection ADMM、前馈+反馈执行结构 | spherical geometry、predefined impedance |
| 探头几何 | AuSoScan，`QPuQ4sfxSTIJ` | cylinder--plane 法向几何；只有 action-changing 时才启用 sliding asymmetry/deformation resistance | 不把整套成像/扫描系统移植进控制器 |
| 在线阻抗决策 | constrained stiffness/damping planning `USER-TRO-2022-3216078`；MPVIC `imoj6X2imAgJ`、`xlNjdqnC2fMJ` | 阻抗序列作为预测决策、约束内联合选择、时变增益能量授权 | 不采用学习自由空间动力学，不复制另一套 MPC 主干 |
| 均值--偏差控制权分离 | 用户提供的 model-predictive impedance/GP 原文公式；与 `9vRJMR2dgG4J` 的 uncertainty-tightened force MPC 证据一致 | 阻抗改变闭环状态转移与协方差，即使 nominal error 为零仍有独立作用 | 不要求 GP；本轮使用局部接触 Jacobian 与有界噪声 |
| 局部参数更新 | NNBO `j4KJ1EdUyYAJ` | projected EW-RLS 作为探头接触参数中心的低阶在线估计器 | 不迁移 HJB/single critic 到 Route A |
| 时变阻抗安全 | `3HbLu0M801YJ`、`xlNjdqnC2fMJ` | 显式计算 \(\tfrac12e^T\Delta K e\) 并由 tank 授权 | 不声称一般主动扫描闭环全局无源 |
| 超声组织模型驱动 VIC | Beber et al., ICRA 2024, DOI `10.1109/ICRA57147.2024.10610167` | HC 参数图、force/penetration constraints、在线 stiffness QP 与 tank 的目标领域基线 | 不迁移离线全表面触诊、current-point normal-focused decision |
| 医疗接触在线 QP | Fu et al., IEEE TIM 2024, DOI `10.1109/TIM.2024.3372209` | 有界在线 stiffness QP 的标准形式、单步实时实现与超声探头基线 | 不迁移 PID 目标、`N=1`、仅法向优化或固定高切向刚度 |
| 移动参考能量核算 | Fu et al., IEEE T-RO 2025, DOI `10.1109/TRO.2025.3548493` | robot-side 功率项 \(\tfrac12e^T\dot Ke+\dot p_{ref}^TKe\) 与 tank lower/upper bounds | 不迁移 bilateral teleoperation、haptic feedback 或其整系统无源定理 |

上述迁移均位于同一条 T-RO 因果链上的替换接口；没有新增第二个并行控制器。原论文公式用于核对主线，新出现的 mean--deviation 关系、最小阻尼参数化和 Route B 淘汰结论在下文显式推导。

### Intervention-level Source Mechanism 审计

| Source 的真实问题与 intervention | Source 的实际作用 | Target 映射与结论 | 迁移边界 |
|---|---|---|---|
| T-RO deformable-contact MPC：把 \(\dot F_e\) 与 robot dynamics 一起放进 DDP/IK/Projection ADMM | future contact 改变 nominal torque/trajectory | 映射到 Target 的完整主干，**PASS** | 其 impedance 预定义，正是待修接口 |
| AuSoScan：以 cylinder--plane pressure/deformation 描述超声探头，并建模 sliding asymmetry | 改变 \(F_n(\delta)\)、局部斜率和切向阻力 | 只迁移 geometry baseline，**PASS** | asymmetry/resistance 只有 action-changing 才激活 |
| Beber ICRA 2024：用离线 HC 组织参数图构造 force/penetration bounds，并在当前步 stiffness QP 中加入 tank constraints | 证明超声场景中 tissue mechanics、QP、physical bounds 与 tank 可形成统一 VIC | 作为 strongest target-domain mechanism prior 与 killer baseline，**PASS** | 无 future sliding-contact rollout、无 \(k_t\) decision，不替换 R15 核心关系 |
| Model-Based Variable Impedance Learning Control：把未来 stiffness sequence 作为 predictive action | 未来 interaction rollout 可以对 gain sequence 排序 | 迁移“gain 必须通过未来结果被选择”的关系，**PASS** | learned free-space dynamics、CEM 和无硬约束实现不迁移 |
| Model Predictive Impedance Control with Gaussian Processes：同一 nominal force 可由不同 impedance 实现，impedance 通过闭环矩阵改变 covariance | 以 mean/covariance cost 和 chance constraint 消除 nominal authority 冗余 | 迁移 mean--deviation control-authority split，**PASS** | GP 不是必要条件；本方法使用 probe physics + bounded uncertainty |
| NNBO：HC 参数识别后，用 HJB single critic 得到 optimal force relation | 轻量更新 nonlinear environment relation | projected EW-RLS 映射到局部参数中心，**PASS**；整套 HJB 映射在 n/t constrained gains 上 **FAIL** | 不把 critic 与 MPC 并列 |
| MPVIC/passivity 工作：用 tank 或等价能量状态限制时变 stiffness 注入 | 将增益变化的正能量纳入 action authority | 映射到 feedback-gain update，**PASS** | 仅声明 feedback port；主动 feedforward/reference 另算外部供给 |
| Fu TIM 2024：由当前 force error 构造有界 stiffness QP，`N=1`，仅优化探头法向刚度 | 证明医疗/超声接触中小型 QP 在线选刚度是成熟可实现的 | 迁移 QP realization，**PASS**；作为 strongest simple baseline | 不足以产生 future-conditioned \((k_n,k_t)\)，不能替代 mean--deviation prediction |
| Fu T-RO 2025：在共享控制中把 \(\tfrac12e^T\dot Ke+\dot p_{ref}^TKe\) 纳入全局 tank | 修复 moving reference 下只计算 \(\Delta K\) 的能量漏项 | 迁移 robot-side modulation-power ledger，**PASS** | 双边/触觉控制与 human input 对自主扫描无必要，**FAIL** |

因此 Source genealogy 是 `T-RO 主干 → AuSoScan geometry replacement → predictive-impedance gain authority → Fu-style convex impedance update → robot-side modulation-energy authorization`。Beber 的工作不新增需要迁移的模块，而是把“tissue mechanics + ultrasound VIC + QP + tank”整体划入 prior，并升级为 target-domain killer baseline。Fu 的 QP 和 tank 都只替换 R15 已经存在的 solver/energy 接口，不形成第二个控制器。NNBO 只在 estimator 接口提供局部机制；它的 HJB 分支经重推后被淘汰。这里的箭头表示同一主线的逐项替换，不表示把多个完整系统串联。

### Fu 2024/2025 补审：它们改变了什么，未改变什么

Fu TIM 2024 在低速医疗接触中令

\[
F_{ext}=K_c\tilde x-D_c\dot x,
\]

并以当前测力误差的 PID 补偿构造单步 QP：

\[
\min_{K_c}\ \tfrac12K_c^TGK_c+b^TK_c,
\quad K_{min}\preceq K_c\preceq K_{max},\quad |F_{ext}|\le F_{max},
\]

\[
G=\tilde x^TQ\tilde x+R,\qquad
b=Q\tilde x(D_c\dot x-F_{des}-F_{com})-RK_{min}.
\]

其 passivity regulator 将 `K_c=K_cons+K_v`，令 `T(x_t)=0.5 x_t^2`，并按

\[
\dot T=\sigma\dot{\tilde x}^{T}D_c\dot{\tilde x}
+\tilde x^TK_v\dot{\tilde x}
\]

存入耗散、支付 variable-stiffness work；当 tank 低于 `E_min` 时关断 `K_v`，实际退回 `K_min`。这是成熟的当前步 passivity gate，但没有在 horizon 内为未来 gain/reference schedule 预留能量。

其实时实现取 `N=1`，只优化探头 `z` 轴刚度，`x/y` 轴保持高刚度。KUKA LWR4+、六维力传感器和线阵超声探头实验覆盖软/硬材料转换、斜面和连续滑扫；TIM 论文报告扫描最大 RMSE 从 constant-stiffness 的 `2.04 N` 降至 `0.57 N`。这使它成为必须击败的 **reactive normal-stiffness QP**，也排除了“医疗超声中在线刚度 QP”本身作为创新。

但该 QP 的预测量来自当前 spring--damper 与 force error，而不是未来 deformable-contact rollout；它不区分法向保持与切向滑移容量，不传播模型不确定性，也没有让未来接触状态改变 `k_t`。因此它能解决材料变化后的快速 force correction，却不能区分“当前测量相同、未来几何/摩擦状态不同”的两个扫描决策。用它整体替换 R15 会消除本任务的目标能力。

Fu T-RO 2025 把上述 QP 放入 human--robot shared control，并指出时变 stiffness 与移动参考共同引入

\[
P_{mod}=\tfrac12\tilde x^T\dot K_c\tilde x+\dot x_{rd}^TK_c\tilde x
\]

这一非无源功率；全局 energy tank 对该功率、触觉反馈和阻尼耗散统一记账，在能量低时关断主动项并注入附加阻尼。论文在 `1 kHz` haptic loop、在线 QP、线阵超声探头、软/硬及转换材料上验证，并报告自主实验最大 median force error `0.25 N`。它证明 moving-reference term 不能被 R15 的旧 \(\tfrac12e^T\Delta K e\) 账本忽略；但 bilateral teleoperation、haptic force 与 human command 并不是自主扫描缺口，迁移它们只会形成不必要的第二主线。

补审后的选择不是默认保留旧 R15，而是：**保留 future mean--deviation relation；用 Fu 的 QP 结构把 impedance update 凸化；用 Fu 的 robot-side 功率分解补全 energy ledger。** 两篇直接相关顶刊工作已经闭合 solver 与 energy 两个接口，剩余问题是本方法自身的 action-fidelity/WCET 实验，而不是缺少另一个 Source Mechanism，故不再扩展检索。

---

# 2. Route A：T-RO 主线

## 2.1 \(M_0\)：原主线

T-RO 的离散状态和 torque action 为

\[
x_k=[q_k^T,\dot q_k^T,F_{e,k}^T]^T,\qquad u_k=\tau_k,
\]

\[
\dot F_e=G_{\mathrm{TRO}}(q,\dot q,F_e;\eta),
\]

\[
\ddot q=M(q)^{-1}\left[\tau-C(q,\dot q)\dot q-g(q)-J(q)^TF_e\right].
\]

其 MPC 以接触力、末端位姿和 torque 为目标，施加关节、力矩和接触约束，并用 DDP、IK、Projection 的 ADMM 分裂求解。执行端为

\[
\tau_k^{\mathrm{app}}=\tau_{\mathrm{ff},k}
+K_q(q_k-q_k^{\mathrm{nom}})
+C_FJ(q_k)^T(F_{e,k}^{\mathrm{nom}}-F_{e,k}).
\]

原论文已经能预测未来 deformable contact，但机器人内层阻抗为安全预设量；因此“未来接触状态”没有进入阻抗选择关系。

## 2.2 Failure A0：geometry mismatch

原球--平面 Hertz 关系

\[
d=\left(\frac{9F_n^2}{16E^{*2}R}\right)^{1/3},\qquad a=\sqrt{Rd}
\]

不对应长轴超声探头的主要接触截面。错误的 \(F_n(\delta)\) 和 \(\partial F_n/\partial\delta\) 会同时改变：

- nominal force prediction；
- local environment stiffness \(k_e\)；
- damping feasibility；
- covariance propagation中的 \(A_{\mathrm{cl}}\)；
- contact-loss/slip margin，最终改变最优阻抗。

这不是“模型精度更高即可”的泛化理由，而是模型误差直接通过上述五条关系改变 action feasibility/ranking。

## 2.3 \(M_0\rightarrow M_1\)：最小 probe-contact 替换

令 \(\{n,t_1,t_2\}\) 为探头接触坐标系，\(\delta\ge0\) 为法向压入，\(L,R\) 为探头有效长度和曲率。AuSoScan 的 cylinder--plane 静态关系给出

\[
p(r)=p_s\sqrt{1-r^2/a^2},\quad
p_s=\frac{E^*}{2}\sqrt{\frac{\delta}{R}},\quad
F_n=\kappa_c\delta,
\]

\[
\kappa_c=\frac{\pi E^*L}{4},\qquad
\frac1{E^*}=\frac{1-\nu_p^2}{E_p}+\frac{1-\nu_t^2}{E_t}.
\]

对连续滑动采用可微、符号一致的最小切向关系

\[
F_t=-\mu F_n\tanh(v_t/v_s)-c_tv_t,
\]

并通过链式法则得到 \(\partial F_t/\partial \delta\) 和 \(\partial F_t/\partial v_t\)。只有当静态 cylinder baseline 在 matched replay 中产生持续、action-changing 残差，才启用 AuSoScan 的动态不对称项

\[
F_n=\frac{\pi LE^*}{8}
\left(2\sqrt{R\delta}+\Delta k_v\right)\sqrt{\delta/R},
\]

以及相应 deformation resistance。保留条件不是拟合误差降低，而是它使最优 \((k_n,k_t)\)、约束活跃集或 SCAN/WITHDRAW 决策发生可重复变化。

为避免把 algebraic contact force 误写成没有演化方程的独立状态，令局部表面位置/速度为外生短时预测 \((p_s,\dot p_s)\)，接触运动学为

\[
\delta=\max(0,\psi(q,p_s)),\qquad
\dot\delta=J_n(q)\dot q-\dot p_{s,n},
\]

\[
v_t=J_t(q)\dot q-\dot p_{s,t},\qquad
F_e=h_c(\delta,\dot\delta,v_t;\eta).
\]

静态 cylinder baseline 下最小动态状态是

\[
x=[q^T,\dot q^T]^T,
\]

而 \(F_e\) 是由 \(h_c\) 约束的 lifted output。于是闭合 ODE 为

\[
\dot x=
\begin{bmatrix}
\dot q\\
M^{-1}\!\left(\tau-C\dot q-g-J^Th_c(x,p_s,\dot p_s;\eta)\right)
\end{bmatrix}.
\]

若 action-changing test 要求 relaxation、动态不对称或 pre-slip，则只添加被证伪项的内部状态 \(\xi_c\) 及显式 \(\dot\xi_c=g_c(\xi_c,\delta,v_t;\eta)\)，并令 \(x=[q,\dot q,\xi_c]\)、\(F_e=h_c(x;\eta)\)。这保留了 T-RO 的 `contact dynamics → robot dynamics` 因果顺序，同时避免未闭合的 force-state DAE。若实现希望继续使用 T-RO 的 \(F_e\) 状态，可由 \(\dot F_e=(\partial h_c/\partial x)\dot x+(\partial h_c/\partial p_s)\dot p_s\) 得到等价 lifted dynamics。

接触参数中心 \(\hat\eta=[\hat E^*,\hat\mu,\hat c_t,\ldots]\) 可用 NNBO 中的 projected EW-RLS 机制低阶更新：

\[
L_k=\frac{P_k\phi_k}{\lambda+\phi_k^TP_k\phi_k},\qquad
\hat\eta_{k+1}=\Pi_{\mathcal E}\left[
\hat\eta_k+L_k(y_k-\phi_k^T\hat\eta_k)\right],
\]

\[
P_{k+1}=\lambda^{-1}(I-L_k\phi_k^T)P_k.
\]

\(\mathcal E\) 是离线校准的物理包络；在单次 horizon 内 \(\hat\eta\) 固定，避免 estimator 与 optimizer 形成未定义的代数环。\(P_\eta\) 只用来收紧预测，不赋予超出 \(\mathcal E\) 的安全权威。

## 2.4 Failure A1：为什么 A2-I 不成立为最终结构

A2-I 令 \(\theta_I\rightarrow\pi_{imp}\rightarrow\tau\rightarrow f_{TRO}\)，即以 impedance parameters 取代 torque action。设

\[
\tau=\tau_0-J_c^T(K_ce+D_c\dot e).
\]

若 nominal rollout 满足 \(e=\dot e=0\)，则

\[
\frac{\partial \tau}{\partial\theta_I}=0,
\qquad
\frac{\partial x_{k+1}^{\mathrm{nom}}}{\partial\theta_I}=0.
\]

此时 gain-only action 对 nominal problem 无控制权；若允许通过改变 reference 或 \(\tau_0\) 恢复控制权，实际又把前馈/轨迹 action 隐藏进“阻抗”，失去参数语义并破坏 T-RO 的 torque authority。因此 A2-I 作为唯一 action 被淘汰。

## 2.5 Failure A2：朴素 A2-II 也会退化

朴素 A2-II 保留 \(\tau_{ff}\) 并同时优化 feedback gain，但若优化器只 rollout nominal state，仍有

\[
e_k^{\mathrm{nom}}=\dot e_k^{\mathrm{nom}}=0
\Rightarrow
\partial\bar x_{k+1}/\partial\theta_I=0.
\]

因此仅把 \(K,D\) 加进 action vector 并不能使其可优化。必须让阻抗进入未来**闭环偏差结果**，而非继续给 nominal cost 叠加 gain penalty。

## 2.6 \(M_1\rightarrow M_2\)：mean--deviation 预测闭环

最终 action 为

\[
a_k=[\tau_{\mathrm{ff},k}^T,k_{n,k},k_{t,k}]^T,
\]

其中

\[
K_{c,k}=\operatorname{diag}(k_{n,k},k_{t,k},k_{t,k}).
\]

执行律围绕 nominal contact trajectory：

\[
\tau_k=\tau_{\mathrm{ff},k}
-J_c(q_k)^T\left[K_{c,k}\delta p_{c,k}+D_{c,k}\delta v_{c,k}\right].
\]

### Nominal mean

\[
\bar x_{k+1}=f_p(\bar x_k,\tau_{\mathrm{ff},k};\hat\eta_k).
\]

### Closed-loop deviation

令 \(\delta x_k=x_k-\bar x_k\)、\(\delta p_c=C_{p,k}\delta x_k\)、\(\delta v_c=C_{v,k}\delta x_k\)。局部线性化得

\[
\delta x_{k+1}=A_{\mathrm{cl},k}\delta x_k+G_kw_k+S_{\eta,k}\tilde\eta_k,
\]

\[
A_{\mathrm{cl},k}=A_{0,k}
-B_{\tau,k}J_{c,k}^T(K_{c,k}C_{p,k}+D_{c,k}C_{v,k}).
\]

协方差递推为

\[
P_{k+1}=A_{\mathrm{cl},k}P_kA_{\mathrm{cl},k}^T
+G_kQ_kG_k^T+S_{\eta,k}P_{\eta,k}S_{\eta,k}^T.
\]

现在即使 \(\delta x_k^{\mathrm{nom}}=0\)，只要 horizon 至少跨过一个注入噪声/参数不确定性的节点，一般仍有

\[
\frac{\partial P_{k+1}}{\partial k_i}
=\frac{\partial A_{\mathrm{cl},k}}{\partial k_i}P_kA_{\mathrm{cl},k}^T
+A_{\mathrm{cl},k}P_k
\frac{\partial A_{\mathrm{cl},k}^T}{\partial k_i}\ne0.
\]

于是 \(\tau_{ff}\) 决定 mean motion/force，\(K_c\) 决定 disturbance rejection、force variance、contact retention 与 slip probability。未来预测到的 \(\bar\delta,\bar v_t,k_e,c_e,P_\eta\) 又改变 \(A_{\mathrm{cl}}\) 和风险项，形成所需连续因果链：

\[
\text{future soft-contact state}
\rightarrow A_{\mathrm{cl}},P,F\text{-margin}
\rightarrow(k_n,k_t)^*
\rightarrow\text{future closed-loop contact outcome}.
\]

这正是阻抗相对 torque feedforward 的独立控制权证明。

严格地说，若 \(P_0=0\)，第一步 \(P_1=W_0\) 对当前 gain 可能不敏感；但当 \(H\ge2\) 且 \(W_0\ne0\) 时，\(P_2=A_{cl,1}W_0A_{cl,1}^T+W_1\) 已对 gain 敏感。因此测试必须包含至少两步预测，或者直接传播一组有界 deviation scenarios。若 \(P=W=0\) 且所有 robust margins 都不依赖 gain，则优化器必须冻结 nominal gain，而不能用正则项伪造“在线选择”。

## 2.7 \(M_2\rightarrow M_3\)：最小充分 impedance parameterization

不把 \(D_n,D_t\) 作为独立优化维。对每个 \(i\in\{n,t\}\)，局部闭环二阶模态为

\[
m_{\mathrm{eff},i}\ddot e_i+(d_i+c_{e,i})\dot e_i+(k_i+k_{e,i})e_i=0,
\]

其中

\[
m_{\mathrm{eff},i}=
\left(e_i^TJ_cM^{-1}J_c^Te_i\right)^{-1},\quad
k_{e,i}=\frac{\partial F_i}{\partial p_i},\quad
c_{e,i}=\frac{\partial F_i}{\partial v_i}.
\]

选择设计阻尼比 \(\zeta_i\ge\zeta_{\min}>0\)，先导出未裁剪值

\[
\tilde d_i(k_i,\bar x_i,\hat\eta)=
2\zeta_i\sqrt{m_{\mathrm{eff},i}(k_i+k_{e,i})}-c_{e,i}.
\]

定义 \(d_i=\tilde d_i\)，并直接约束 \(d_i^{\min}\le\tilde d_i\le d_i^{\max}\)。若越界，对应 \(k_i\) 不可行；不能先裁剪再声称保持了目标阻尼比。这样两个刚度变量即确定四个 feedback 系数，并保证局部极点有明确解释。只有 matched constrained comparison 显示独立 \(D_i\) 改变可行性或联合 force/path outcome，才升级为四变量；否则二变量是最小充分参数化。

## 2.8 \(M_3\rightarrow M_*\)：目标、约束、能量与 solver

### 目标函数

\[
\begin{aligned}
J=\sum_{j=0}^{H-1} [&
\|\bar F_{n,j}-F_{n,j}^{ref}\|_{Q_F}^2
+\|\bar p_{t,j}-p_{t,j}^{ref}\|_{Q_p}^2
+\|\tau_{ff,j}\|_{R_\tau}^2\\
&+\operatorname{tr}(Q_xP_j)
+\rho_{sl}\,\mathcal R_{slip,j}
+\rho_{cl}\,\mathcal R_{loss,j}
+\|\Delta\log k_j\|_{R_k}^2 ]+V_f.
\end{aligned}
\]

\(\mathcal R_{slip}\) 由 \(|F_t|-\mu F_n\) 的 tightened margin 构造；\(\mathcal R_{loss}\) 由 \(F_n\) 接近接触下界的概率或保守界构造。它们依赖 \(P_j\)，所以 gain 的优化不是装饰性 regularization。

### 同一关系内的约束

随机扰动与 epistemic 接触包络必须分开处理。对每个 \(\eta^v\in\operatorname{Vert}(\mathcal E)\) 或其保守 support-function 上界，先 rollout \((\bar x^v,P^v)\)；\(\beta\sigma\) 只收紧该 vertex 下的随机 deviation，不能替代对 \(\mathcal E\) 的 worst-case 检查。对每个 horizon 节点和每个保留 vertex 约束：

\[
q^-\le\bar q\pm\beta\sigma_q\le q^+,\quad
\dot q^-\le\bar{\dot q}\pm\beta\sigma_{\dot q}\le\dot q^+,
\]

\[
\tau^-\le\tau_{ff}\pm
\beta\sigma_{J^T(K\delta p+D\delta v)}\le\tau^+,
\]

\[
F_n^{\min}\le\bar F_n-\beta\sigma_{F_n},\qquad
|\bar F_t|+\beta\sigma_{F_t}\le
\mu(\bar F_n-\beta\sigma_{F_n}),
\]

\[
k_i^-\le k_i\le k_i^+,qquad
|\Delta\log k_i|\le r_i,qquad
d_i(k_i)\in[d_i^-,d_i^+].
\]

离线校准包络 \(\mathcal E\) 对 hard constraints 始终有效；在线收缩的 \((\hat\eta,P_\eta)\) 可改善 nominal ranking 或增加约束，但不能删除任何 full-envelope vertex 或放宽其 hard margin。固定 facet/vertex 数是实时实现参数；若 vertex 枚举过大，必须使用能证明外包络包含性的 support-function/zonotope 近似。这样估计误差不会在检测延迟期间取消安全约束。

### 时变 impedance 与移动参考的 robot-side 能量授权

定义接触坐标系误差 `e_c = p_c_ref - p_c = -delta p_c`、
`delta v_c = dot p_c - dot p_c_ref = -dot e_c` 与 feedback storage

\[
V_{K,k}=\tfrac12e_{c,k}^TK_{c,k}e_{c,k}.
\]

旧 R15 只计算 gain-change injection；Fu T-RO 2025 的 robot-side 功率分解表明，移动参考还会通过 \(\dot p_c^{ref,T}K_ce_c\) 做功。对一个离散预测步，定义保守 modulation-energy debit

\[
\overline{\Delta E}_{mod,k}^+=
\sup_{(e_c,\eta)\in\mathcal D_k\times\mathcal E}
\left[
\tfrac12e_c^T(K_{c,k+1}-K_{c,k})e_c
+h\dot p_{c,k}^{ref,T}K_{c,k}e_c
\right]_+,
\]

其中 \(\mathcal D_k\) 是由同一 mean--deviation predictor 给出的 containing deviation set；因此 tank 不是只对 nominal error 记账。能量罐递推为

\[
E_{T,k+1}=\min(E_T^{\max},E_{T,k}
+h\rho_d \delta v_{c,k}^TD_{c,k}\delta v_{c,k})
-\overline{\Delta E}_{mod,k}^+,
\]

并施加 `E_T,k+1 >= E_T,min`。若没有能量，优化器必须减小/冻结 `K_c` 或选择 WITHDRAW，而不是事后裁剪一个已经联合验证的 action。这里 debit 覆盖 gain variation 与 reference-motion elastic work；
\(\tau_{ff}\) 仍是显式主动供给，另受 task、torque 与 contact constraints 约束。因此可声明的是 robot-side impedance/reference-modulation channel 的能量授权，不是含主动 feedforward 的整机全局无源。Fu T-RO 的 bilateral passivity theorem 依赖其 haptic/teleoperation interconnection，不能原样移植。

### Deadline-total applied-action relation

令 \(\mathcal A_{scan}(b_k)\) 为在 belief state \(b_k=(\bar x_k,P_k,\mathcal E,E_{T,k})\) 下通过全部 hard checks 的首节点集合；令预验证卸载动作 \(a_{wd}(b_k)\) 满足设备 envelope 上的 rate、torque、耗散和法向卸载约束。实际发布关系定义为

\[
\pi_{pub}(b_k)=
\begin{cases}
a_{inc,k}, & t<t_{ddl},\ a_{inc,k}\in\mathcal A_{scan}(b_k),\\
a_{wd}(b_k), & \text{otherwise}.
\end{cases}
\]

因此 totality 不是由“优化器通常收敛”推出，而由集合包含条件

\[
\forall b\in\mathcal B_{adm},\qquad a_{wd}(b)\in\mathcal A_{safe}(b)
\]

推出。该条件必须在完整 robot/probe envelope 上离线验证；若存在某个 admissible state 使卸载动作也不可行，则 totality claim 立即失败，必须缩小 operating envelope，而不能继续叠加 safety module。

### Solver：T-RO 主干内的 future-sensitivity impedance QP

Fu TIM/T-RO 证明单步 stiffness QP 在医疗接触中成熟，但直接使用其当前误差 QP 会丢失 future-contact authority。R15 的最小修改是保留 T-RO 的 DDP nominal block，并把 gain update 写成同一 ADMM 内的序列凸 QP，而不是让 DDP 黑箱式地同时搜索 gains。

用 \(\ell_j=[\log k_{n,j},\log k_{t,j}]^T\) 保证正刚度。在当前 nominal/contact/gain iterate \((\bar x_j,\bar P_j,\bar\ell_j)\) 上，定义

\[
A_j=A_{cl,j}(\bar\ell_j),\qquad
A_{\ell,j,r}=\left.\frac{\partial A_{cl,j}}{\partial\ell_{j,r}}\right|_{\bar\ell_j}.
\]

对每个 horizon gain 分量 `r`，协方差敏感度显式递推：

\[
\begin{aligned}
S_{P,j+1}^{(r)}={}&A_jS_{P,j}^{(r)}A_j^T
+A_{\ell,j,r}\bar P_jA_j^T
+A_j\bar P_jA_{\ell,j,r}^T
+S_{W,j}^{(r)},\\
S_{P,0}^{(r)}={}&0,
\end{aligned}
\]

其中不作用于节点 `j` 的分量令 `A_{ell,j,r}=0`；`S_W` 只在 noise map 随 gain 改变时非零。由此得到 future force/path statistics、slip/contact-loss margin 和 torque feedback margin 对整段 `Delta ell` 的 Jacobian `J_y,J_g`。impedance block 求解

\[
\begin{aligned}
\min_{\Delta\ell,s}\quad &
\tfrac12\|J_y\Delta\ell+\bar y-y^{ref}\|_Q^2
+\tfrac12\|\Delta\ell\|_R^2+\tfrac{\rho_s}{2}\|s\|^2,\\
\mathrm{s.t.}\quad &
\bar g_h+J_{g_h}\Delta\ell\le0,\\
&\bar g_s+J_{g_s}\Delta\ell\le s,\quad s\ge0,\\
&\ell^-\le\bar\ell+\Delta\ell\le\ell^+,\quad
|\ell_j-\ell_{j-1}|\le r,\quad\|\Delta\ell\|_\infty\le r_{tr},\\
&d_i^-\le \bar d_i+J_{d_i}\Delta\ell_i\le d_i^+,\qquad
\widehat E_{T,j+1}^{lb}(\Delta\ell)\ge E_T^{min}.
\end{aligned}
\]

`g_h` 包含 force、slip、contact retention、joint/torque 和 full-envelope constraints，不允许 slack；`g_s` 只包含 task-risk surrogate。\(\widehat E_T^{lb}\) 是由 modulation-energy supremum 的保守 affine upper bound 得到的 tank-energy lower bound；square-root damping 也使用 trust region 上的保守 affine bound，使子问题保持 QP。candidate 随后必须回代原 nonlinear mean--deviation/contact/tank equations 做 exact acceptance；不通过则缩小 trust region 或保留上一 verified incumbent。

- **DDP block**：只优化 \(\tau_{ff}\) 与 nominal robot/contact trajectory。
- **Impedance QP block**：用未来闭环敏感度优化每节点 `(k_n,k_t)`，而不是当前 force error。
- **IK block**：保持末端 scan path 与 robot configuration 的一致性。
- **Projection block**：对 exact nonlinear joint/torque/contact/slip/gain-rate/damping/tank/robust margins 做验证和投影。
- **ADMM consensus**：对共享 \(q,\dot q,F_e,\tau_{ff},k\) 做固定次数协调，并始终保存 verified incumbent。

当 `H=1`、删除 covariance/slip/contact-loss sensitivity、固定 `k_t` 且用当前 PID force target 代替 `y_ref` 时，该 block 退化为 Fu 的医疗 stiffness QP；因此 Fu 是 R15 的严格简化基线，而不是并列模块。R15 不新增外层 CEM、第二个 MPC 或 actor。协方差可只保留 contact-relevant reduced subspace，gain block 每节点只有两个变量。

## 2.9 每个控制周期的完整算法

1. 读取 \(q,\dot q\)、六维 wrench 和 probe pose，构造接触坐标系及 \(\delta,v_n,v_t\)。
2. 更新 projected EW-RLS 的 \(\hat\eta,P_\eta\)，并检查其是否仍位于校准包络 \(\mathcal E\)。
3. 用上一周期解 warm start；rollout cylinder-probe nominal contact 与 robot dynamics。
4. 沿 nominal rollout 计算 \(k_e,c_e,m_{eff}\)，由候选 \((k_n,k_t)\) 导出 \((d_n,d_t)\)。
5. 递推 \(A_{cl}\)、reduced covariance \(P\) 及其对整段 \(\ell=[\log k_n,\log k_t]\) 的敏感度，计算 contact-loss、slip、torque 和 joint tightened margins。
6. DDP 更新 nominal \(\tau_{ff}\)；future-sensitivity QP 更新 \((k_n,k_t)\)；IK/Projection/ADMM 在固定迭代预算内协调两者。
7. 对 QP candidate 回代 exact nonlinear dynamics、full contact envelope 和包含 moving-reference work 的 tank balance；只有 hard constraints 全部通过才更新 verified incumbent。
8. 在截止时间前原子发布 incumbent 的整个首节点 \((\tau_{ff},K_c,D_c)\)，低层高频执行 feedback law，高层保持短时域滚动。
9. 若无可验证 incumbent、求解超时、接触状态越出 \(\mathcal E\)，执行预验证 WITHDRAW：停止切向推进、使用耗散且 rate-limited 的 gain，并沿最后可信法向卸载。

## 2.10 理论闭合与实时判断

### Dynamics closure

nominal \(f_p\)、deviation \(A_{cl}\)、noise/parameter covariance、gain sensitivity、gain-derived damping 和含 reference work 的 tank 都有显式递推；horizon 内 estimator 固定，无代数环。QP 只是这些方程在 trust region 内的局部求解形式，发布前的 exact rollout 防止线性化被误当成真实可行性。

### Control authority / redundancy

\(\tau_{ff}\) 通过 \(\bar x\) 控制 mean；\(k\) 通过 \(A_{cl}\) 控制 \(P\) 与 robust margins。只要 \(P_k\ne0\) 或 \(Q_k+S_\eta P_\eta S_\eta^T\ne0\)，gain 对未来结果具有非零一阶作用。若实际噪声和模型不确定性均可忽略，固定阻抗足够，variable impedance 科学问题本身即失去必要性。

### Observability / estimability

只估计进入 \(F_n,F_t\) 和局部斜率的低阶参数；需要 persistent excitation 的完整材料辨识不是安全前提。辨识不足时使用 full envelope 收紧或 WITHDRAW，不以置信度替代 authority。

### Stability / passivity

冻结 gain 时，\(m_{eff}>0\)、\(k+k_e>0\)、\(d+c_e>0\) 给出局部渐近稳定二阶模态；时变 gain 与 moving reference 的正 modulation energy 由 robust tank debit 支付。结论是局部闭环稳定 + robot-side impedance/reference-modulation energy authorization；主动 \(\tau_{ff}\)、未建模通信延迟以及超出 \(\mathcal E\) 的组织仍不在无源性声明内。

### Constraint feasibility / deadline totality

每个发布动作在同一 Projection relation 中检查；超时不是“保持上一控制”而是切换到已验证的 WITHDRAW。因此 action relation 对 deadline/infeasibility 是 total，但 WITHDRAW 对完整设备 envelope 的可行性必须作为实现前的硬验证。

### Real-time feasibility

T-RO 原系统报告约 150 ms 平均高层求解及 100 Hz force loop；Fu 的医疗系统证明 `N=1` stiffness QP 可在线运行且 haptic loop 达 `1 kHz`，但两者都没有证明本方法的 horizon sensitivity、robust projection 与 exact acceptance 的 deadline。新增 gain block 是每节点两个变量的凸 QP，协方差可降到 contact-relevant subspace；合理目标是 5--10 Hz 高层与 500--1000 Hz 内层。**WCET 仍是唯一决定性实现接口**：必须在目标控制器上验证固定 horizon、固定 ADMM/DDP/QP 次数、reduced covariance 和完整 projection 的 99.9% latency；若不能在 deadline 内产生 verified incumbent，Final Method 只允许 WITHDRAW，不能声称实时扫描成立。

---

# 3. Route B：NNBO 主线完整重推

## 3.1 \(M_0^B\)：原 NNBO

NNBO 先将单方向 Hunt--Crossley 模型改写为

\[
\dot x=-\frac{K_e}{B_e}+\frac{f}{B_e(x-x_e)^n}+\varepsilon,
\]

再把 tracking generator 状态并入 \(X=[e,r^T]^T\)，得到 interaction-force control-affine 辅助系统

\[
\dot X=F(X)+G(X)f,
\]

目标

\[
J=\int_0^\infty [X^T\tilde QX+f^TRf+\zeta_\varepsilon^2(X)]dt,
\]

HJB 为

\[
0=\min_f\{X^T\tilde QX+f^TRf+\zeta_\varepsilon^2+\nabla V^T(F+Gf)\},
\]

因而

\[
f^*=-\tfrac12R^{-1}G^T\nabla V^*.
\]

single critic 逼近 \(V^*\)。原文随后把 \(f^*(X)\) 重写成类似 impedance 的非线性 force--position relation，并用 Newton downhill 求 reference position \(x_r\)；它并没有直接求一个受约束的 \((K_n,D_n,K_t,D_t)\) 序列。这一区分对 Route B 是否能自然扩展至扫描任务是决定性的。

## 3.2 Failure B0 与 contact replacement

HC 并非对所有软组织都错误；Evidence `_8NXx8-VkcgJ` 明确排除了这种过度声明。具体 failure 是：其单方向点接触状态无法表达长轴 probe geometry、法向接触保持和切向 friction capacity，因此不同 scan states 可能投影成相同 scalar interaction state，却需要不同 \((k_n,k_t)\)。

按用户要求，首先用 Route A 的 cylinder-probe normal + sliding dynamics 替换 HC，定义

\[
X=[e_n,\dot e_n,e_t,\dot e_t,F_n,F_t,\hat\eta]^T,
\]

\[
\dot X=F_p(X,\tau,\eta),
\]

其中 \(F_n=\kappa_c\delta\)，\(F_t=-\mu F_n\tanh(v_t/v_s)-c_tv_t\)，robot/contact mapping 与 Route A 一致。至此 dynamics closure 可以建立，但 HJB 到 optimal impedance 的映射出现两个互斥情况。

若忠实重推原 NNBO，scan objective 至少应变为

\[
J_B=\int_0^\infty\left[
e_{np}^TQ_ne_{np}+e_t^TQ_te_t+f^TR_ff
+\rho_{sl}\psi_{sl}(F_n,F_t)^2+\zeta_\eta(X)^2
\right]dt,
\]

其中 \(e_{np}\) 同时包含 normal-force/penetration error，\(e_t\) 为 tangential path error。对于 force-action auxiliary system \(\dot X=F_p+G_pf\)，形式 HJB 为

\[
0=\min_f\{\ell_B(X,f)+\nabla V_B^T(F_p+G_pf)\}.
\]

忽略 hard constraints 且 \(\ell_B\) 对 \(f\) 为二次时，仍有

\[
f_B^*=-\tfrac12R_f^{-1}G_p^T\nabla V_B^*.
\]

single critic 可写为 \(\hat V_B=\hat W^T\phi(X)\)，策略

\[
\hat f_B=-\tfrac12R_f^{-1}G_p^T\nabla\phi(X)^T\hat W,
\]

并以 Hamiltonian residual

\[
\delta_H=\hat W^T\nabla\phi(F_p+G_p\hat f_B)+\ell_B(X,\hat f_B)
\]

更新 \(\hat W\)。所以更换 contact model 后，`interaction state → dynamics → objective → HJB → critic → optimal force relation` 可以重推；真正不能自然闭合的是最后的 `optimal force relation → unique feasible normal–tangential impedance`。

## 3.3 情况 B-I：保留 control-affine wrench HJB

若仍令 HJB action 为 interaction force/wrench \(f=[f_n,f_t]^T\)，且 probe-contact dynamics 能写成 \(\dot X=F_p(X)+G_p(X)f\)，则无约束 closed-form \(f^*\) 保留。然而 impedance realization 要解

\[
f_i^*=k_ie_i+d_i\dot e_i.
\]

当 \((e_i,\dot e_i)\ne(0,0)\) 时，若 \(k_i,d_i\) 均自由，单个方程对应无穷多对增益；当 \(e_i=\dot e_i=0\) 时，任何有限增益都不能实现非零 \(w_i^*\)。即使令 \(d_i=d_i(k_i)\)，也只有在 reference error 提供足够基向量且 torque/contact constraints 可行时才有解。

因此 HJB 优化的是 wrench，不是物理增益。所谓 \(f^*\rightarrow\theta_I^*\) 不是一般单值映射，Newton reference realization 解决的是“怎样产生该 force relation”，并不唯一选择 feedback impedance；它也不保证该 realization 与 energy、slip、actuator 和 gain-rate limits 同时一致。

## 3.4 情况 B-II：直接以 impedance 为 HJB action

若直接把四个独立 gains 定义为 \(\theta_4=[k_n,d_n,k_t,d_t]\)，在固定 reference error 下 robot acceleration 对 gains 可以写成控制仿射形式

\[
\dot X=F_p(X)+G_\theta(X)\theta_4,
\]

因而**不能简单声称“换成 gains 后一定非仿射”**。但这里 \(G_\theta\) 的列分别乘 \(e_n,\dot e_n,e_t,\dot e_t\)：当相关 error 为零时对应列为零，HJB 无法从未来 contact uncertainty 中区分 gains；无约束二次 Hamiltonian 得到的 gains 还可能违反 positivity、rate、damping ratio 和 torque/contact constraints。加入 \(\theta\in\Theta(X,E_T)\) 后，最优律是 state-dependent constrained minimizer，而不是原 NNBO 的全局解析 single-critic policy。

若按 Route A 的最小充分 action 使用 \(\theta=[k_n,k_t]\) 和 \(d_i=d_i(k_i)\)，interaction dynamics 为

\[
\dot X=F_p(X,\theta;\eta),
\]

一般对 \(\theta\) 非仿射。HJB 变为

\[
0=\min_{\theta\in\Theta(X,E_T)}
\{\ell(X,\theta)+\nabla V^TF_p(X,\theta;\eta)\}.
\]

由于 \(d_i(k_i)\) 含平方根且 \(A_{cl}\)、covariance 和 contact risk 均依赖 gains，因此一般非仿射，不能再得到 NNBO 的解析 single-critic policy；每个状态仍需求一个受限非线性最小化。加入

\[
|F_t|\le\mu F_n,\quad F_n\ge F_n^{min},\quad
\tau\in[\tau^-,\tau^+],\quad
|\Delta\log k|\le r,\quad E_T\ge E_T^{min}
\]

后，问题成为 state-dependent constrained HJB/variational inequality。critic-only UUB 也不推出硬约束、port passivity 或 finite-time feasibility。

若再加入 actor、safety filter、covariance predictor、MPC projection 和 fallback，主线实际已变成 Route A 类型的 constrained predictive controller，NNBO 不再是保留的完整方法。

## 3.5 Route B 的理论与实现判定

- **Dynamics closure**：更换 probe contact 后可闭合。
- **Decision authority**：wrench-HJB 到 impedance underdetermined；impedance-HJB 非 control-affine，single-critic closed form 消失。
- **Observability**：n/t 接触状态可局部估计，但 safe excitation 不由 NNBO critic 保证。
- **Stability**：原证明只给特定条件下 tracking/UUB，不覆盖时变 n/t gain、hard contact constraints 和 energy tank。
- **Feasibility**：HJB 无自然机制同时处理 wrench/slip/actuator/rate/deadline constraints。
- **Real time**：single critic 原本轻量；修复上述问题所需 constrained online minimization 取消了这一优势。

**Route B 淘汰。** 它不是因为 learning 或 HJB 本身“不好”，而是因为目标 action 从 interaction wrench 变为受约束的 normal--tangential impedance 后，原主线的解析最优性和 single-critic realization 不能同时保留。NNBO 只保留为单轴 baseline，以及 Route A 接触参数 EW-RLS 的局部 Source Mechanism。

---

# 4. 可选 Source Mechanism 判定

本轮不加入 NMI、RSS、tactile prediction 或其他 learned residual。进入条件被严格定义为：在 matched physical baseline、相同信息和相同约束下，存在跨 held-out material/geometry/sliding/history 的持续 residual；该 residual 必须改变 \((k_n,k_t)^*\)、约束活跃集或 SCAN/WITHDRAW 决策，并且简单参数包络/不确定性收紧无法包含。现有正式 Evidence 尚未证明这一条件，故添加 learning 会重新造成方法拼接。

---

# 5. Cross-Route Comparison

| 判据 | Route A：T-RO mean--deviation impedance MPC | Route B：probe-contact NNBO |
|---|---|---|
| Problem fit | 未来接触状态直接改变 gain 的闭环风险与约束 | HJB先给 wrench，gain realization 不唯一 |
| 主线保留 | 保留 contact→robot→DDP/IK/Projection→ADMM | direct-gain 后失去 control-affine/single-critic 主干 |
| 修改必要性 | 精确替换 predefined impedance 接口 | 需要 actor/solver/filter 才能满足目标约束 |
| control authority | mean 与 covariance 两条通道可辨识 | nominal error 为零时 gain realization 奇异 |
| hard constraints | 原 Projection block 可自然扩展 | constrained HJB 不是原结构 |
| stability/passivity | 局部极点 + feedback tank，声明边界明确 | 原 UUB 不传播到时变 n/t impedance |
| 实时性 | 每节点2变量的 convex impedance QP，仍需整链 WCET 验证 | 修复后需在线 NLP，轻量优势消失 |
| Scientific Delta | contact prediction 从“只选 torque”变为“同时选 future closed-loop compliance” | 方法身份不稳定 |

唯一首选为 Route A。Route B 是有价值的失败推导，它排除了“只换 contact model 后继续 NNBO”的表面融合。

## 5.1 与 strongest closest priors 的直接比较

| Closest prior | 已经解决 | 对同一真实超声任务仍缺 | R15 的最小新增关系 |
|---|---|---|---|
| T-RO deformable-contact-aware MPC | 未来 deformable contact、robot dynamics、DDP/IK/Projection ADMM | feedback impedance predefined；future contact 只选择 nominal torque | future contact → `A_cl,P` → `k_n,k_t` → robust applied action |
| Beber ICRA 2024 ultrasound VIC | HC tissue map、online stiffness QP、force/penetration/tank constraints、dummy-torso/contact-loss validation | current-point/offline-map 驱动、重点为法向 force；无 future sliding-contact evolution、`k_t` decision 或 horizon slip/retention relation | 用 future n/t contact sensitivities 替换 current-point impedance wrench relation |
| Fu TIM 2024 medical VIC | 在线有界 stiffness QP、energy tank、线阵超声探头与软/硬转换验证 | `N=1`、当前 force-error 驱动、仅法向刚度；无 future n/t contact/slip/uncertainty relation | 用 T-RO horizon 的闭环敏感度替换 current-error QP 系数，同时保持小型 QP realization |
| Fu T-RO 2025 shared control | moving-reference/stiffness/haptic power的全局 tank；医疗接触与用户实验 | 面向 human shared control；仍是单步法向 stiffness，固定切向高刚度；其 bilateral theorem 不适用于自主 feedforward scan | 只迁移 robot-side `P_mod`，对 predicted deviation envelope 记账，不迁移 haptic/teleoperation |
| GP model-predictive impedance control / Jin/Xue MPVIC | gains 通过未来闭环 outcome、uncertainty、constraints 或 safety modes 被排序 | 非 probe-specific n/t deformable contact；不保持 T-RO 的 force-modulated robot/contact solver 与完整 scan constraints | architecture-specific mean--deviation authority 与 T-RO contact/Projection 的单主线闭合 |

这个比较给出一个可证伪的 necessity 边界：若在 matched replay 中 Beber 式 current tissue-map QP 或 Fu 式 `N=1` normal QP 与 R15 的 joint force--path、slip/contact-loss 和安全 margin 无显著差异，则 future horizon 与 `k_t` 不必要，R15 应退化为更简单的 target-domain 方案；不能以“预测更先进”为理由保留复杂度。

## 5.2 Final Method 三判据审计

### Problem fit：PASS，但问题陈述必须收窄

Beber ICRA 2024 与 Fu 两篇工作已经直接覆盖“根据组织参数或当前法向 force error 在线调整超声接触刚度”，并包含 QP、physical constraints、tank 和医疗接触验证。因此 R15 不能再以一般 force tracking、tissue-aware VIC 或 online stiffness QP 为问题。剩余且与超声连续扫描直接相关的问题是：在当前测量尚未显露变化时，未来 probe geometry、组织斜率、切向阻力与 friction capacity 已预示 force overshoot、slip 或 contact loss，控制器如何提前联合改变法向顺应性与切向路径保持。R15 的 `A_cl → P → constraints` 关系正好作用于这一缺口。

### Method fit / necessity：CONDITIONAL PASS

相对直接采用 Beber/Fu QP，R15 只保留三个额外自由度来源：horizon、normal--tangential stiffness pair、future deviation/uncertainty。它不独立优化 damping，不引入 actor/second MPC/learned residual；QP 复用 T-RO rollout 的 sensitivities，energy ledger 复用同一 deviation set。复杂度的必要条件是存在 current-state aliasing：相同当前 force/error、不同未来 contact trajectory 导致不同最优 `(k_n,k_t)`。matched replay 若不能观察到这一 action/outcome difference，审计结论自动降级为 current-step target-domain QP。

### Scientific strength：FORMULA-CLOSED，EMPIRICALLY OPEN

state、nominal/deviation dynamics、action、objective、hard/soft constraints、damping map、energy recursion、sequential solver、exact acceptance 与 deadline fallback 已形成闭合链；closest-prior limiting case 也可显式恢复。理论边界是局部 frozen-gain stability 与 robot-side modulation-energy authorization，不夸大全系统无源。顶会顶刊级强度仍取决于两个结果：future `(k_n,k_t)` 相对 Fu baseline 的 action-changing benefit，以及目标硬件上的 reduction fidelity/WCET。它们是验证条件，不再是待补文献接口。

---

# 6. Final Method 定义

## 6.0 Scientific Principle 与具体 realization

算法无关的科学原则是：

> **对每个候选 nominal action 与最小 normal--tangential impedance，使用未来软接触状态同时预测 nominal interaction outcome 和 feedback-conditioned closed-loop deviation outcome；只在同一关系中对 task cost、contact/wrench、actuator、impedance/reference-modulation energy 与 deadline 均有 authority 的 action 才可发布。**

这个原则不依赖 DDP、ADMM、Gaussian covariance 或某一种 tank 实现。T-RO + DDP/IK/Projection ADMM 是本项目最自然的具体 realization，因为它已经具有所需 contact/robot 主干和约束分裂；reduced covariance、fixed vertices、EW-RLS、energy tank 和 WITHDRAW 轨迹则是必须被原则测试证伪/确认的 realization hypotheses。这样可以清楚区分 Scientific Delta 与 solver engineering。

## 6.1 主干与 \(M_0\rightarrow M_*\)

\[
\begin{aligned}
M_0 &: \text{T-RO spherical deformable-contact torque MPC + predefined impedance},\\
M_1 &: \text{cylinder-probe normal/sliding contact + bounded local estimation},\\
M_2 &: \text{nominal mean rollout + feedback-dependent deviation/covariance rollout},\\
M_3 &: \text{action }[\tau_{ff},k_n,k_t],\ d_n,d_t\text{ from local closed-loop poles},\\
M_* &: \text{DDP nominal + future-sensitivity impedance QP},\\
&\quad\text{with robust energy-authorized, deadline-total IK/Projection ADMM}.
\end{aligned}
\]

## 6.2 Final state, dynamics and action

动态状态为

\[
x_k=[q_k^T,\dot q_k^T,\xi_{c,k}^T]^T,
\]

其中最小 cylinder baseline 令 \(\xi_c=\varnothing\)，contact wrench 是 algebraic output \(F_e=h_c(x,p_s,\dot p_s;\eta)\)；只有通过 action-changing test 的动态接触项才进入 \(\xi_c\)。为便于 Projection，优化器使用 lifted belief state

\[
z_k=[\bar q_k,\bar{\dot q}_k,\bar F_{n,k},\bar F_{t,k},\operatorname{vech}(P_k),E_{T,k}],
\]

估计器辅助状态为 \((\hat\eta_k,P_{\eta,k})\)。action 为

\[
a_k=[\tau_{ff,k},k_{n,k},k_{t,k}],
\]

nominal 与 deviation dynamics 分别为

\[
\bar x_{k+1}=f_p(\bar x_k,\tau_{ff,k};\hat\eta_k),
\]

\[
P_{k+1}=A_{cl,k}(\bar x_k,k_k,\hat\eta_k)P_kA_{cl,k}^T+W_k.
\]

lifted force variables通过 \(\bar F_{e,k}=h_c(\bar x_k;\hat\eta_k)\) 与动态状态相等约束，不再被当作缺少演化方程的自由状态。

这就是 prediction/optimal-control relation：未来接触的 geometry、force slope、sliding state 和 uncertainty 改变 \(A_{cl}\)、\(W\)、covariance sensitivities、tightened constraints 及可用 modulation-energy，从而由 horizon QP 在线选择 \((k_n,k_t)\)；DDP 仍只负责 nominal torque/trajectory。两个 blocks 通过原 ADMM consensus 与 exact Projection closure 相连，而不是串联两个控制器。

## 6.3 Scientific Delta

相对最近的 T-RO deformable-contact MPC，Scientific Delta 不是“更复杂的接触模型”，而是：

> 将预测的 deformable-contact trajectory 从仅服务于 feedforward torque 的外生信息，改造成同时决定 contact-frame feedback dynamics 的闭环状态；在同一个 DDP/IK/Projection ADMM 问题中，由未来 normal--tangential contact state 联合选择 nominal torque 与最小充分 impedance，并以显式 uncertainty、energy 和 deadline authority 决定可发布动作。

相对 Beber/Fu 的 ultrasound/medical online stiffness QP、现有 stiffness/damping planning 和 Haninger/Jin/Xue MPVIC，本方法的残余差异不是任何单独组件，而是 probe-specific future normal--tangential deformable contact 共同决定 nominal torque、feedback gains、contact margins 与 modulation-energy authority 的整体关系。在线 gain optimization、QP、MPC、covariance、uncertainty、tissue estimation 或 energy tank 本身均不构成 novelty。完整审计见 `METHOD_NOVELTY_AUDIT_R15.md`。

## 6.4 Scientific Contributions（final wording）

最终只保留三条主贡献；其层级依次是 **target-domain capability → closed-loop mechanism → real-time constrained realization**，而不是三个可拆卸模块。

1. **利用未来探头--组织接触状态，在线联合选择法向--切向阻抗。** 现有机器人超声方法已具有示范变阻抗、当前力/组织估计驱动的在线法向刚度 QP，以及预测软接触的 torque MPC，但尚未让未来 probe-specific deformable normal--tangential contact 联合决定 \(k_n,k_t\)。R15 建立这一关系，使相同当前测量、不同未来接触演化能够产生不同阻抗决策。

2. **把未来软接触预测从 nominal force/motion planning 扩展为 feedback-impedance decision state。** 一般 predictive/risk-sensitive impedance 已能根据未来交互与不确定性调节 gains；R15 对连续软组织滑动接触重新推导 mean--deviation dynamics：未来几何与接触参数既改变 nominal contact trajectory，也通过 \(A_{cl}(k_n,k_t)\) 改变 feedback deviation，二者共同决定 force、slip、contact-retention 与 actuator margins；zero-authority 时冻结 gains。

3. **推导面向连续扫描的 probe-contact-horizon sensitivity impedance QP。** 现有 ultrasound QP、online stiffness QP 与通用 multistep MPVIC 均不足以单独构成创新；R15 将整段 future deformable-contact 与 closed-loop deviation 对 \((k_n,k_t)\) 的敏感度压缩为两变量约束 QP，同时处理 future force、slip、contact retention、actuator、gain/damping 与 energy-admissibility margins，并由原非线性模型复验候选动作。

其中独立在线决策量是 \((k_n,k_t)\)，\((d_n,d_t)\) 由局部闭环极点/阻尼比关系导出。Energy tank 不列为独立创新：它只作为第 3 条中的 safety/assurance mechanism；除非进一步得到超出现有 medical/ultrasound VIC 的 robustness/passivity theorem，否则不升级为第 4 条贡献。

对应的 paper-ready English wording（每条少于 60 个英文单词）为：

1. **Robotic-ultrasound methods learn impedance from demonstrations, adapt normal stiffness from current contact, or predict soft contact only for motion/torque. We formulate future-contact-conditioned normal–tangential impedance: predicted probe–tissue evolution jointly selects \(k_n,k_t\), allowing identical current measurements but different future geometry, deformation, or sliding conditions to produce different feedback mechanics.**

2. **Predictive impedance priors anticipate uncertain interaction, but do not close feedback-gain selection with probe-specific deformable sliding contact. We derive coupled nominal and deviation dynamics in which future contact shapes mean interaction while \(A_{cl}(k_n,k_t)\) shapes force, slip, retention, and actuator margins; an authority test freezes gains when this coupling cannot change outcomes.**

3. **Ultrasound stiffness QPs are reactive, whereas generic MPVIC lacks probe-specific sliding-contact constraints. We derive a two-variable contact-horizon sensitivity QP whose coefficients encode future deformable-contact and closed-loop-deviation effects on force, slip, retention, actuator, gain/damping, and energy margins, followed by exact nonlinear revalidation for real-time publication of a feasible impedance action.**

## 6.5 唯一未闭合的决定性接口与最小验证

公式关系已经闭合；唯一尚未由现有证据证明的是目标硬件上的 WCET 和 model reduction fidelity。最小验证不是新一轮路线调研，而是：

1. 在记录的真实 ultrasound scan transitions 上比较 fixed impedance、Beber 式 current tissue-map QP、Fu 式 `N=1` reactive normal-stiffness QP、naive nominal A2-II 与 CMI-MPC，验证 future horizon 与 `k_t` 是否改变 force variance、slip/contact-loss margin 和 joint force/path outcome；
2. 比较 full covariance 与 contact-reduced covariance 的 action order 和 constraint decisions；
3. 在目标控制器固定 horizon/iterations 下测 99.9% latency，并强制 timeout/infeasibility，确认每周期只发布 verified SCAN 或 WITHDRAW；
4. 若 reduced formulation 不能保留 action order，或 WCET 超过 deadline 且无 verified incumbent，则淘汰实时扫描声明；不通过增加 learning 模块补救。

因此 R15 已经从多个路线收敛到一个可证伪的 Final Method，而未把尚待实验的实时性写成未经验证的事实。
