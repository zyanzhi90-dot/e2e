# Impedance Control E2E 修改日志

## 2026-08-12：来源政策与调研范围修订

1. 新建本独立修改日志，用于逐项记录阻抗控制领域调研 E2E 的人工修订；不再把本轮修改混入 Harness 模块记录。
2. 修订 `idea-stage/SOURCE_ADMISSION_POLICY.yaml`：
   - 将领域名称从泛化的机器人阻抗、导纳与交互控制收敛为机器人阻抗控制；
   - 删除属于证据审查重点的 `risk_rules`，仅保留来源资格、准入轨道、检索路由、来源类型、语言和准入边界；
   - 明确 teleoperation/haptics、legged/whole-body 与泛交互控制文献只有在直接推进或解释阻抗控制机制时才可准入。
3. 修订 `RESEARCH_BRIEF.md`：
   - 以阻抗控制方法自身的发展脉络为唯一主线；
   - 将预列技术阶段降为可由实际文献证据修正、增删、合并或重组的初始假设；
   - 将 teleoperation/haptics、legged/whole-body 等降为条件性证据，不再作为独立调研分支；
   - 要求逐阶段梳理核心机制、所解决问题、关键假设、适用与失效条件及遗留瓶颈，并覆盖经典阻抗控制、学习型阻抗控制及其后续发展。

本轮未修改 Harness 代码、工作流、Gate、Hook、State、Artifact、Validator、Reviewer 或其他模块。

## 2026-08-12：E2E-1 CLI Unicode 输出修复

1. 首个真实查询已由 `serpapi_google_scholar` 成功完成，Controller 写入 Q0001 与 20 条候选文献；随后 CLI 在 Windows GBK 输出流打印包含 en dash 的 JSON 时抛出 `UnicodeEncodeError`，导致成功命令错误返回退出码 1。
2. 仅修订 `arisctl/__main__.py` 的输出边界：优先向底层字节流写入 UTF-8；无 `.buffer` 的测试捕获流继续使用普通文本写入。
3. 新增 `tests/test_aris_cli_output.py`，用 GBK 文本流和不可编码字符验证输出仍是可解析的 UTF-8 JSON。
4. 未修改 Controller、provider cascade、query ledger、Gate、State、Artifact、Validator 或 Reviewer；没有重放已经完成的 Q0001。

## 2026-08-12：E2E-2 全文 fallback 过早阻塞修复

1. 两篇开放全文已成功生成完整 read event；第三篇自动全文失败后，旧逻辑立即进入 `human_fulltext_batch`，错误要求重新提供全部 14 篇，包括已完成的两篇，并阻止继续尝试其他论文的自动路由。
2. `arisctl/controller.py` 现在把 completed read event 视为已取得全文；当仍有未尝试论文时，单篇 provider 失败只记录 `FULLTEXT_PROVIDER_UNAVAILABLE` 并继续 `PAPER_READING`。
3. 自动尝试耗尽后，`finish_reading` 才复用原有单次批量 fallback，且只列出真正失败、仍缺全文的论文。所有已准入论文仍必须有 accepted Evidence Card，未降低科研约束。
4. 新增定向回归，连同既有单篇 fallback、零证据阻断和 CLI Unicode 测试共 `4 passed`。
5. live E2E 只撤销旧逻辑生成的过早批次请求并恢复 `PAPER_READING`；三次读取预算、两次成功事件、一次 AQ 失败、账本和准入记录均完整保留。

## 2026-08-12：全文获取策略与批量下载清单

1. 按用户指示停止逐个探测 IEEE、ASME 及其他受限出版商页面；后续自动获取限于 arXiv 和已实际成功取得的公开全文，不再为付费墙逐站消耗检索轮次。
2. 新增 `FULLTEXT_DOWNLOAD_BATCH.md`，单独列出需人工下载的论文 ID、题名、DOI 和固定保存文件名；新增 `HUMAN_FULLTEXT_BATCH_TEMPLATE.json`，供下载完成后一次性注册全部文件。
3. 已取得的 PDF 经过结构检查、首页目视检查和文本抽取。`LJMBf6MAPHQJ`、`dqYs440MKMMJ`、`AQhmZpqu8cQJ`、`ZUU9058b5_oJ` 的内容身份一致，无需重复下载。
4. `kg4YFrTouZMJ` 的公开 PDF 实际是同题同作者的 IJRR 版本，与当前准入记录的 Springer DOI 不同；它不进入 Evidence Card，并加入人工批次以取得与准入身份一致的版本。

