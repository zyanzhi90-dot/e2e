# Method Design Gap Audit

## 结论

当前 Method Design 需要**局部重构**，不是继续补几条提示词，也不需要重写 RCA、文献、Controller 或测试生命周期。应保留“问题失败 → 机制根因 → 跨域搜索 → 结构同构 → 方法迁移”主轴，但把 Human Gate 前的正式产物从 **Candidate Principle** 升级为 **Candidate Method Seed**。Candidate Principle 只能作为搜索中的中间对象，不能再作为可直接进入测试选择的终点。

当前系统能稳定保证 Scientific Validity 的基本条件，却没有在测试前强制 Novelty 和 Method Worthiness。因此它会可靠地产生“真实、可证伪、但抽象或平庸”的 C1/C2/C3/C4。

## 根因与证据

| 根因 | 证据 | 后果 |
|---|---|---|
| **G1. 产物类型错位：把 Solution Principle 当成 Method Seed** | `Phase I-确定方法论.txt` 和 `method-design-contract.md` 都把“算法无关 Principle”定义为候选终点；`idea-creator` 明令在 Principle 收敛前不得做 Method adaptation、minimal realization 或 composition。 | Human Gate 前只可能得到“应改变什么”的命题，得不到“靠什么新技术实体改变”的论文方法种子。 |
| **G2. 为防止过早工程化而过度禁止技术实体化** | Contract 把 architecture、optimizer、component bundle、implementation backbone 一并排除；真正的 target-domain adaptation 和 minimal faithful realization 被推迟到 `method_refinement`。 | 正确地排除了随意堆模块，也同时排除了新状态、算子、约束、更新律、控制律、优化分解等发明核。测试前无法判断候选是否可形成主贡献。 |
| **G3. Provisional Scientific Delta 只要求“诚实”，不要求“足够强”** | Phase I 明示“较弱或暂时不存在不构成否定”；Contract 也规定 weak delta 不机械 falsify Principle；独立 reviewer 对本次四个候选只判定 delta 边界诚实，仍给出 `PRINCIPLE_PACKET_READY`。 | “可能发现一个边界”“可能证明简单模型足够”即可通过，哪怕没有新的方法本体。 |
| **G4. Scientific Validity、Novelty、Method Worthiness 未形成三个独立判定** | 现有 method reviewer 检查因果闭合、算法无关、同构真实性、假设和预测；Candidate 级 novelty 仅有可选 advisory screen；正式 novelty Gate 在完整 refinement 之后；代码和规范中不存在 Method Worthiness 判据。 | 有效但已知、有效且新但太薄、以及足以成为论文主方法三类候选被混在一起，淘汰发生得过晚。 |
| **G5. 跨域迁移只验证“类比成立”，没有完成“构造性迁移”** | Schema 记录 source problem/root cause/intervention、结构映射和边界，却不要求保留源方法的发明核、给出 target substitution、失配补偿、目标域新技术对象或最近目标域先验差异。 | 真同构可以成立，但输出仍只是 slogan；源方法真实性被迁移了，源方法的技术实体性没有被迁移或重新发明。 |
| **G6. 工程实现强化了完整性，却不约束强度** | `validate_method_design_packet()` 明言只验证 machine-resolvable packet “without judging quality”；`provisional_scientific_delta`、`principle`、`intervention` 等只需非空。测试夹具用 `Principle A`、`intervention A`、`delta A` 即可通过。搜索 validator 只保证四维查询和 ID 绑定；Human view 只渲染一句 Principle、机制、delta、风险和差异。 | Harness 会稳定奖励字段齐全和链路闭合，不能拒绝平庸内容；查询数量和结构覆盖可替代真正的 seed 收敛。 |
| **G7. e2e 按 RCA 链切成四个候选，而没有合成一个贡献级发明核** | `METHOD_DESIGN_PACKET.json` 的四个候选分别对应 spatial、wrench、history、feedback 四条 causal chain；前三个是目标/坐标定义，第四个是复杂度选择元规则。仅保留一个跨域同构（PSR）。 | 候选彼此“机制不同”，但没有一个同时给出目标域核心对象、生成/估计方式、控制接口和新的能力闭环。Distinctness 不等于 contribution strength。 |
| **G8. 问题在 candidate-only 对齐前已存在** | 旧 `METHOD_ROUTES.jsonl` 更具体，但 Route A 是嵌套消融研究方案，Route B 是已知 contact model + viscoelastic state + wrench margin + robust MPC 的组件包，Route C 是 simple controller + shadow audit；其 novelty posture 也承认新颖性主要依赖实验后得到的边界。 | 仅把抽象 Principle 再具体化为路线仍不够；必须要求一个不可被“评测计划”或“已知模块集合”替代的中央技术核。 |

