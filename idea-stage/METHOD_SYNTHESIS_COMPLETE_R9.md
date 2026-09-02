# Human Selection 前完整 Method synthesis（R9）

> 状态：Method Design 决策材料，不是 `FINAL_METHOD_PACKET`。下述性质是待验证的方法主张与成立条件，不表示实验、统计覆盖、稳定性或实时性已经得到证明。

## 1. 简洁技术路线

**DASCO-TAC：Decision-Sufficient Adaptive Soft-Contact Operator with Total-Action Control**

连续扫描的观测历史首先被压缩为一个只保留“会改变可行阻抗集合或阻抗排序”的预测状态；该状态和候选法向–切向阻抗共同决定未来力、运动、接触/滑移、六维 wrench、交互能量和执行器负载的联合结果。初始支持域内使用独立轨迹校准得到的 simultaneous constraint tube；离开校准域但仍在声明的适应包络内时，用有界漂移集合更新实际吸收新材料、几何、滑动和接触历史，并只允许满足包络鲁棒裕度的信息与性能动作。实时控制把 `SCAN` 阻抗和 `WITHDRAW` 动作放在同一个可行作用关系内，利用 warm-start RTI-SQP 寻找更优扫描阻抗，而在 infeasibility、模型失效或 deadline 到达时由同一关系按时返回预验证撤离动作。

这是一条单一因果链：

`history/action aliasing`
→ `decision-sufficient action-conditional contact operator`
→ `support-dependent uncertainty authority and online set adaptation`
→ `joint constrained normal–tangential impedance decision`
→ `one deadline-total applied-action relation`。

### 1.1 Method 核心与实现自由度

本轮将尚未闭合内容明确分成两类。

**属于 Method 本身、不可删除的结构**只有三项：

1. history 与候选 impedance 共同条件化的 decision-sufficient contact operator；否则第一条 RCA 的 contact-state aliasing 未被干预；
2. supported calibration 与 calibration-out adaptation 的 authority 分离，并且 adaptation 必须实际更新决策关系；否则校准外变化仍只是 detection/refusal；
3. scan performance、全部 hard constraints、computation outcome 和 withdrawal 属于同一个 applied-action relation；否则第二条 RCA 的 selection-feasibility decoupling 仍存在。

能量账本、finite-area margin、actuator/gain-rate checks 和 deadline reserve 是 accepted Target Constraints 对第 3 项施加的必要闭合条件，也不是为增加模块而添加的独立 Principle。

**主要属于后续实现和实验设计的自由度**包括：GRU 或 causal TCN、latent/adapter 的具体维数、椭球或 zonotope 外逼近、RTI-SQP 的线性代数实现、horizon、更新频率、support threshold、calibration sample size 和各类硬件接口。本文固定它们必须实现的数学接口、失败判据与证据要求，但不把某个网络宽度、求解器品牌或频率数字包装成 Method contribution。

## 2. 问题表述与控制接口

### 2.1 接触坐标与低层阻抗

在每个增益更新时刻建立接触坐标系

\[
R_{c,k}=[n_k,t_k,b_k],
\]

其中 (n_k) 是局部法向，(t_k) 是扫描切向，(b_k=n_k\times t_k)。扫描规划器提供切向路径与期望法向力；Method 不重新规划任务路径，而只决定正常扫描时的四维阻抗

\[
a_k=[k_{n,k},d_{n,k},k_{t,k},d_{t,k}]^\top .
\]

横向 (b) 与旋转方向由固定、经过系统辨识的低层阻抗保持；它们仍进入 wrench 和 actuator constraints。参数用有界 log-gain 表示以保证 (K_k,D_k\succ0)，并满足

\[
a_{\min}\le a_k\le a_{\max},\qquad
|a_k-a_{k-1}|\le \dot a_{\max}\Delta t_g .
\]

内环在较高频率实现

\[
\tau_k=J(q_k)^\top\!\left[f_k^d+K_k e_k+D_k\dot e_k\right]
+C(q_k,\dot q_k)+g(q_k),
\]

其中 (K_k,D_k) 由 (a_k) 在接触坐标系中组装后映射至任务空间；增益更新器只在固定的 (\Delta t_g) 周期边界发布新参数。

### 2.2 优化状态、结果和约束裕度

测量向量为

\[
y_k=[e^x_k,\dot e^x_k,f^c_k,m^c_k,n_k,t_k,
\sigma_k^{\rm slip},\sigma_k^{\rm contact},a_{k-1},T_k],
\]