## 2026-08-12：E2E-3 Crossref 同题记录消歧修复

1. 实际 PDF 核验发现 Crossref 可同时返回同题同作者的期刊版和书籍章节版；旧实现只取首个完全同题结果，可能把论文身份、DOI 与后续全文证据错误串接。
2. `arisctl/gateways.py` 在存在多个完全同题结果时，使用检索记录已有的 DOI、期刊、年份和作者姓氏做确定性排序；若仍并列则 `verify_failed`，不猜测身份。单一完全同题结果的原行为不变。
3. 新增正确选中期刊版本及无法消歧时拒绝核验的回归；research-lit gateways、Controller 与 CLI 共 `91 passed`。
4. 未新增或削弱来源政策、Gate、State、Artifact、Validator、Reviewer 或人工检查点。

## 2026-08-12：E2E-4 准入后撤回与用户范围纠正

1. 用户明确要求移除 `kg4YFrTouZMJ`。旧 Controller 在 `PAPER_READING` 后没有准入撤回路径，直接略过会使 `finish_reading` 永久等待该文献。
2. 新增 Controller 非推进纠正 `withdraw_admission`：仅在全文阅读或待人工全文批次阶段、且 Evidence Card 尚未接受时可执行；结果固定为 `EXCLUDE_USER_WITHDRAWN`，理由与时间写入 Corpus 和 Ledger。
3. 若撤回项位于待下载批次，则从 live request 中删除；若是最后一项则恢复 `PAPER_READING`。检索、失败读取和预算历史不删除。
4. 该纠正由 Controller 根据 live stage 动态暴露，不改变 `idea-workflow.yaml`，因此不会触发正式 run 的 workflow hash 迁移或破坏既有执行记录。
5. `kg4YFrTouZMJ` 已正式撤回，无需下载，不进入 Evidence Registry。

## 2026-08-12：E2E-5 已准入文献身份纠正

1. 本地全文核验确认 `YroEWEBohvsJ` 是最初 Scholar 发现的 1998 IEEE T-RA 期刊版，DOI `10.1109/70.678454`；旧 verifier 曾错误绑定 1995 ICRA 同题版本。
2. 新增 `reverify_admission`：仅在 Evidence Card 接受前重新调用正式身份 verifier，成功时保存 previous/corrected 元数据快照并写 Ledger；核验失败不改变 live state。
3. 两次 Crossref 官方接口调用均发生网络错误，Controller 已 fail closed，当前 live 身份尚未纠正，后续服务恢复后重试。
4. 用户下载的 Hogan 文件经全文首页与正文核验，实际是 IEEE 会议论文，不是 ASME Part II DOI `10.1115/1.3140713`；因此没有错误注册。目前真正需要补下载的只剩这一篇。
5. 更新 `FULLTEXT_DOWNLOAD_BATCH.md` 和 `HUMAN_FULLTEXT_BATCH_TEMPLATE.json`，使用用户实际文件名并移除撤回项。公共 research-lit 回归 `94 passed`。

## 2026-08-12：用户接受同题同作者版本并继续

1. 用户明确决定：本批次中题名和作者一致即可进入下一步，不再因 DOI、期刊/会议或系列版本差异阻塞。
2. Hogan 文件按实际 IEEE 会议版本注册；Cheah/Wang 文件按实际 1998 IEEE T-RA 期刊版本注册。两者的 Evidence Card 必须写明实际读取版本，并且只能提取该文件中真实出现的机制、实验与结论，不能把另一版本的内容归入当前证据。
3. `HUMAN_FULLTEXT_BATCH_TEMPLATE.json` 已改为用户实际文件路径，批次达到 9/9；`kg4YFrTouZMJ` 仍保持正式撤回。
4. 本项是用户对具体全文版本可接受性的关键科研决定，不修改 Harness Gate、State、Validator、Reviewer 或来源政策。

## 2026-08-12：E2E-6 paper_reader Hook Unicode 边界修复

1. 首张正式 Evidence Card 在 Windows 下暴露 Hook 输入按系统 locale 解码、Unicode 项目路径写回机器回执时可能失败的问题。
2. Hook 改为从 `stdin.buffer` 按 UTF-8（兼容 BOM）解码，并以 ASCII escape 的 UTF-8 JSON 写机器回执；Evidence Card 的 canonical JSON 与内容 SHA-256 语义不变。
3. 定向 Hook 回归通过；13 张 Evidence Card 后续均经过相同 Hook、read-event/hash binding、Validator 与 Controller 验收。

