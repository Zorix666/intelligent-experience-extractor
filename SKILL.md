---
name: intelligent-experience-extractor
description: 检索并沉淀项目内已验证、可复用的工程经验。采用「索引 + 正文」两级轻量架构（先整读轻量索引，再按需 Grep 正文，严禁整篇全量加载正文）。Use when starting troubleshooting, build, deployment, CI/CD, dependency, environment, configuration, performance, or stability work to search the project's Markdown knowledge base; also use after such work is verified complete, or when the user asks to record or review lessons, to extract a sanitized preview and save it only with explicit authorization or a persisted auto-save opt-in. Operate only through project file reads and writes, without platform memory, hooks, or proprietary APIs.
whenToUse: 检索并沉淀项目内已验证、可复用的工程经验。采用「索引 + 正文」两级轻量架构（先整读轻量索引，再按需 Grep 正文，严禁整篇全量加载正文）。Use when starting troubleshooting, build, deployment, CI/CD, dependency, environment, configuration, performance, or stability work to search the project's Markdown knowledge base; also use after such work is verified complete, or when the user asks to record or review lessons, to extract a sanitized preview and save it only with explicit authorization or a persisted auto-save opt-in. Operate only through project file reads and writes, without platform memory, hooks, or proprietary APIs.
---

# 智能经验提取器

只把可执行、已验证、对未来同类工程任务有帮助的结论写入项目 Markdown 知识库。**采用「索引 + 正文」两级轻量架构，按需 Grep，严禁全量读入正文。** 不要归档对话、推理过程、长日志或全量任务历史。

## 架构核心原则（两级轻量化检索）

1. **两级双文件结构**：
   - **索引文件**（`experiences-index.md`）：极轻量（几十行，百字节量级），一行一条。格式为：
     `- **[现象/报错特征]** → grep `[精确关键词]` — [一句话根因与方案简述]`
   - **正文文件**（`experiences.md`）：详细条目正文（可能膨胀至上百 KB），每条固定包含五段链路（现象 → 根因 → 方案 → 验证 → 预防）。
2. **严禁全量加载正文（红线）**：
   - 绝不可在任务开始时无脑全量读取/加载 `experiences.md`。必须**先读索引，命中后再用精准关键词 Grep 正文切片**，节约 90%+ 上下文 Token 并避免注意力稀释。
3. **双步同步闭环沉淀**：
   - 沉淀任何经验时，必须**同时完成两步**：
     - ① 正文追加或合并重写至 `experiences.md`；
     - ② 同步在索引 `experiences-index.md` 补加/更新对应的一行索引映射（漏填索引会导致未来会话永远无法被轻量扫描命中）。
4. **与记忆项目（Project Memory Curator）的职责解耦**：
   - **经验库专职**：排障、Bug 修复、故障恢复、构建失败、环境踩坑复盘；
   - **记忆库专职**：技术栈架构、模块边界、用户偏好、部署命令等项目事实；
   - 严禁将排障流水账写入记忆库，也严禁在经验库中记录与故障排查无关的项目基础事实。

## 兼容性约束

- 只使用 Agent 普遍具备的目录遍历、文本搜索、文件读取和文件写入能力。
- 不要求任何平台专有 API、Hook、记忆、数据库、网络服务或脚本运行时。
- 把项目内 Markdown 文件视为唯一持久状态；不要依赖当前会话之外的隐式记忆。
- 使用当前 Agent 可用的文件工具；通过 Grep / 局部切片读取候选条目。
- 不要为了使用本 Skill 修改业务代码、Agent 配置或项目工作流。

## 定位项目与知识库

1. 确定本次任务的项目根目录。优先使用版本库根目录；没有版本库时，使用包含主要项目文件的最近共同目录。
2. 检查项目已有的知识库位置。没有既有规范时，默认使用 `<project-root>/.agent-knowledge/`：
   - `README.md`：说明、自动保存设置与规则。
   - `experiences-index.md`：轻量索引表，先读此文件。
   - `experiences.md`：正文文件，按需 Grep。
3. 首次创建默认知识库时，以 [assets/knowledge-base-readme.md](assets/knowledge-base-readme.md) 为基础生成 `README.md`，使用 [assets/experiences-index.md](assets/experiences-index.md) 作为索引模板，使用 [assets/experience-entry.md](assets/experience-entry.md) 作为条目模板。

## 在新任务开始时检索（按需 Grep）

当新任务涉及排障、构建、部署、CI/CD、依赖、环境、配置、性能或稳定性时，在深入修改前执行两级检索：

1. **第 1 步：整读轻量索引**：
   直接读取 `experiences-index.md`（或 `README.md` 中的索引表）。对比当前任务的报错现象、关键命令或异常行为，快速扫描命中的索引行。
