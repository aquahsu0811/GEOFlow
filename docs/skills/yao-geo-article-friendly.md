# yao-geo-article-friendly

`yao-geo-article-friendly` 用于把已有文章改造成 GEO/AI 搜索友好的发布草稿，同时保留原文核心观点，并输出评分、改造说明、待补充内容和风险提示。

## 核心能力

- 盘点原文核心观点、证据、数据、引用、案例、结构、实体和术语。
- 按 GEO 加权维度诊断原文差距，重点检查证据、可引用性、结构、表达和语义密度。
- 重构标题层级、核心摘要、正文结构、结论和 FAQ。
- 区分 `原文支持`、`外部已核验` 和 `建议补充`，避免把待补充数据或引用写成事实。
- 输出改造后的完整文章、GEO 优化度评分、改造执行说明、补充清单和风险提示。

## 适合场景

- 已有 SEO 文章、公众号文章、官网文章、白皮书片段或产品页文案需要轻量 GEO 改造。
- 需要把松散文章整理成更利于 AI 搜索理解、引用和摘要的结构化 Markdown。
- 需要在不编造引用和数据的前提下，标记应该补充哪些来源、数据口径或专家原话。

## 不适合场景

- 从零写一篇新文章。
- 法律、广告合规或医疗金融等高风险专业审查。
- 承诺 AI 搜索排名、引用率或收录结果。
- 自动生成未经核验的统计数据、报告名、专家引用或案例。

## 默认输出

1. `改造后的完整文章`
2. `GEO优化度评分`
3. `改造执行说明`
4. `需要用户补充或确认的内容`
5. `改造风险提示`

## 相关入口

- [Skill 包](../../skills/yao-geo-article-friendly)
- [执行方法](../../skills/yao-geo-article-friendly/references/geo-article-transformation-method.md)
- [输出契约](../../skills/yao-geo-article-friendly/references/geo-output-contract.md)
- [输出风险说明](../../skills/yao-geo-article-friendly/reports/output-risk-profile.md)