包括位姿/速度误差、接触系六维力矩、滑移与接触指标、上一阻抗和能量账本 (T_k)。Method 预测的不是材料标签或完整本构参数，而是候选阻抗序列

\[
A_k=(a_{k|k},\ldots,a_{k+H-1|k})
\]

作用下的决策结果

\[
o_{k+i}=[e^f_n,e^x_t,f_n,f_t,m,
c^{\rm contact},c^{\rm slip},\Delta E,\tau^{\rm act}]_{k+i}.
\]

所有硬限制写成“正值为可行”的裕度 (g_j(o,a)\ge0)：

- 六维力/力矩上限；
- 最小接触力和最大压入/法向力；
- 切向滑移与有限接触面的 moment-capacity 裕度；
- actuator torque/velocity 及其 rate；
- gain magnitude/rate；
- 交互能量账本；
- 在当前更新 deadline 前可发布的完整动作。

## 3. Decision-sufficient adaptive soft-contact operator

### 3.1 可递归、可在线适应的参数化

用一个离线训练后冻结的 causal encoder 更新预测状态

\[
z_{k+1}=F_\psi(z_k,y_{k+1},a_k),\qquad z_k\in\mathbb R^{d_z}.
\]

(F_\psi) 可由小型 GRU 或 causal temporal-convolution 实现，但其语义由决策等价而非网络名称定义。给定 (z_k,A_k)，一个冻结的 nominal head 与低维线性适应 head 形成多步算子

\[
\hat o_{k+1:k+H}
=\bar o_\psi(z_k,A_k)+\Phi_\psi(z_k,A_k)\theta_k .
\]

(\theta_k\in\mathbb R^{d_\theta}) 只修正会移动 active constraints 或阻抗排序的 residual directions。连续结果使用异方差 head；接触保持和滑移使用事件概率/裕度 head。多步 rollout 在 latent space 内递归，因此未来观测不需要在求解时已知。

这个结构比在线更新整个网络更现实：高维特征保持固定计算图，在线只更新小矩阵或椭球；同时它比仅更新一个 Hunt–Crossley stiffness 更宽，能够用同一低维 residual 表示材料、几何、摩擦、速度和松弛历史对联合决策结果的局部改变。

### 3.2 离线数据与 teacher

离线数据必须覆盖声明的 nominal calibration population，并对 (a=(k_n,d_n,k_t,d_t)) 做受限激励。每条轨迹保存完整 history、实际 applied action 和上述 joint outcome。高信息 teacher 可来自高保真仿真、慢速离线系统辨识或稠密安全 bench replay；它只用于构造可行集和动作排序标签，不在部署时运行。

训练目标为

\[
\mathcal L(\psi)=
\mathcal L_{\rm multi-step}
+\lambda_g\,d_H(\widehat{\mathcal F}_\psi,
\mathcal F^{\rm teacher})
+\lambda_J[J(\hat A_\psi)-J(A^{\rm teacher})]_+
+\lambda_I I(z;h),
\]

其中第一项拟合多步联合结果，第二项惩罚 robust feasible-set 的 Hausdorff disagreement，第三项直接惩罚表示错误引起的 constrained decision regret，最后一项限制不必要的信息容量。嵌套状态消融从 (z) 中删除不改变可行集、active margin 或阻抗排序的坐标。

因此“decision-sufficient”有可检验定义：若两个 histories 映射到同一 (z)，则在声明动作类上，它们的 robust feasible sets 与最优动作值必须在预声明容差内等价。若 calibrated memoryless 或标量状态也满足该条件，history representation 的增量应被淘汰。

### 3.3 与 NNBO 的明确差异

NNBO 的对象是固定 trial 内的标量 Hunt–Crossley 参数，先通过 EWRLS 识别，再由 critic/HJB 关系生成期望力–位置关系。DASCO-TAC 同时改变三个因果位置：

1. **表示对象**：从标量法向本构参数变为 history/action-conditioned joint outcome and capacity operator；
2. **更新时间**：从先辨识、后控制变为每次接触更新后的低维 residual-set adaptation；
3. **决策关系**：从近似 unconstrained 单轴最优关系变为同时包含 normal/tangential performance、wrench/contact、energy、actuator、gain-rate、deadline 和 fallback 的 applied-action relation。

若只换复杂 contact model 而不做第 2、3 项，accepted 两条 RCA 链仍未关闭。