2. **第 2 步：按关键词精准 Grep 正文**：
   - 若索引命中对应条目，提取索引行中注明的 `grep 关键词`；
   - 使用文本搜索工具定向抓取正文切片（例如 `Grep pattern="<关键词>" path=".agent-knowledge/experiences.md" -A 100`）；
   - **绝不整篇读入 `experiences.md`**。
3. **第 3 步：引用并告知**：
   命中高度相关条目时，简短提示用户：「经验库中存在相关记录：〈标题〉（.agent-knowledge/experiences.md）」，并作为待验证线索融入本次修复思路。
4. 没有命中结果时静默继续，不打断任务。

## 在任务完成后评估

先确认任务真正完成，再决定是否建议沉淀。满足以下至少一项才进入提取流程：

- Bug 已修复，并由测试、构建、实际运行、复现检查或用户确认表明问题解决。
- 部署、CI/CD、依赖、环境或配置故障已经恢复，并有可观察验证。
- 性能或稳定性改进已经通过指标、基准、压力测试、监控或可靠复现完成验证。
- 用户明确要求记录、复盘或沉淀本次经验。

出现任一以下情况时跳过，不展示保存预览，也不写文件：

- 仅是简单拼写、格式、文案或机械性修改。
- 根因无法确认，且用户没有明确要求保存带不确定性标记的复盘。
- 只是不可泛化的一次性操作，没有未来可执行的结论。
- 尚未验证修复、恢复或优化结果。
- 候选内容主要是原始日志、个人信息、密钥、私密配置或客户数据。
- 与现有经验实质重复，且没有新增适用边界、验证证据或更好的解决方案。

## 提炼候选经验

只从任务中已确认的事实提炼五段链路：

1. **问题现象**：记录用户可观察的错误、行为或影响，以及必要的触发条件。
2. **根本原因**：记录证据支持的直接因果关系。区分根因、诱因和表面报错；未知处明确写“未知”。
3. **解决方案**：给出未来可重复执行的最小步骤、关键配置和必要命令。解释关键选择，不复述全部操作过程。
4. **验证方式**：记录实际执行过的验证及结果。不虚构未跑过的测试。
5. **预防措施**：记录能减少复发的版本锁定、静态检查、CI 校验、健康检查、监控、告警或文档改进。

置信度只使用：`已验证`（均有证据）、`部分验证`（部分细节待明确）、`推测`（用户明确要求保留假设）。

## 脱敏与最小化

在展示预览之前完成脱敏：

- 移除 API key、Token、密码、Cookie、Authorization 头、私钥、连接串中的凭据及其可识别片段。
- 泛化邮箱、真实姓名、用户名、本机主目录、私有 IP、租户标识、内部主机名、私有 URL。
- 使用统一占位符，如 `<REDACTED_TOKEN>`、`<USER>`、`<PRIVATE_HOST>`、`<PROJECT_PATH>`。
- 路径优先写项目相对路径，不暴露开发者个人机器绝对路径。

## 查重与合并优先

在预览前搜索现有条目：

- **合并优先于新增**：同一主题若已有条目，应**合并重写旧条目**，把新环境、新证据和完善后的方案合为一体，严禁堆砌“补充一 / 补充二”流水账；
- 无实质重合时才作为新条目追加。

## 预览与授权

默认自动保存为关闭。唯一可跨会话生效的授权是在知识库 Markdown 中存在精确标记：

```markdown
<!-- intelligent-experience-extractor:auto-save=on -->
```

生成候选后，先展示以下简短预览，不执行文件写入：

```markdown
经验预览
- 动作：新增 / 更新
- 正文目标：.agent-knowledge/experiences.md
- 索引同步：.agent-knowledge/experiences-index.md
- 标题：...
- 索引行：- **[现象简述]** → grep `[关键词]` — [一句话根因与方案]
- 现象：...
- 根因：...
- 方案：...
- 验证：...
- 预防：...
- 脱敏：已检查（无敏感数据）

请选择：保存 / 编辑（说明修改） / 跳过
```

## 保存与双步同步写入

获得授权后执行最小写入：

1. **步骤 1：写入正文**：写入或更新 `experiences.md` 对应条目。
2. **步骤 2：同步写入索引**：在 `experiences-index.md` 索引列表中同步追加或更新对应的一行索引映射。
3. 写入后局部重读验证，确认索引关键词与正文中的特征词严格一致。
4. 简短向用户汇报完成，指出写入的标题与文件路径。

## 行为边界

- 不要因为启用本 Skill 就把每个日常编码任务都写成经验。
- 不要把内部思维过程、聊天摘要或全量长日志当作经验。
- 严禁全量读入 `experiences.md`。必须始终坚持“先读索引，按需 Grep 正文”。
- 不要让经验提取改变“先完成并验证工程任务”的优先级。