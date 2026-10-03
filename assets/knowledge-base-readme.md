# Agent 工程经验库

> 本目录采用**两级索引轻量化架构**：索引（`experiences-index.md`）+ 正文（`experiences.md`）。**严禁整篇全量加载正文**，通过索引按需精确 Grep 正文，大幅节省上下文并防止幻觉。

<!-- intelligent-experience-extractor:auto-save=off -->

## 核心原则

- **分工边界**：本目录专门记录排障、Bug 修复、构建失败、环境配置与部署踩坑经验；项目事实与技术选型等基本事实请使用 `project-memory-curator`。
- **两级检索**：
  1. 任务开始时先阅读 `experiences-index.md` 索引表（几十行极轻量）；
  2. 命中后再按关键词 Grep `experiences.md` 对应切片内容，绝不全文读入上下文。
- **双步沉淀**：任务完成后沉淀经验时，**必须同时完成两步**：
  1. 写入/合并正文 `experiences.md`（标准四段结构：现象 → 根因 → 方案 → 验证）；
  2. 同步在索引 `experiences-index.md` 中补加一行映射。
- **合并优先**：同一主题的新经验应合并重写旧条目，避免堆砌流水账。
- **脱敏合规**：绝不写入 Token、API Key、密码、真实姓名、私有 IP 或客户数据。

## 文件结构

- 索引文件：[experiences-index.md](experiences-index.md)（任务前优先读取）
- 正文文件：[experiences.md](experiences.md)（按需 Grep 读取）