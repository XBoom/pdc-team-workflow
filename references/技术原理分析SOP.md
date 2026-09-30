# 技术原理分析 SOP · 三级拆解 playbook（v1）

> **本文件是「深度分析四准则 · 准则 2 · 原理 → 方案详细设计 → 可落地」的可执行 SOP**。
> 当分析组 B（或撰写组 B）需要对调研组已锁定的某个机制做"原理性"展开时，按本 playbook 执行。


---

## 一、为什么需要这个 SOP

### SKILL.md §四·准则 2 的问题

当前 SKILL.md §四·准则 2 只写了抽象三级（原理 → 设计 → 落地），但**没有回答三个实操问题**：
1. **怎么拆**：每个机制都拆三级，会不会把简单机制写复杂了？什么时候用二级、什么时候用三级？
2. **每级多少字**：原理层写 50 字和写 500 字，区别是什么？
3. **怎么避免写成"概述"**：调研员最容易踩的坑 —— 把原理层写成"X 是一个 Y，它支持 Z"这种产品介绍。

### 本 SOP 的目标

把"原理 → 设计 → 落地"从**抽象原则**变成**可裁剪、可验收、可复用**的工作流：
- **可裁剪**：调研员知道"这个机制用三级 / 二级 / 一级"
- **可验收**：C 闸门能用本 SOP 判定 B 写得"够不够原理"
- **可复用**：每级都有模板 + 反例 + 实战案例，可直接复制粘贴

---

## 二、三级拆解 SOP（核心）

### 2.1 何时拆三级 / 二级 / 一级

| 复杂度 | 拆解级别 | 适用场景 |
|---|---|---|
| **L3 · 复杂机制** | 三级必拆（原理 + 设计 + 落地）| 选型核心要慎重的机制（如 Provider 路由、Checkpoint、流式输出）|
| **L2 · 中等机制** | 二级（设计 + 落地）| 选型较明朗，只需落地细节（如 ORM 配置、认证集成）|
| **L1 · 简单机制** | 一级（落地即可）| 标准库用法（JSON Schema 校验、`fetch` API 等）|

**判定方法**：如果该机制错了会让架构重构 → L3 三级；如果错了只是配置改一下 → L2 二级；如果是 API 手册级别的 → L1。

### 2.2 三级模板（每个机制必须包含）

```
原理层（Principle）        典型篇幅：300-500 字
  - 思想来源：这个问题历史上怎么解决的？哪个经典模式 / 论文 / RFC？
  - 形式化描述：用 1-3 个公式 / 伪代码 / 状态图 / 时序图表达核心机制
  - 为什么这样设计：作者在 trade-off 中选了哪个支点？
  - 替代方案：考虑过哪些别的设计，为什么放弃了？

方案设计层（Design）       典型篇幅：500-800 字
  - 数据结构：核心数据结构 + 字段含义（可引用 class/struct 定义行号）
  - 模块划分：模块边界 + 接口签名 + 依赖方向
  - 算法步骤：关键算法 + 时间 / 空间复杂度
  - 异常路径：失败如何处理？回滚？重试？

可落地层（Implementation） 典型篇幅：500-800 字
  - 依赖清单：npm 包 / pip 包 / 系统依赖 / 硬件要求
  - 边界条件：6+ 条 if-else / 异常处理场景
  - 选型建议：3-5 条「这个场景应该选 X」
  - 已知坑：3+ 条踩过的坑 + 规避方法
  - 可观测性：关键 metric / log / trace
```

### 2.3 每级回答的核心问题（关键！）

| 级别 | 核心问题 | 反例（写错的标志）|
|---|---|---|
| **原理层** | **"为什么这样设计"** | 写成了"X 是一个 Y，它支持 Z"（这是介绍，不是原理）|
| **方案设计层** | **"具体怎么做"** | 写成了"它使用了 ABC 技术"（这是堆砌，不是设计）|
| **可落地层** | **"我应该怎么用"** | 写成了"请参考官方文档"（这是逃避，不是落地）|

**经验法则**：每个级别只回答一个核心问题。如果一个段落同时回答 2 个问题，**说明分错了级别**，要拆开。

---

## 三、3 个真实示例（来自 Requira 调研实战）

