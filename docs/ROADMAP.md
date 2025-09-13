# 路线图（30/60/90 天）— 三层任务分解 · 第一代（精简而可运行）

> 原则：**一切为了道的践行；Bias for Action；少即是多。**  
> 风控语言统一用 **Theta / Gamma**。先跑通，再迭代第二代。

---

## L1 战略目标
- G0：建立一支 **纪律严明、奇正相合、自我进化** 的交易军队（合乎大道，百战不殆）。
- G1：在 30 天内完成「最小可行链路」：数据 → 回测 → 报告 → paper trading → 战报。

---

## L2 核心模块
- M1 立魂：军魂与军纪落地为制度  
- M2 计划与工程设计：系统边界与模块契约（**编码前完成**）  
- M3 数据后勤：行情/期权/IV/VIX 管线与质检  
- M4 回测训练营：最小回测框架（含成本/滑点占位）  
- M5 风控体系：Theta/Gamma 敞口边界、止损规则  
- M6 策略与研究：Alpha Canvas（直觉 × 数据）  
- M7 情报网络：人/平台/社区（多源互证）  
- M8 执行与模拟：先从 paper trading  
- M9 监控与复盘：指标、审计、战报  
- M10 开发体验：Cursor + GPT-5 流、CI 占位

---

## L3 任务清单（可直接建 Issue）

### M1 立魂
- [x] (M1-T1) 将《军魂与军纪》收录到仓库根目录并在 README 链接
  - DoD：仓库根有 `军魂与军纪.md`；README 有链接；提交信息含引用。
- [ ] (M1-T2) `RISK_POLICY.md`：仓位/止损/回撤上限、Theta/Gamma 边界
  - 子任务：
    - [ ] 定义组合层 **最大资金回撤**（如 10–15%）
    - [ ] 定义单策略 **Theta/Gamma** 最大敞口表（按资产/策略）
    - [ ] 定义直觉交易 **仓位 ≤ 10%** + 硬止损（点位或风险额）
  - DoD：文档落库，PR 过审；在回测报告模板中可引用阈值。
- [ ] (M1-T3) 「直觉在纪律框架内」规则
  - 子任务：
    - [ ] 模板化直觉假设的逻辑链（因→果→失效条件）
    - [ ] 规定 **试探仓位** / **加仓规则** / **撤退条件**
  - DoD：发布到 `RESEARCH_PLAYBOOK.md` 并在 Issue 模板引用。

---

### M2 计划与工程设计（编码前完成）
- [ ] (M2-T1) `ARCHITECTURE.md`：系统边界、模块图、数据流
  - 子任务：
    - [ ] 模块清单与职责（data/backtest/alpha/portfolio/risk/execution/reports）
    - [ ] 数据流示意（从抓取到报告）
    - [ ] 失败模式与回滚策略（数据缺失、适配器中断、报错）
  - DoD：架构图 + 文档合并；后续实现不得偏离。
- [ ] (M2-T2) `ADR-0001`：技术栈与非目标
  - 子任务：
    - [ ] 选择 Python 3.11、pandas、pyarrow、pytest 等
    - [ ] 明确非目标（高频毫米级延迟、复杂分布式存储）
  - DoD：ADR 合并，后续冲突需再开 ADR。
- [ ] (M2-T3) 模块契约：`src/*/interfaces.py`（仅接口/类型/协议，不写实现）
  - 子任务：
    - [ ] 定义 `DataSource`, `BacktestEngine`, `Alpha`, `RiskLimiter`, `Reporter` 协议
    - [ ] 为 each 接口添加最小 docstring 与输入/输出约定
  - DoD：mypy 通过；接口可被样例实现导入。
- [ ] (M2-T4) CI 初版：lint/pytest 占位
  - 子任务：
    - [ ] `pyproject.toml`（ruff/black/mypy/pytest）
    - [ ] GitHub Actions `ci.yml` 运行 lint/pytest（允许空测试）
  - DoD：PR 自动跑 CI；reports 目录被保存为 artifact（可为空）。

---

### M3 数据后勤
- [ ] (M3-T1) 数据源清单（最小可用）
  - 子任务：
    - [ ] 股票/指数行情、期权链、IV/VIX（任选一条免费或试用）
    - [ ] 列出获取频率、速率限制、可用历史范围
  - DoD：`docs/data-sources.md` 出台，含取数样例与速率限制。
- [ ] (M3-T2) 抓取脚本 & parquet 存储
  - 子任务：
    - [ ] `src/data/fetch_prices.py`、`fetch_options.py`
    - [ ] `data/schema.md`：时间戳/时区/复权/展期约定
  - DoD：能抓一段历史数据到 `data/*.parquet`；脚本有日志。