## 2026-08-12：E2E-7 重复检索不得回滚正式论文状态

1. Coverage Reviewer 要求 exact-title 补检索时，查询结果重复命中 6 篇已有 Evidence Card 的论文。旧 `execute_query` 用 discovery row 整行覆盖，导致 `identity_status=verified`、`ADMIT_DECISION_GRADE` 和核验 provenance 回滚为 `verify_pending`、`DISCOVERY_METADATA_ONLY`，而 Evidence Artifact 仍保留，形成 Paper/Artifact 语义冲突。
2. 自动与人工 metadata merge 现在只刷新引用计数、cited-by、snippet 和 discovery provider，并合并 `found_by_query_ids`；一旦已有 verified identity 或正式 admission/withdrawal 决定，新发现记录不得覆盖正式字段。
3. 对修复前已经发生的 live 冲突，`finish_retrieval` 仅在 accepted Evidence 存在、且正式 Corpus 中能找到同一 paper 的 `verified + ADMIT_DECISION_GRADE` 快照时恢复；找不到正式快照则 fail closed。恢复会写 Corpus 和 Ledger 事件。
4. 6 篇 live 记录均已恢复为 `verified + ADMIT_DECISION_GRADE`。该修复未新增 Gate、Reviewer、科研评分规则或准入旁路。
5. research-lit gateways、CLI 与 Controller 受影响范围回归：`97 passed in 125.41s`。

## 2026-08-12：Coverage Review Cycle 2 补充全文批次

1. 初版 13-card Field Map 通过结构 Validator，但独立 Coverage Reviewer 正式给出 `CONTINUE`：缺少 classic online adaptive/optimal impedance、2011 early learning VIC 和 online passivity preservation 的 primary evidence，可能改变方法 taxonomy。
2. 第二轮仅执行 4 条 exact-title 查询。准入 direct adaptive、Learning VIC、passivity preservation 和高引用 uncertain-environment dynamic force tracking；2014 optimal-interaction 论文因未达到既定主门槛保留 discovery-only，没有滥用 exception。
3. 自动开放全文路由均失败，Controller 一次性请求 4 篇。新增 `SUPPLEMENTAL_FULLTEXT_DOWNLOAD_BATCH.md` 和 `SUPPLEMENTAL_FULLTEXT_BATCH_TEMPLATE.json`；不逐页探测付费出版商。

## 2026-08-13：E2E-8 已发现论文的后续用户全文升级

1. 第二轮补充检索中，`Impedance adaptation for optimal robot–environment interaction` 与 2021 IRL 论文已存在于 Corpus，但原 Controller 只能在元数据检索阶段注册“全新”的用户来源，也只能回执待下载批次中精确列出的四篇，无法把用户后来补交的已知论文正式转入 `USER_SUPPLIED_READ`。
2. 新增非推进动作 `promote_user_source`：仅允许在全文阅读或待全文批次期间，对已知 discovery-only、尚无 accepted Evidence Card 的论文执行；文件必须位于 `source-materials/`，必须给出理由并具有 verified identity。原 discovery provider 与 query IDs 保留，本地路径、SHA-256、身份来源和升级理由写入 Corpus/Ledger。
3. 不改变高引/顶会准入阈值，不扩大三类 decision-grade exception，不新增 Gate、Reviewer、State 或 Evidence 旁路。论文仍须依次经过 read event、`paper_reader`、Hook、Validator 和 Controller。
4. 四篇待下载论文已按原批次一次性回执；2014 optimal interaction 与 2021 inverse-RL VIC 按用户明确提供材料升级。2021 论文在 Crossref 网络失败时先 fail closed，随后依据本地 PDF 首页的题名、四位作者、期刊、年份与 DOI 完成离线身份核验。
5. 六篇均已建立完整 read event 与全文哈希。早期 direct adaptive 的实际所读版本为同作者 1991 IEEE CDC 六页论文，Evidence 明确限定该版本，不迁移未读 1993 期刊版细节。
6. 共享 research-lit gateways、CLI、Controller 回归 `99 passed`；本修改继续单独记录于本 Markdown 日志。