### 示例 1 · LiteLLM 的 Provider 路由机制（原理层 · L3）

**核心问题**：LiteLLM 为什么能"零代码切换 100+ LLM"？

#### 思想来源

- **API Gateway 模式**（1990s SOA 时代）：一个入口 + 多个后端适配
- **Strategy 模式**（GOF 1994）：把"算法族"封装成可替换的策略
- **Adapter 模式**（GOF 1994）：把不同接口适配成统一接口

LiteLLM 是这三个模式的组合。

#### 形式化描述

```python
# 简化伪代码（实际在 litellm/main.py:5132）
def completion(
    model: str,              # e.g. "anthropic/claude-sonnet-4-6"
    messages: List[Message],
    stream: bool = False,
    **options,
) -> ModelResponse:
    provider_name, model_name = parse_model_string(model)
    adapter = provider_router.get(provider_name)
    return adapter.completion(model=model_name, messages=messages, stream=stream, **options)
```

#### 为什么这样设计

**核心 trade-off**：用户写 LLM 调用代码时，是"为每个 provider 写一份 SDK"还是"写一份代码，参数化 provider"？

- 选 A（每 provider 一份 SDK）：调用灵活，但切换成本高
- 选 B（LiteLLM）：调用统一，但增加 1 层间接 + 失去 provider-specific 高级特性

LiteLLM 选 B，因为 **90% 调用只用到 provider 的 20% 通用能力**（chat / stream / tool use），那 20% 值得统一。

#### 替代方案

- **LangChain**：比 LiteLLM 更重（自带 chain / agent / vector store 抽象），SDD 场景过重
- **OpenAI Function Calling 标准**：Anthropic 不完全兼容，工具调用需绕一层
- **直接调 SDK**：失去模型切换能力，多云策略受限

> **来源**：[src: E001, E003, E004]

---

### 示例 2 · LangGraph 的 Checkpoint 机制（方案设计层 · L3）

**核心问题**：LangGraph 怎么做到"长时 workflow 中断后能精确恢复"？

#### 数据结构

```python
# libs/checkpoint/langgraph/checkpoint/base/__init__.py:177
class BaseCheckpointSaver(Generic[V]):
    """保存 / 加载 workflow state 的抽象接口"""

class Checkpoint(TypedDict):
    v: int                        # 版本号
    id: str                       # checkpoint 唯一 id
    ts: str                       # ISO timestamp
    channel_values: dict[str, V]  # state 数据（用户消息 + 决策问题答案 + Spec）
    channel_versions: dict[str, int]  # 每个 channel 的版本号（用于冲突检测）
    pending_writes: list[tuple[Channel, Write]]  # 待提交写入
    versions_seen: dict[str, int] # 用于 time travel
```

#### 模块划分

```
BaseCheckpointSaver (抽象层，libs/checkpoint/base/)
    ↓ 实现
MemorySaver      ← 单进程，调试用
SqliteSaver      ← 本地持久化，MVP 用
PostgresSaver      ← 多进程 / 多机，生产用
AzureCosmosSaver  ← 云原生，特定云厂商用
```

**依赖方向**：`BaseCheckpointSaver` 不依赖任何具体实现；具体实现可独立替换。

#### 算法步骤

```
每次 node 执行后：
  1. node.fn(state) → new_state
  2. saver.put(config, checkpoint_metadata, new_state)
     - 序列化 channel_values → bytes
     - 写 backend（memory dict / sqlite row / postgres table）
  3. 返回 new_state 给下一个 node

resume 时：
  1. saver.get_tuple(config) → checkpoint
  2. checkpoint.channel_values → 恢复 state
  3. 找到 pending_writes 中断时的写入，重放
  4. 从 last_node 继续执行
```

**复杂度**：put O(n)（n = state 大小），get O(1)。

#### 异常路径

- **保存失败**：抛异常，workflow 终止（不部分提交，避免状态不一致）
- **恢复失败**：版本号校验失败 → 抛 IncompatibleCheckpointError，要求用户从头跑
- **backend 不可用**：MemorySaver 永远可用；Sqlite/Postgres 启动时校验

> **来源**：[src: E008, E009, E010, E011, E012]

---

### 示例 3 · Dify 的 SSE 流式输出（可落地层 · L3）