- [ ] (M3-T3) 简单质检
  - 子任务：
    - [ ] 缺失率、重复率、跳点、时区一致性检查
    - [ ] 生成 `reports/data-quality.md`
  - DoD：质检报告生成且在 CI artifact 中可见。

---

### M4 回测训练营
- [ ] (M4-T1) 最小回测脚本（双均线 + 成本/滑点占位）
  - 子任务：
    - [ ] `src/backtest/engine.py`：支持信号→订单→成交的简单流程
    - [ ] 成本/滑点用固定基点占位（后续再进化）
  - DoD：可对任一 symbol 生成逐日/逐分钟回测结果。
- [ ] (M4-T2) 报告生成（HTML）
  - 子任务：
    - [ ] `src/reports/generate.py` 输出 PnL、回撤、Sharpe
    - [ ] 报告中引用 `RISK_POLICY` 阈值并标记越界
  - DoD：`reports/backtest-YYYYMMDD.html` 产出并上传 CI artifact。
- [ ] (M4-T3) 可复现绑定
  - 子任务：
    - [ ] 报告页底部显示：代码 hash、数据快照 id、参数 seed
  - DoD：任意报告都能追溯到数据与代码版本。

---

### M5 风控体系（第一代）
- [ ] (M5-T1) 敞口聚合（Theta/Gamma）
  - 子任务：
    - [ ] `src/risk/limits.py`：按策略与组合聚合 Theta/Gamma
    - [ ] 超阈值打印警告/阻止下单（占位实现）
  - DoD：回测/模拟能看到阈值触发事件。
- [ ] (M5-T2) 止损规则
  - 子任务：
    - [ ] 单策略/组合：达到止损即平仓/降仓（简化为市价）
  - DoD：报告中能看到止损执行点。

---

### M6 策略与研究
- [ ] (M6-T1) `alpha-canvas` Issue 模板（直觉 × 数据）
  - 子任务：
    - [ ] 模板字段：逻辑链、失效条件、数据验证计划、风控需求、上线门槛/下架条件
  - DoD：新 alpha 一律以 Issue 形式提交。
- [ ] (M6-T2) 首批因子（2–3 个）
  - 子任务：
    - [ ] 趋势（如 MA 交叉）、波动率（如 HV/IV 信号）、跨期（可占位）
  - DoD：各因子都有回测报告与结论。
- [ ] (M6-T3) 1 个宏观直觉假设
  - 子任务：
    - [ ] 以 Canvas 形式提出；设定试探仓位与止损
  - DoD：形成文档与后续验证任务。

---

### M7 情报网络
- [ ] (M7-T1) 情报渠道清单
  - 子任务：
    - [ ] 人（名单/频率）、平台（Unusual Whales）、社区（Reddit）
  - DoD：`INTELLIGENCE_PLAYBOOK.md` 更新并落库。
- [ ] (M7-T2) 周报模板
  - 子任务：
    - [ ] `reports/intel-weekly-YYYYWW.md` 模板（Insights → Actions）
  - DoD：第一份情报周报产出。

---

### M8 执行与模拟
- [ ] (M8-T1) Interactive Broker接口打通
  - 子任务：
    - [ ] 通过api获取account，仓位等数据
    - [ ] 开发脚本，生成每日监控报告
- [ ] (M8-T2) Paper trading 占位实现
  - 子任务：
    - [ ] 模拟下单/成交/失败重试（简化）
    - [ ] 记录成交率/滑点/延迟
  - DoD：3 天模拟运行，产出执行摘要。

---

### M9 监控与复盘
- [ ] (M9-T1) 第一份战报
  - 子任务：
    - [ ] 绩效归因（策略/标的）、异常审计（越界/止损）
    - [ ] 下一步改进清单
  - DoD：`reports/after-action-YYYYMMDD.md` 产出。

---

### M10 开发体验
- [ ] (M10-T1) `pyproject.toml` + pre-commit（ruff/black/mypy）
- [ ] (M10-T2) Cursor 项目规则文件（约束/约定/常用命令）

---

## 里程碑（Milestones）
- **Day 7**：M1/M2 完成；M3 抓到数据；M4 跑出首个回测（哪怕简陋）。
- **Day 14**：报告可视化完成；风控占位（Theta/Gamma、止损）接入回测。
- **Day 21**：首批因子与宏观假设完成回测与结论；第一份情报周报产出。
- **Day 30**：完成 3 天 paper trading 与战报；确定第二代迭代清单。

---

## 备注
- 第二代（不在本路线图内）：TWAP/更真实滑点模型、细颗粒熔断、组合优化、经纪商适配器、自动化情报评分。