## 4. 初始校准、支持判定和校准外状态

### 4.1 轨迹级 simultaneous calibration tube

训练集和 calibration trajectories 按 episode 分开。对第 (r) 条 calibration trajectory 定义最大标准化过度乐观残差

\[
R_r=\max_{i\le H,j}
\frac{\hat g_{j,r,i}-g_{j,r,i}^{\rm obs}}
{s_{j,r,i}+\epsilon_s}.
\]

取独立 episode residual 的有限样本分位数 (q_{1-\alpha})，在支持域内使用

\[
g^{\rm cal}_{j,k+i}
=\hat g_{j,k+i}-q_{1-\alpha}s_{j,k+i}\ge0.
\]

使用 max score 是为了让一次校准直接对应“一个 horizon 内全部声明 constraints 同时成立”的 tube，而不是把若干边际区间误写成联合保证。其统计主张仍只限于 calibration population 的 episode-level marginal coverage，不宣称任意 context 的 conditional coverage。

### 4.2 Pre-outcome support event

支持判定针对候选 context–action pair (q=(z,a))，在 outcome 产生前完成。冻结 feature (\varphi(q)) 的 leverage score 为

\[
\ell(q)=\varphi(q)^\top
(G_{\rm cal}+\lambda I)^{-1}\varphi(q),
\]

并要求其 calibration neighborhood 至少包含预声明数量的独立 episodes。仅当

\[
\ell(q)\le\ell_{\max},\qquad N_{\rm epi}(q)\ge N_{\min}
\]

且 sequential residual audit 没有拒绝当前 calibration relation 时，(q) 才为 `SUPPORTED`。持续 innovation 或 constraint residual change 在本次 outcome 后触发下一更新的 calibration invalidation，不能倒改已经执行动作的支持标签。

### 4.3 三种 authority state

1. `SUPPORTED`：候选 pair 位于 calibration population，使用 (g^{\rm cal})；
2. `ADAPTING-IN-ENVELOPE`：校准关系不再适用，但状态、动作和可观测变化仍属于声明的适应包络，使用 adaptive envelope set；
3. `WITHDRAWAL-ONLY`：adaptive model class、集合一致性或物理包络已经不足以支持扫描，只允许同一 action relation 中的撤离动作。

状态 2 不是 unsupported detection 后拒绝服务：只要存在 envelope-feasible scan action，系统继续扫描、更新 (\theta)，并在有价值时选择可行的信息动作。

## 5. Calibration-out adaptation

### 5.1 带漂移的可行参数集合

适应包络显式声明可处理的材料刚度/松弛、有限接触面尺度、局部曲率、摩擦、扫描速度、载荷和传感噪声范围，以及 frozen feature 对这些变化的 residual representability bound。对每次更新先扩张旧集合以容纳有界漂移：

\[
\Theta_k^-=(\Theta_{k-1}\oplus\mathcal D_\rho)
\cap\Theta_{\rm env}.
\]

再用最新 action–outcome transition 做 set-membership 更新：

\[
\Theta_k=\{\theta\in\Theta_k^-:
o_k-\bar o_\psi(z_{k-1},a_{k-1})
-\Phi_\psi(z_{k-1},a_{k-1})\theta
\in\mathcal W_{\rm env}\}.
\]

部署时用固定维数椭球或 zonotope 外逼近 (\Theta_k)，以 bounded-cost rank-one update 实现。若集合交为空、innovation 连续超过 representability bound，或物理 monitor 越界，则本次状态直接进入 `WITHDRAWAL-ONLY`；不把扩大 covariance 冒充覆盖保证。

### 5.2 估计中心与信息价值

集合中心用 projected exponentially weighted RLS 更新：

\[
P_k^- = P_{k-1}/\lambda_k+Q_\rho,
\]

\[
K_k=P_k^-\phi_k
(\Sigma_\epsilon+\phi_k^\top P_k^-\phi_k)^{-1},
\]

\[
\hat\theta_k=\Pi_{\Theta_k}
\left[\hat\theta_{k-1}+K_k
(o_k-\hat o_k)\right].
\]

中心只用于 expected performance 和动作排序；hard constraints 对所有 (\theta\in\Theta_k) 成立。若不确定性会改变 active margin 或第一动作排序，可加入

\[
\mathcal I_k(a)=
\operatorname{tr}\!\left(S_k[P_k-P_{k+1}(a)]S_k^\top\right),
\]