**核心问题**：怎么把 SSE 流式输出从 demo 升级到生产？

#### 依赖清单

```python
# requirements.txt
fastapi >= 0.110        # ASGI 框架
uvicorn[standard] >= 0.27  # ASGI server，支持 SSE
sse-starlette >= 2.0    # SSE helper（可选，自己实现也简单）
```

#### 边界条件（6+ 条）

| 条件 | 行为 |
|---|---|
| **客户端断开连接** | Starlette 检测 `disconnected` 事件 → 取消上游 LLM API 请求（避免浪费 token） |
| **上游 LLM 流断开** | 发送 `event: error\ndata: {"code": "stream_broken"}\n\n` 后结束流 |
| **大消息切分** | 单 event data > 4KB 时分多个 event（部分代理 / proxy 限制） |
| **背压控制** | 用 asyncio.Queue 做 producer-consumer 模式，避免内存爆炸 |
| **心跳** | 每 15 秒发送 `: heartbeat\n\n`（SSE 注释），防 NAT / proxy 超时断开 |
| **取消 timing** | Client 取消 → asyncio.CancelledError → 触发上游 cancel callback |
| **消息顺序** | SSE 天然有序，但多 worker 时需 sticky session（不要 LB 到不同节点） |

#### 选型建议

| 场景 | 推荐 |
|---|---|
| 单租户 / 单进程 LLM 调用 | 直接用 SSE，EventSource API |
| 多租户 / 需 resumability | SSE + `Last-Event-ID`（浏览器原生支持）|
| 双向通信（用户中断生成）| WebSocket（但需 LLM provider → SSE → WS 转换）|
| 超大规模（10k+ 并发流） | SSE + nginx stream + Redis pub/sub |

#### Dify 的"延迟批量"优化

Dify 源码 `api/core/app/apps/agent_app/app_runner.py:1103` 有个不显眼但极重要的优化：

```python
class AgentAppRunner:
    text_delta_debounce_seconds: float = 0.05  # 50ms
    # 当 LLM 输出的 delta 间隔 < 50ms 时，累积到下一个 chunk 一起发
    # 避免 1 个 token / event 的高频发包导致前端卡顿
```

**为什么**：Anthropic / OpenAI 流式输出粒度细到 1 个 token = 1 个 event。1 个 100 token 响应会有 100+ 个 event，浏览器渲染跟不上。批量后 100 token → 5-10 个 event，前端 50ms 一次的 batch 渲染完全够用。

#### 已知坑（3+ 条）

1. **Nginx 默认 proxy_buffering on** → 流式响应被缓存 4KB 才发出。**必须**在 location 加 `proxy_buffering off;` + `proxy_cache off;`
2. **CDN 缓存**：Cloudflare 默认缓存 SSE 响应 → **必须**配 `Cache-Control: no-store` + 关闭 `Brotli` 压缩（部分版本 SSE + Brotli 有 bug）
3. **Heartbeat 缺失**：某些反向代理（如 AWS ALB）默认 60 秒空闲断开 → 客户端永远等不到 EOF。必须配置服务端心跳 < 60 秒
4. **CORS preflight**：浏览器 SSE 用 EventSource，**不**触发 preflight，但跨域 cookie 需要 `withCredentials: true` + 服务端 `Access-Control-Allow-Credentials: true`

> **来源**：[src: E043, E044, E045, E062, E072]

---

## 四、撰写"原理层"的 5 个反例（避免写成概述）

### 反例 1 · 把"是什么"当成"为什么"

| ❌ 反例 | ✅ 正确 |
|---|---|
| "LiteLLM 是一个统一的 LLM 网关" | "LiteLLM 用 Strategy 模式把 100+ LLM provider 封装成统一接口，路由函数 `completion(model, messages)` 根据 model 名查表分发到 `anthropic.py` / `openai.py` / `ollama.py` 等适配器" |

**诊断**：如果把机制名称删掉，句子还能成立 → 写成了概述。

### 反例 2 · 缺少形式化描述

| ❌ 反例 | ✅ 正确 |
|---|---|
| "LangGraph 支持 checkpoint" | "LangGraph 把每次 node 执行后的 `{state, next, metadata}` 序列化到 `BaseCheckpointSaver` 抽象层，可插拔后端包括 memory / sqlite / postgres，resume 时反序列化恢复" |