## 顶级/高引论文给出的反向基准

`source-materials/user-batch*` 中的代表性工作在实验前已经具备以下 Method Seed；实验用于验证它，而不是替它发明方法：

| 论文中的发明核 | 实验前已经成立的技术实体 | 非平凡性来源 |
|---|---|---|
| Hogan, *Impedance Control* | 在 interaction port 上直接控制 effort–flow 动态关系，并给出 workspace impedance 到 actuator torque 的实现关系。 | 从控制 position/force 改为控制二者之间的动力学关系，统一自由运动、约束和动态交互。 |
| Kang et al., *Accuracy/Robustness Dilemma* | IMC 注入目标阻抗并校正模型误差，TDE 估计和补偿机器人非线性动力学。 | 同时解除 impedance accuracy 与 model-error robustness 的结构性冲突。 |
| Buchli et al., *Learning Variable Impedance Control* | DMP 同时参数化参考轨迹和 gain schedule，PI² 用 rollout 的 path-integral 更新学习两者。 | 把高维 VIC 设计变成可在真实机器人上执行的无模型随机最优控制更新。 |
| 稳定 state-dependent VIC | 具有唯一全局极小值的神经能量函数编码可学习 stiffness，并由能量梯度形成稳定控制结构。 | 不再以牺牲 VIC 表达力换稳定性，而是把稳定性嵌入表示本身。 |
| 风险敏感 impedance optimization | 将状态估计误差/EKF 动力学并入 risk-sensitive 最优控制递推，直接输出轨迹和局部 feedback gains。 | 使 impedance schedule 显式依赖接触与测量不确定性，而不是人工调参。 |
| 非对称 impedance policy | 任务条件化的非对称 Cartesian impedance matrix，加上接触后的 residual reference modification。 | 打破 diagonal/symmetric action space 的能力上限，并同时缩小 RL 搜索空间以支持 sim-to-real。 |
| Deformable-contact-aware MPC | Hertz viscostatic contact dynamics、robot dynamics 和 contact constraints 进入统一 TO；以 ADMM 分解实现实时 MPC。 | 同时处理软接触真实性、力/运动联合规划和实时约束求解。 |
| Viscoelastic limit surface | 用时间演化的 contact area/pressure profile 构造随 relaxation/creep 演化的 friction-wrench limit surface。 | 将“材料有记忆”转成可改变抓取/滑移可行域的具体状态与几何对象。 |

这些种子的共同最低条件是：**一个目标域技术对象 + 一个明确作用机制 + 一个可执行的生成/更新/求解关系 + 一个难以由现有方法同时满足的冲突 + 一个对最近先验的实体级差异**。仅有新问题、新评价边界或“最小充分状态”目标，不足以构成强 Method Seed。

## 本次四个候选的具体缺口

- `SP-DECISION-SUFFICIENT-QUOTIENT` 是表示选择准则；没有定义 quotient 的可计算对象、学习/构造算子、保持的控制不变量或新控制接口。
- `SP-WRENCH-FEASIBILITY-MARGIN` 是待估计坐标；没有给出软体滑动超声中的 feasible set、margin dynamics、可观测估计器或如何约束 impedance action。它还丢失了源证据中六维 wrench cone 的具体 coupled bounds。
- `SP-PREDICTIVE-CONTACT-STATE` 的同构真实，但迁移后删掉了 PSR 的 core tests、observable operator、rank/closure 条件和学习机制，只留下“用未来预测压缩历史”。
- `SP-FEEDBACK-GATED-SIMPLICITY` 是模型选择/实验解释元规则，不是方法本体；除非它自身产生新的 formal boundary estimator 或在线 gating mechanism，否则只能是设计义务或基线原则。

