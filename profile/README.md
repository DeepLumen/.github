# DeepLumen

## Roadmap
状态建议：⬜ 未开始 / 🟡 进行中 / 🟢 完成 / 🔴 阻塞

### 全局链路（模块在规划中的位置）
`LumenDatabase` → `LumenGym` → `LumenMetrics` → `LumenBacktest` →（`LumenPretrained` / `LumenAlpha` / `LumenRisk`）→ `LumenTrader`

> 说明：  
> - `LumenDatabase` 提供数据底座；  
> - `LumenGym` 提供训练/交互环境；  
> - `LumenMetrics` 提供评估与统计对比；  
> - `LumenBacktest` 将 Database/Gym/Metrics 组装成统一回测（历史/模拟/实盘回测）；  
> - `LumenPretrained`/`LumenAlpha`/`LumenRisk` 为模型层；  
> - `LumenTrader` 为实盘执行层。

---

### 模块清单（按职责分层）
#### 数据底座
- ⬜ `LumenDatabase`：量化数据库与数据流水线  
  - 职责：数据获取/抓取、存储、整理、导出  
  - 输出：结构化数据集（可回放/可复现）、统一导出接口

#### 环境与评估
- ⬜ `LumenGym`：基于 `LumenDatabase` 的量化深度强化学习环境  
  - 职责：将数据与交易规则封装为可交互训练环境  
  - 依赖：`LumenDatabase`

- ⬜ `LumenMetrics`：评估指标与统计对比报告生成  
  - 职责：将不同环境/回测产生的执行数据转化为指标与统计对比结果  
  - 输入：执行日志/轨迹/交易记录（由 Gym/Backtest/Trader 产生）

#### 回测框架（组装层）
- ⬜ `LumenBacktest`：统一回测框架（历史回测 + 实盘/模拟盘回测）  
  - 职责：整合 `LumenDatabase`、`LumenGym`、`LumenMetrics`，形成可复现的端到端回测闭环  
  - 依赖：`LumenDatabase`、`LumenGym`、`LumenMetrics`

#### 模型层
- ⬜ `LumenPretrained`：预训练模型  
  - 职责：提供可复用的基础表征/初始化（供策略/风险等下游使用）

- ⬜ `LumenAlpha`：策略模型（Alpha/Policy）  
  - 职责：生成策略信号/动作（训练与评估依赖环境与指标）

- ⬜ `LumenRisk`：风险模型  
  - 职责：风险度量、约束与风控信号（训练与评估依赖指标体系）

#### 执行层
- ⬜ `LumenTrader`：实盘交易执行  
  - 职责：执行交易、产生真实执行数据  
  - 输出：实盘/模拟盘执行数据（供 `LumenMetrics` 与 `LumenBacktest` 回放/评估）