## 2026-08-13：补充 Evidence、Field Map 与第二轮 Coverage Review

1. 六篇补充全文均逐篇经过 `paper_reader → Hook → read-event/SHA binding → Validator → Controller`，总计形成 19 张 accepted decision-grade Evidence Cards。
2. 更新 Field Map 为 9 个方法家族。新增 `M9_ADAPTIVE_OPTIMAL_IMPEDANCE_SELECTION`，以“是否改变目标阻抗本身”区分 fixed-target direct adaptive realization、force-error gain adaptation 与 cost-optimal target selection；M6 补入 online PPC，M7 补入 2011 PI² 和 2021 AIRL reward recovery。
3. 2021 AIRL 的 transfer 被限定为：固定 learned reward 后，在同一任务的改变设置中重新执行 forward RL；不表述为跨任务或 zero-shot transfer。2018 adaptive VIC 被限定为零虚拟刚度、固定质量、标量阻尼适应及近似标量稳定条件。
4. Field Map Validator 通过，19/19 Evidence IDs 全部解析。第二轮独立 Coverage Reviewer 正式给出 `CANDIDATE_SUFFICIENT`，确认上一轮 classic adaptive/optimal、2011 early VIC、online passivity preservation 三类缺口均由 primary evidence 实质关闭，且不存在会改变顶层 taxonomy 的新 material gap。
5. Controller 已接受 verdict，landscape 状态为 `accepted / SUFFICIENT`，当前停止于必要的 `scope_human_approval` Gate。

## 2026-08-13：E2E-9 Corpus 旧回执污染与窄修复

1. 第二轮 coverage verdict 首次提交时，最终审计在 Corpus 第 340 行 fail closed。根因是 E2E-7 从正式快照恢复论文时复制了旧 `record_sha256`，旧 writer 又把它作为普通语义字段参与新记录哈希；共影响连续 6 条 reconciliation rows。
2. `append_jsonl` 现在在写入边界剥离来访的 `previous_record_sha256` 与 `record_sha256`；reconciliation 也不再把旧机器回执写入 live paper state。
3. 新增的恢复只接受可证明的旧缺陷签名：错误哈希必须能由“同一论文更早的 record hash 被嵌入后”唯一复算；其他损坏、断链或未知异常仍拒绝。修复前 Corpus 按 SHA-256 归档，随后仅原子重建机器链字段，并在 Ledger 写明前后哈希和行号。
4. Live Corpus 的第 340-345 行精确匹配该签名；原 353 行文件归档于 `.aris/repair-history/`，重建后全局 coverage audit 通过。没有改变任何论文语义、Evidence、Field Map 或 reviewer 判断。
5. 最终全量回归：`622 passed, 66 skipped, 4 subtests passed`；`compileall` 与 scoped `git diff --check` 通过。

## 2026-08-13：删除来源政策中残留的 `risk_rules`

1. Harness 的来源政策 Validator、canonical template 和本项目已批准的正式政策均不要求 `risk_rules`，后续 Controller 流程也不消费该字段；残留要求只存在于共享说明和旧候选政策中。
2. 删除共享来源政策说明中的强制要求，并从 `SOURCE_POLICY_CANDIDATE.yaml` 删除残留 `risk_rules` 段。正式已批准政策未变，因此不需要重新批准、迁移哈希或改写运行状态。
3. 未增加针对该删除项的负向回归断言；现有主版本/Codex 镜像一致性检查已足够。既有定向测试 `21 passed`，`compileall` 通过。
4. 按用户最新决定，不修改 Codex 项目级审批配置，也不修改当前 `paper_reader` 的启动或审批行为；Gate、Hook、State、Artifact、Validator、Reviewer 和证据规则均未变化。

## 2026-08-13：全文获取收敛为 arXiv 自动下载与非 arXiv 批量交接

1. canonical `fetch-fulltext` 现在只调用已声明 arXiv 身份的 PDF 下载器，不再对非 arXiv 文献逐篇尝试 Crossref、OpenAlex、Semantic Scholar 或网页 PDF 路线。
2. research-lit 主流程先对未读准入文献分组：arXiv 文献自动下载；所有非 arXiv 文献通过既有 `defer-fulltext-batch` 一次性登记，不执行额外全文探测。
3. arXiv Evidence Cards 完成后只调用一次 `finish-reading`，Controller 复用原有 `HUMAN_SEARCH_REQUIRED` 批次，将全部非 arXiv 文献和直接下载失败的 arXiv 文献统一列出；用户下载到 `source-materials/` 后一次性提交 manifest。
4. 未新增 Gate、State、Artifact、Validator、Reviewer、回执或模块；`paper_reader` 与 Codex 审批配置未改变。相关 Gateway、CLI、Controller 和镜像回归 `132 passed`，`compileall` 与 scoped diff 检查通过。

