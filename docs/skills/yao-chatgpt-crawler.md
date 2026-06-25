<!--
Copyright © 2026 姚金刚. All rights reserved.
Project: yao-chatgpt-crawler
Created by: 姚金刚
Date: 2026-06-25
X: https://x.com/yaojingang
-->

# yao-chatgpt-crawler

`yao-chatgpt-crawler` 是一个 ChatGPT Web AI Search 采样与 GEO 概率分析 skill。它通过 OpenCLI Browser Bridge 连接已经登录的 Chrome 或 Edge profile，对同一组问题做多次 ChatGPT web 采样，抓取答案、可见引用来源和来源弹层链接，再输出目标实体与同类型竞品的概率报告。

它不是 ChatGPT API 客服开发工具，也不是通用网页爬虫。

## 适用场景

- 评估某个品牌、公司、人物或产品在 ChatGPT 答案中的出现概率。
- 多次采样同一批 AI 搜索问题，观察 Top 1 / Top 3 / Top 5、平均排名和提及率。
- 识别同类型竞品，并过滤噪声词、概念词和非同类型实体。
- 分析 ChatGPT 答案里的引用来源、域名分布、标题意图和重复引用。
- 输出可交付的 HTML 报告、结构化 Markdown、Excel 和机器可读 JSON。

## 输入项

标准执行前需要确认：

- 关键词 / 问题列表
- 重复采样次数
- 目标实体
- 实体类型：人、公司或产品
- OpenCLI profile
- 单次查询间隔：默认随机 `30s-1m`，可选 `1-3m` 或 `3-10m`
- 可选别名 / 竞品表

## 执行流程

1. 运行 `node scripts/preflight.mjs --profile <profile>` 检查 OpenCLI、浏览器连接和 ChatGPT 会话。
2. 准备 `questions.txt`，每行一个问题。
3. 准备可选 `brands.txt`，每行一个实体，别名用 `|` 分隔。
4. 运行 `scripts/chatgpt_batch_crawl.mjs` 做重复采样。
5. 运行 `scripts/analyze_chatgpt_results.py` 聚合实体、来源、标题、情绪和概率指标。
6. 检查 `report/summary.json`、`report/structured-data.md`、`report/structured-data.xlsx` 和 `report/report.html`。

## 延迟策略

Fresh crawl 默认在每次查询之间随机等待 `30s-1m`。更保守的运行可以使用：

- `--delay-preset 1-3m`
- `--delay-preset 3-10m`
- `--safe-random-delay`，等同于 `3-10m`

这些策略只是降低请求频率，不绕过登录、验证码、限流或账号风控。

## 示例命令

```bash
node scripts/chatgpt_batch_crawl.mjs \
  --questions questions.txt \
  --repeat 3 \
  --profile edge-chatgpt \
  --target-entity "豆包" \
  --target-aliases "Doubao,豆包大模型,豆包模型" \
  --entity-type product \
  --brands-file brands-model-products.txt \
  --delay-preset 1-3m \
  --out-dir runs/doubao-chatgpt
```

```bash
python3 scripts/analyze_chatgpt_results.py \
  runs/doubao-chatgpt/chatgpt-crawl.json \
  --target-entity "豆包" \
  --target-aliases "Doubao,豆包大模型,豆包模型" \
  --entity-type product \
  --brands-file brands-model-products.txt \
  --semantic-review auto \
  --title "ChatGPT AI Search Probability Report" \
  --out-dir runs/doubao-chatgpt/report
```

## 公开真实测试

仓库包含一个脱敏后的真实测试：

- [豆包模型产品 ChatGPT Web Search 测试](../../skills/yao-chatgpt-crawler/examples/doubao-model-products-real)
- [HTML 报告](../../skills/yao-chatgpt-crawler/examples/doubao-model-products-real/report/report.html)
- [summary.json](../../skills/yao-chatgpt-crawler/examples/doubao-model-products-real/report/summary.json)

这个案例使用 5 个国产大模型问题、每题 3 次采样、目标实体 `豆包`、实体类型 `模型产品`。公开版保留答案文本、引用来源、实体识别和概率指标，但移除了本机 profile、ChatGPT 会话链接、raw 控制台输出和绝对路径。

## 质量门

- 每个正式报告必须声明目标实体、实体类型和采样口径。
- 竞品概率表只能纳入同类型实体。
- 自动实体识别必须暴露语义标签、置信度、进入竞品原因和排除原因。
- 英文报告模式下，标题、导航、表头、说明文字和图表标签都应使用英文。
- 公共示例必须脱敏，不提交私有 raw/log 目录。

## 包路径

- Skill package: [skills/yao-chatgpt-crawler](../../skills/yao-chatgpt-crawler)
