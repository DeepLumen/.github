# Lumen Data Engine (LDE)

Lumen Data Engine（简称 LDE）把同一套数据处理能力做成四种“使用方式”，方便不同场景的开发者直接接入：

- **Rust 库**：给 Rust 项目当依赖用
- **CLI 工具**：在终端里跑任务、做批处理、在 CI 里调用
- **Python 包**：让 Python 脚本/Notebook 直接调用
- **HTTP 服务**：用接口调用，适合跨语言/跨系统集成

核心原则很简单：**功能只实现一份**，其它形式只是不同的“入口”。

---

## 这项目适合什么场景

- 你有一套数据处理/计算能力，既要给服务端用，也要给脚本用，还要能命令行跑。
- 你不想维护四份逻辑（CLI 一份、HTTP 一份、Python 一份、库一份），希望行为一致、升级不痛。

---

## 项目怎么组织（多仓库）

LDE 按“交付方式”拆成多个 GitHub 仓库。你可以只用其中一个，也可以组合使用。

### 1) 核心库：所有能力的来源
**Repo：`lumen-data-engine-core-rs`**

- 这里放真正的业务逻辑/算法/规则
- CLI / HTTP / Python 都会调用它
- 如果你是 Rust 项目集成方，通常只需要这个仓库（或 crates.io 上的包）

### 2) 契约：让不同入口保持一致
**Repo：`lumen-data-engine-contract`**

- 这里放“对外长什么样”的统一定义，比如：
  - 请求/响应字段（HTTP 返回什么结构、字段名是什么）
  - 错误码（失败时用什么 code，含义是什么）
  - OpenAPI 文档（如果提供 HTTP 服务）
- 目的：避免 CLI/HTTP/Python 各说各话

> 你把它理解为“统一的接口说明书”。

### 3) HTTP 服务：把核心能力变成 API
**Repo：`lumen-data-engine-service`**

- 把 HTTP 请求转换成 core 的输入，然后调用 core，再把结果转成 HTTP 响应
- 这里也会包含服务运行需要的东西：配置、日志、健康检查、部署相关文件等

### 4) CLI：终端工具
**Repo：`lumen-data-engine-cli`**

- 负责参数解析、输出格式（文本/JSON）、退出码
- 实际做事还是调用 core
- 适合本地跑、批处理跑、CI 里跑

### 5) Python：给 Python 用户用的包
**Repo：`lumen-data-engine-python-sdk`**

- 把 Python 的参数/返回值转换成 core 的输入/输出
- 把 core 的错误转换成 Python 异常
- 负责打包发布（pip 安装、各平台 wheel 等）

---

## 一句话看懂调用链路

不管你用哪种方式，最终都是：

- CLI → core  
- HTTP service → core  
- Python → core  
- 其他 Rust 项目 → core  

只有 core 负责“真正做事”。

---

## 开发者从哪里开始

按你的使用方式选一个入口即可：

- 想在 Rust 项目里用：看 `core-rs`
- 想命令行跑：看 `cli`
- 想提供/部署 API：看 `service`
- 想在 Python 里用：看 `python-sdk`

如果你要改功能，**优先改 `core-rs`**，然后在入口仓库里补适配（如果需要）。

---

## 版本与兼容性（简要）

- **core**：对外 API 稳定后会按语义化版本管理（重大变更升大版本）
- **contract**：字段名、错误码等尽量稳定；有破坏性变更会升 API 版本（例如 `/v2`）
- **cli/service/python**：跟随 core/contract 更新，确保行为一致

---

## 仓库清单（占位链接）

把 `<ORG>` 替换成你的 GitHub 组织或用户名：

- Core: https://github.com/<ORG>/lumen-data-engine-core-rs
- Contract: https://github.com/<ORG>/lumen-data-engine-contract
- Service: https://github.com/<ORG>/lumen-data-engine-service
- CLI: https://github.com/<ORG>/lumen-data-engine-cli
- Python SDK: https://github.com/<ORG>/lumen-data-engine-python-sdk