其中 (S_k) 只选择当前 active constraint 与 top-action gap 相关方向。信息动作必须首先通过与性能动作完全相同的 hard relation；不存在 admissible informative action 时，系统不得为辨识而越界。

### 5.3 Decision recovery 与 calibration re-entry 分离

在线集合收缩可以先恢复可行集和动作排序，但这不自动恢复 initial conformal calibration。回到 `SUPPORTED` 需要一个不参与当前 (\theta_k) 更新的 delayed residual buffer，满足预声明 effective sample size、局部平稳/依赖审计，并重新通过同一 max-score one-sided calibration audit。此前所有动作始终使用 adaptive envelope authority。

## 6. Constrained normal–tangential impedance selection

### 6.1 同一 horizon objective

扫描模式的有限时域目标为

\[
J_{\rm scan}(A_k)=
\sum_{i=0}^{H-1}
\Big[
w_f(e^f_{n,k+i})^2
+w_x\|e^x_{t,k+i}\|^2
+w_l\rho^{\rm loss/slip}_{k+i}
+w_a\|a_{k+i}-a_{k+i-1}\|^2
\Big]
-\beta_k\mathcal I_k(a_{k|k}).
\]

(\beta_k=0) 当信息不会改变当前决策或不存在可行信息动作。权重在所有 baseline 与 Candidate 中固定；它们表达 accepted problem 的 force、trajectory 和 contact-loss/slip 联合性能，而不是事后调权制造优势。

### 6.2 支持域与适应域的 constraint tightening

`SUPPORTED` 候选满足全部 (g_j^{\rm cal}\ge0)。`ADAPTING-IN-ENVELOPE` 候选必须满足

\[
\underline g^{\rm env}_{j,k+i}(A_k)
=\min_{\theta\in\Theta_k,w\in\mathcal W_{\rm env}}
g_j\big(\hat o(z_k,A_k;\theta,w),a_{k+i}\big)
\ge0.
\]

RTI-SQP 中对 (g_j) 做局部线性化，并用 (\Theta_k) 与 (\mathcal W_{\rm env}) 的 support function 形成确定性 tightening；线性化 remainder 由预声明 trust region 和 remainder bound 覆盖。posterior center 可以改变目标，但绝不能缩小 (\Theta_k)、(mathcal W_{\rm env}) 或放松该 robust margin。

### 6.3 交互能量约束

正阻尼并不足以保证时变刚度不注入能量。对接触坐标各受控方向维护 energy tank

\[
T_{k+1}=T_k
+\eta\Delta t_g\dot e_k^\top D_k\dot e_k
-\frac12\sum_{r\in\{n,t\}}
[k_{r,k+1}-k_{r,k}]_+e_{r,k}^2
-E_k^{\rm loss},
\]

并强制 (T_{k+i}\ge T_{\min})。该式把 gain change 造成的可计算能量注入与执行动作放在同一个优化关系内；它只支持声明端口和 tank accounting 下的 energy budget，不被表述成任意环境下的全局稳定性证明。

### 6.4 Actuator、wrench 与有限接触约束

预测 wrench 经 (J^\top) 映射为 joint effort，直接检查 torque/velocity/rate。接触 head 输出正常接触裕度、切向 friction-capacity 与 normal-moment capacity，而不是把摩擦系数当作固定常数。有限接触面的 moment limit 和 relaxation history 只有在移动这些裕度或动作排序时才进入 (z_k)。

## 7. Deadline-total applied-action relation

### 7.1 混合 action，而不是优化器外 guard

控制输出定义为

\[
u_k=(m_k,a_k,r_k),\qquad
m_k\in\{\mathrm{SCAN},\mathrm{WITHDRAW}\}.
\]

在每个声明 operating state 中，候选集合都显式包含预验证撤离动作

\[
u_k^{\rm wd}=(\mathrm{WITHDRAW},a^{\rm wd},r_k^{\rm wd}).
\]

其切向参考速度置零，增益在 rate bounds 内转向 dissipative withdrawal setting，法向参考沿最后可信法向单调卸载，直到实测接触力低于 release threshold。撤离关系在单独声明的 withdrawal envelope 内离线验证 robot-side wrench、energy、actuator、gain-rate 和 deadline feasibility。

扫描解与撤离动作属于同一 feasible-action relation：

\[
\mathcal U_k^{\rm total}
=\{(\mathrm{SCAN},A):A\in\mathcal F_k^{\rm auth}\}
\cup\{u_k^{\rm wd}\}.
\]