## 2026-08-14：迁移后进度核对与两篇全文豁免

1. 只做 live Controller 状态和下一转移可执行性检查；未重新检索、阅读或改动 Harness 代码。
2. `5mglSkdhyw4J` 题名和作者与已读且已接受的 Hogan IEEE 会议版 Evidence（`kmOXrypch9kJ`）一致；根据用户明确决定，Controller 将其记为 `EXCLUDE_DUPLICATE`，不再下载或重读。
3. `sSS-zzms-yMJ` *Cartesian Impedance Control of Redundant and Flexible-Joint Robots* 是较旧的 Springer Tracts 长篇专著，不属于当前政策列明的期刊/会议主动准入类型，且用户明确不要求；Controller 将其记为 `EXCLUDE_IRRELEVANT`。
4. 迁移后 live stage 实际为 `METADATA_RETRIEVAL`，不是预期的待下载 `HUMAN_SEARCH_REQUIRED`。31 个 Query Plan item 虽都能按 `plan_item_id` 匹配历史 query event（28 `complete`、3 `failed`），但重提交的 plan 将它们全部重置为 `planned`；`finish-retrieval` 因此按设计 fail closed。
5. 旧 `FULLTEXT_DOWNLOAD_BATCH_V2.md` 不再是可执行的 live Gate 清单：其仍含上述两篇，且与三个 live `ADMIT_FOR_READING` 记录不一致。在 Controller 完成 query-plan/event 状态对齐并重新发出正式全文批次前，不应使用该文件提交下载回执。

## 2026-08-14：新全文批次已下载与跨聊天 Handoff

1. 用户已将新批次 PDF 放入 `source-materials/user-batch-v2/`。按 live admission 和标题/文件名核对后，当前活跃全文集与本地文件均为 61；未尚未注册 read event 或 Evidence Card。
2. 按用户明确决定，`amoExo-Mk8oJ` 与 `auEqC5QRdLQJ` 降为 `ADMIT_DISCOVERY_ONLY`，`dnPmUKACMDYJ` 退出活跃全文集。Hogan 继续复用 `kmOXrypch9kJ`，Ott 长篇专著继续排除。
3. 将修订下载清单中本就不属于全文下载的三条迁移残留（`AS4dK5P2ShQJ`、`k555IwSorykJ`、`6EZvx0epOzMJ`）退出活跃阅读，但保留 Corpus/Ledger 发现历史。
4. 新建 `HANDOFF_2026-08-14_FULLTEXT_DOWNLOADED.md`，记录完整范围、来源政策、两轮旧调研与 Coverage Review、21 张已接受 Evidence、61 份待正式注册全文、所有绑定用户决定、迁移状态问题与新聊天的精确续接顺序。
5. 本次未启动新论文阅读、未重新检索、未修改 Harness 代码；正式 run 仍停在待 Controller 对齐 Query Plan/event 状态的 `METADATA_RETRIEVAL`。

## 2026-08-15：native generic reader/reviewer compatibility

1. 同步 ARIS-owned native lifecycle Hook 与项目运行说明：当前 active Codex turn 内优先使用 configured `paper_reader` / `coverage_reviewer`；runtime 无法选择 custom role 时，仅这两个角色可使用 `fork_turns = none` 的 native generic compatibility path。
2. compatibility receipt 如实记录 `dispatch_mode = native_generic_compat`、runtime/child identity、transcript hash、task binding 与实际 tools；不把 generic child 伪装为 loaded configured profile。
3. reader 必须绑定 live `paper_id`、`read_event_id`、`content_sha256`，且 transcript 中任一 tool call 均 fail closed；coverage reviewer 必须绑定 live `run_id`、`review_request_id`、artifact hashes，仅允许既有 contract 明示的 WebSearch capability。
4. 未通过 nested `codex exec`、新 CLI session 或新 top-level turn 运行任何 reader/reviewer；未创建 Evidence、Review、receipt 或改变本项目 formal state。
