<!--
Copyright © 2026 姚金刚. All rights reserved.
Project: yao-deepseek-crawler
Created by: 姚金刚
Date: 2026-06-20
X: https://x.com/yaojingang
-->

# yao-deepseek-crawler

`yao-deepseek-crawler` 是一个 DeepSeek 网页端 AI 搜索重复采样与 GEO 概率分析 skill。它通过本地 OpenCLI Browser Bridge 复用已登录的 DeepSeek 网页会话，对一组关键词进行多轮独立采样，保留原始 JSON、每轮 raw 文件和日志，并生成结构化 Markdown、Excel 和 Kami 风格 HTML 可视化报告。

## 适用场景

- 评估品牌、人物、公司或产品在 DeepSeek AI 搜索里的可见性
- 观察目标实体的提及率、平均提及次数、Top 1 / Top 3 / Top 5 概率和平均排名
- 比较目标实体与同类型竞品在 AI 推荐结果里的概率差异
- 分析 DeepSeek 引用的信源结构、域名、来源标题和标题意图
- 为 GEO 优化形成可复采、可审计、可视化的诊断报告

## 标准输入

```text
1. 关键词：一个或多个 DeepSeek 搜索问句
2. 轮询次数：每个关键词查询几次
3. 目标实体：例如 新东方、姚金刚、某产品名、某公司名
4. 实体类型：人、公司、产品
5. OpenCLI profile：已连接的 Browser Bridge profile
6. 抓取间隔：例如 1-3 分钟随机，或 5-20 分钟安全随机
```

目标实体别名可选，但建议提供。示例：

```text
目标实体别名：
姚金刚先生、姚金刚老师、姚金刚xx等
```

## 核心输出

- `deepseek-crawl.json`：规范化后的 DeepSeek 重复采样证据数据集
- `raw/*.json`：每次独立采样的原始抓取结果
- `logs/*.log`：每次独立采样的执行日志
- `summary.json`：机器可读的分析指标汇总
- `structured-data.md`：结构化字段和分析表格的 Markdown 导出
- `structured-data.xlsx`：与 Markdown 对应的 Excel 工作簿
- `report.html`：可视化诊断报告

## 蔚来真实采样示例

示例基于 2026-06-20 的真实 DeepSeek 网页端采样：

- 关键词：4 个
- 每个关键词轮询：5 次
- 计划样本：20
- 成功样本：20
- 失败样本：0
- 引用来源：199 条
- 目标实体：蔚来
- 补充别名：蔚来 ES8、蔚来 ES6、蔚来 EC6、NIO 等

示例文件：

- [问题列表](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/questions.txt)
- [品牌别名表](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/brands.txt)
- [原始聚合 JSON](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/deepseek-crawl.json)
- [品牌/公司口径 Markdown](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/report-brand-company/structured-data.md)
- [品牌/公司口径 Excel](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/report-brand-company/structured-data.xlsx)
- [品牌/公司口径 HTML 报告](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/report-brand-company/report.html)
- [产品口径 HTML 报告](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/report/report.html)
- [单次 raw 样本目录](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/raw)
- [单次日志目录](../../skills/yao-deepseek-crawler/examples/nio-nev-deepseek-20260620/logs)

## 运行边界

- 需要本地 OpenCLI CLI、Browser Bridge、已登录 DeepSeek 网页会话和本地 DeepSeek browser crawler 脚本。
- 不处理 DeepSeek 登录、验证码、Cloudflare、人机校验、账号风控或平台限制绕过。
- 概率指标是重复采样估计，不是真实市场份额或平台官方排名。
- 竞品实体识别优先使用用户提供的别名表；自动识别候选需要人工复核。

## 包路径

- Skill package: [skills/yao-deepseek-crawler](../../skills/yao-deepseek-crawler)