目标对 withdrawal 施加有限但较大的任务中断代价，因此只要存在更优且已验证的 scan action 就继续扫描；否则同一 argmin relation 返回 withdrawal。它不是先选 performance action 再由外部 safety filter 投影。

### 7.2 Anytime RTI-SQP

每个 gain update 执行以下固定顺序：

1. 锁存测量、更新 (z_k,\Theta_k,T_k) 和 authority state；
2. 从常数时间表中注册当前 (u_k^{\rm wd})，由此先建立一个可发布动作；
3. shift 上一 scan sequence 作为 warm start，但在当前 authority 下重新验证第一动作；
4. 在剩余预算内执行 RTI-SQP/feasibility-restoration iterations；
5. 只有完成全部 robust margin、energy、actuator 和 gain-rate checks 的 iterate 才原子替换 best feasible scan record；
6. deadline 到达时，在 best feasible scan 与 (u_k^{\rm wd}) 中按同一 cost/relation 发布动作；若 model-set consistency、withdrawal envelope 或 scheduler contract 本身失败，则触发声明外的硬件急停，且 Method 的 totality claim 被判失败。

由此，R8 Candidate A 的 atomic feasible publishing 被保留为求解器实现细节；Candidate B 的完整高维 viability set 不再必要。总可行性来自“混合 action relation 在每个声明 state 中预先包含一个可执行动作”，不是依赖上一 horizon 永远可 shift，也不是依赖在线计算一个非空 controlled-invariant set。

## 8. 方法成立所需的分析

### 8.1 Decision sufficiency

若 encoder-equivalent histories 满足