**诊断**：能用一段代码 / 公式 / 状态图表达的，必须写出来。光说"支持 X"不够。

### 反例 3 · 没有 trade-off

| ❌ 反例 | ✅ 正确 |
|---|---|
| "Dify 用 SSE 做流式" | "Dify 选 SSE 而非 WebSocket：Anthropic / OpenAI 官方 API 都是 SSE，零转换成本；代价是 SSE 单向，但 LLM 流式不需要双向通信" |

**诊断**：原理层必须解释"为什么不是另一种"。

### 反例 4 · 把"配置项"当成"原理"

| ❌ 反例 | ✅ 正确 |
|---|---|
| "Dify 支持 `text_delta_debounce_seconds` 配置" | "Dify 的 SSE pipeline 包含延迟批量优化（`text_delta_debounce_seconds`，默认 50ms），目的是把高频 token 流降速到前端能消化的 chunk rate，避免 EventSource 回调阻塞主线程" |

**诊断**：原理层要解释"为什么有这个配置"，不是列举配置项。

### 反例 5 · 把"我用了什么"当成"原理"

| ❌ 反例 | ✅ 正确 |
|---|---|
| "我们的项目用 LiteLLM + LangGraph + Trigger.dev" | "我们的项目选 LiteLLM 作 LLM 网关（zero-cost 模型切换）+ LangGraph 作 agent 编排（state graph + checkpoint + HITL 三件套 first-class），二者通过 LiteLLM 的 `success_callback=["langfuse"]` 串联" |

**诊断**：原理层要回答"为什么这样组合"，不是列技术栈。

---

## 五、可落地层的"已知坑"清单（如何挖坑）

挖坑不是"踩过才写"，而是**有方法可循**：

### 5.1 · 看 README 的 Limitations 段

```bash
# 大多数成熟项目 README 有 "Limitations"、"Caveats"、"Known Issues" 段
grep -i "limitation\|caveat\|known issue" README.md
```

### 5.2 · grep 关键词找代码遗留

```bash
grep -rn "TODO\|FIXME\|HACK\|XXX" <package>/ --include="*.py" --include="*.ts" | head -30
```

每条 TODO 背后都有一个故事：作者知道有问题但没修 / 未来再优化 / 设计权衡。

### 5.3 · 看 GitHub Issues 标 "bug" + "documentation" 的高赞

```bash
# WebFetch GitHub Issues（用 ?q=is:issue+is:open+label:bug+sort:reactions）
# 找 starred 数 ≥ 10 的 issue = 社区踩过的高频坑
```

### 5.4 · 看 release notes 的 breaking change 段

```bash
# 大项目每个 minor 版本的 release notes 都有 "BREAKING CHANGES" 段
# 例：LiteLLM v1.0 → v1.20 之间删除了哪些 API
```

### 5.5 · 亲自跑 demo 看错误

```
时间盒：30 分钟
操作：clone → install → run demo → 故意制造异常（断网 / 大 prompt / 并发）→ 记录错误信息
```

**这 5 步产出 5+ 条已知坑**，**质量比"踩过才写"高得多**，因为是**社区共识级**的坑。

---

## 六、与其他文件关联

| 文件 | 关系 |
|---|---|
| `SKILL.md` §四·准则 2 | 上层抽象原则（本文件是它的可执行 SOP）|
| `references/代码仓调研方法论.md` | 找证据用（grep + Read）|
| `outputs/0X-调研/draft.md`（B 产出）| 本 SOP 的输入（"落点"段已是二级的雏形）|
| `outputs/0X-分析/draft.md`（分析组 B 产出）| 本 SOP 的产出（完整三级）|
| `outputs/0X-撰写/final-report.md` | 把"原理 → 设计 → 落地"翻译成报告章节 |
| `outputs/0X-质检/check.md` | 用本 SOP §二 / §四 反例表判定 B 写得"够不够原理" |

---

## 七、一句话总结

> **三级拆解的核心不是"字数凑够"，而是"每个级别回答一个不同的问题"**：原理层回答"为什么这样设计"，方案层回答"具体怎么做"，落地层回答"我应该怎么用"。任何一段同时回答 2 个问题，说明分错了级别，要拆开。