因此，本次失败不是 RCA 不够深，也不是跨域论文不够强，而是发生了**抽象损失**：

`强源方法的技术核 → Solution Principle 抽取 → 结构映射 → 再次算法去除 → 目标域只有目的句`

## 目标状态

Human Gate 前每个候选必须是一张 Method Seed Card，并同时包含：

1. **Principle**：干预什么关系以及为何解除 RCA；
2. **Inventive technical kernel**：新的状态、表示、算子、控制律、约束、优化分解或测量机制；
3. **Minimal target embodiment**：目标实体、输入/输出、状态/动作接口，以及方程、伪代码或块图级操作关系；
4. **Constructive transfer**：源方法保留的 invariant、target substitution、失配、必要补偿，以及迁移后新对象；
5. **Nontriviality claim**：同时解决的冲突或现有方法无法联合满足的约束；
6. **Nearest-prior delta**：与目标域最接近方法在技术实体层面的差异；源方法可以不原创，迁移后的本体不能平庸；
7. **Minimality/core-support split**：一个中央发明核，支持机制仅用于关闭明确 residual gap；
8. **Pre-test predictions and kill conditions**：实验只决定该 seed 是否成立，不负责把它从抽象原则发明出来。

三个判定必须分开记录：

- **Scientific Validity**：因果链、证据、假设和可证伪性是否可信；
- **Novelty**：迁移后 target-domain technical kernel 是否被最近先验覆盖；
- **Method Worthiness**：即使真实且新颖，它是否改变可解问题、保证、能力边界或核心机制，足以承担论文主贡献，而非仅是调参、评测协议、边界描述或已知模块组合。

只有三者均达到 Seed-ready，才进入 Human 选择和 killer test。

## 必要修改（最小范围）

1. **重定义正式对象**：保留 Candidate Principle 作为内部搜索对象；正式 packet 和 Human Gate 改为 Candidate Method Seed。无需改动 RCA 和后续 test execution 生命周期。
2. **把 provisional target embodiment 前移**：在测试前允许并强制方程/伪代码/接口级最小实现；仍禁止完整工程、昂贵实验和无依据的 backbone commitment。将“算法无关”改为“不得用现成算法名冒充 Principle，但 Seed 必须有技术实体”。
3. **加入 Seed Construction 步骤**：`Principle → source kernel preservation → target substitution → mismatch compensation → inventive kernel → minimal embodiment`。如果只得到目标句或评测方案，返回 Principle Search。
4. **把同构记录升级为构造性迁移记录**：新增 source inventive kernel、preserved invariant、target substitutions、broken assumptions、compensation mechanism、emergent target kernel、target closest-prior delta；每个 serious transfer 必须绑定目标 RMC，而不是只绑定查询维度。
5. **增加强度硬门槛**：现有 reviewer 在 Validity 之外给出独立 Novelty 与 Method Worthiness 子判定；新增 `VALID_BUT_NOT_NOVEL`、`VALID_BUT_NOT_METHOD_WORTHY` 或等价返回原因。弱 delta 不必判“假”，但必须阻止进入测试。
6. **修改搜索闭合条件**：不得以四维非空、query 数量或“已搜索”闭合；至少要求目标域 closest-prior、每个保留迁移的完整源技术核、实体级差异和一个可构造 seed。无强 seed 时应返回扩大/重表述搜索，而不是提交四个抽象候选。
7. **修改 Human view**：每张卡必须显示 technical kernel、最小 embodiment、最近先验差异，以及 Validity / Novelty / Worthiness 三栏；当前一句 delta 的 deterministic view 不足以支持选择。
8. **同步最小工程面**：只需局部修改 `method-design-contract`、`idea-creator`、workflow artifact schema、validator、method reviewer rubric、renderer 和对应 tests；Controller 的 phase 顺序、Human approval、test-plan、evidence update、final refinement/novelty Gate 可保持不变。

当前阶段不需要立即引入 representation-first、formal-generalization、measurement-first 或 capability-composition 等其他 formation path；先把现有 failure/RCA + migration 路径改成能在测试前产出技术实体充分、目标域新颖且值得成为主贡献的 Method Seed。