\[
d_H(\mathcal F(h),\mathcal F(h'))\le\epsilon_F,
\qquad
\sup_{A\in\mathcal F}|J(h,A)-J(h',A)|\le\epsilon_J,
\]

并且 optimizer 对 feasible-set perturbation 局部 Lipschitz，则 representation-induced regret 可由 (2\epsilon_J+L_F\epsilon_F) 上界。该命题的关键不是网络拟合误差，而是 history compression 对可行集和动作选择的影响。

### 8.2 Adaptive-set validity

在真实 residual parameter 初始属于 (\Theta_{\rm env})、每步 drift 属于 (mathcal D_\rho)、measurement/model residual 属于 (mathcal W_{\rm env}) 的条件下，set expansion–intersection 归纳保持 (\theta_k^*\in\Theta_k)。因此满足 (\min_{\theta,w}g_j\ge0) 的 adapting action 满足声明模型中的 hard margins。集合交空或 residual-bound 失败必须使 claim 降级为 withdrawal，而不能继续宣称鲁棒适应。

### 8.3 Supported-set calibration

在 calibration episodes 与部署 supported episodes 满足所声明 exchangeability/dependence 条件时，max-score quantile 给出 horizon-level simultaneous marginal coverage。该性质不外推到 `ADAPTING-IN-ENVELOPE`；re-entry 只有在 delayed audit 成功后成立。

### 8.4 Energy accounting

当内环准确实现正定阻抗、tank 更新覆盖所有可控 gain-change energy 且 (T\ge T_{\min}) 时，Method 不从已声明端口注入超过账本的交互能量。未建模 reference work、采样延迟或执行器动态必须进入 (E^{\rm loss}) 或缩小 claim。

### 8.5 Deadline totality

若当前状态属于 withdrawal envelope、(u^{\rm wd}) 的 robot-side feasibility 已验证、lookup 和 publish 的 worst-case execution time 小于 deadline reserve，则任何 optimizer interruption 都仍返回 (mathcal U_k^{\rm total}) 中的动作。该性质独立于 RTI-SQP 是否收敛；优化失败只影响扫描性能。

## 9. 现实实现边界

- 低层 torque/impedance loop 与高层 gain optimizer 分时运行；建议 gain update 在 50–100 Hz，内环按机器人接口的最高稳定频率运行，但最终频率必须由 WCET 测量决定。
- (d_z,d_\theta,H) 由决策等价与 WCET 联合裁剪；不以更大网络作为贡献。
- normal 和 path-tangential 四参数是当前最小 action class；若 cross-tangent/rotation gain 对 held-out decisions 有不可忽略影响，必须扩展 action class，而不能把误差归咎于 predictor。
- 适应只针对 frozen feature 可表示、且 drift/residual bounds 覆盖的变化；新接触模式、黏附、破裂或超出 withdrawal envelope 的状态不在性能/安全声明内。
- 若包络鲁棒集合长期只剩 withdrawal，方法在该区域是安全降级而非问题已解决；这将反证所选 envelope 或 representation 的实用性。

## 10. Source Mechanism 与 Target adaptation

| 来源 | 实际迁移或借鉴 | Target 必需改造 | 未归给 Source 的主张 |
|---|---|---|---|
| VES / SPO | 用 downstream value/decision regret 决定表示保留的信息 | distortion 改为 contact feasible-set、active margin 与 impedance regret | contact calibration、在线适应和约束保证 |
| Predict-then-Calibrate | 独立 residual calibration 后再进入 robust decision | episode-level simultaneous constraint score；只在 supported population 生效 | calibration-out coverage、非线性闭环安全 |
| BOX-FDDP | warm-start、feasibility-restoration 和 one-iteration MPC precedent | atomic fully checked iterate 与预留 withdrawal action | arbitrary interruption totality |
| finite-area QLV/contact evidence | history 会同时移动 normal force、friction 与 moment capacity | 只把会改变 action/margin 的 memory coordinates 纳入 latent state | 在线辨识完整材料模型 |
| PSR / MPVIC | action-observation predictive state与 learned-model MPC 的实现启发 | 低维 contact residual set、在线 constrained information action、hard margins | PSR contact guarantee 或 MPVIC continual safe adaptation |

真正的跨域 Source migration 仍是 VES/SPO 的 decision-relevant representation intervention；其余来源严格保持 Evidence 所支持的局部作用。

## 11. Contributions

1. **Decision-sufficient adaptive soft-contact operator**：把 NNBO 的固定标量环境辨识改造成 history/action-conditioned joint outcome and capacity model，并以 feasible-set disagreement 与 constrained impedance regret 而非完整本构拟合定义表示充分性。
2. **Calibration-out set adaptation with explicit authority**：将 supported conformal tube、bounded-drift set-membership adaptation 和 delayed calibrated re-entry分开，使校准外但包络内的材料、几何、滑动与历史变化能够通过 admissible interaction 实际更新决策，而不是仅被检测或拒绝。
3. **Total-action normal–tangential impedance control**：把性能、信息价值、finite-area contact/wrench、energy、actuator、gain-rate、deadline 和 withdrawal 放入同一个 applied-action relation；实时求解可以改善扫描动作，但其收敛不再决定是否按时返回可执行动作。

## 12. 进入下一阶段所需的关键验证依据

以下是后续 Principle Test Design 必须产生的证据类型，不是已经完成的结果：

1. **Representation necessity**：在 matched sensing、objective、compute、data 和 safety budget 下，history/action state 相对 calibrated memoryless、NNBO scalar 和 full-reconstruction states 是否真正改变 feasible set、selected impedance 与 held-out joint outcome。
2. **Calibration与support validity**：supported population 的 simultaneous coverage/sharpness；pre-outcome support rule 的 false-support 与 false-rejection；不得用 adapting data 冒充原 calibration coverage。
3. **实际 calibration-out recovery**：四类 held-out axis 及其组合下，adaptive set 是否保持 true outcome/margin、是否存在 admissible excitation、decision regret 是否在变化失效前恢复。
4. **第二条 RCA 的因果证据**：同 predictor 下，joint total-action relation 是否相对 separated optimizer + post-hoc guards 改变 realized action、violations、fallback 与联合 performance。
5. **Energy与硬约束证据**：tank accounting、wrench/contact/moment、actuator 和 gain-rate 的 model/reality discrepancy，以及所有 adapting actions 对 authority set 的 membership。
6. **实时 totality**：WCET、强制 solver interruption、infeasibility、support switch、set inconsistency 和 withdrawal activation 时，每次 applied action 的 membership 与 deadline。
7. **整体问题闭合**：在校准外材料、几何、滑动条件和接触历史变化下，同时报告 force regulation、trajectory tracking、contact loss/slip 与所有 constraint outcomes，而不是只报告平均 tracking error。

若第 1 项显示 memoryless/scalar state decision-equivalent，应删除新表示；若第 3 项显示可行信息不足，应缩小适应主张；若 withdrawal 无法覆盖声明 operating states，第 7 节的 total relation 失败。Method 因而已具体到可直接形成测试设计，同时保留了可证伪边界。
