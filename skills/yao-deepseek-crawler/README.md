# Yao DeepSeek Crawler

`yao-deepseek-crawler` turns repeated DeepSeek web AI-search crawls into GEO probability evidence.

It is designed for jobs like:

- 1-20 keywords/questions, each crawled repeatedly in a fresh DeepSeek web session.
- Keep every raw answer and DeepSeek source-panel reference.
- Diagnose one target entity with a declared entity type, then compare it with same-type competitors extracted from AI answers.
- Aggregate visibility, average mention count, average rank, Top 1 / Top 3 / Top 5 probability, sentiment tendency, source/channel mix, title patterns, repeated citations, and target-vs-competitor gaps.
- Generate a canonical JSON dataset, structured Markdown, structured Excel workbook, and a Kami-styled HTML report.

It is not a generic website crawler and does not use the DeepSeek API.

## Requirements

- Node.js 21+ for fresh crawling; Node.js 18+ for analysis-only usage
- Python 3.10+
- OpenCLI CLI 1.8.4+
- OpenCLI Browser Bridge connected to a Chrome or Edge profile
- Logged-in DeepSeek web session
- Existing crawler at `../../SourceCode/opencli-boss-ai/scripts/geo-deepseek-browser-direct.mjs`, or pass `--crawler-script`

Start with the user setup guide:

- `references/user-setup-and-usage.md`

Preflight for fresh crawling:

```bash
node scripts/preflight.mjs --profile <opencli-profile>
```

Preflight for report generation from an existing JSON:

```bash
node scripts/preflight.mjs --analysis-only
```

## Stage 1: Batch Crawl

Create `questions.txt` with one question per line, then run:

```bash
node scripts/deepseek_batch_crawl.mjs \
  --questions questions.txt \
  --repeat 5 \
  --profile <opencli-profile> \
  --target-entity "新东方" \
  --target-aliases "新东方前途出国,前途出国" \
  --entity-type company \
  --safe-random-delay \
  --out-dir runs/deepseek-study-abroad
```

Outputs:

- `runs/deepseek-study-abroad/deepseek-crawl.json`
- `runs/deepseek-study-abroad/raw/*.json`
- `runs/deepseek-study-abroad/logs/*.log`

Use `--dry-run` to verify the plan without opening DeepSeek; dry-run does not require the crawler script to exist. For real DeepSeek web runs, prefer `--safe-random-delay`, which waits a random 5-20 minutes between fresh samples. For short controlled tests, pass `--delay-min-minutes 1 --delay-max-minutes 3`.

## Stage 2: Analyze And Render

Standard input fields:

```text
1. 关键词：出国留学公司推荐、出国留学机构哪家好
2. 轮询次数：每个关键词查询 5 次
3. 目标实体：例如 新东方、姚金刚、某产品名、某公司名
4. 实体类型：公司
```

Optional `brands.txt` can provide reviewed aliases and competitors:

```text
目标实体别名：
例如 姚金刚先生、姚金刚老师、姚金刚xx等
```

```text
新东方|新东方前途出国|前途出国
启德教育|启德留学|启德
新通教育
指南者留学|指南者
```

Run:

```bash
python3 scripts/analyze_deepseek_results.py \
  runs/deepseek-study-abroad/deepseek-crawl.json \
  --target-entity "新东方" \
  --target-aliases "新东方前途出国,前途出国" \
  --entity-type company \
  --brands-file brands.txt \
  --out-dir runs/deepseek-study-abroad/report
```

Outputs:

- `runs/deepseek-study-abroad/report/summary.json`
- `runs/deepseek-study-abroad/report/structured-data.md`
- `runs/deepseek-study-abroad/report/structured-data.xlsx`
- `runs/deepseek-study-abroad/report/report.html`

The standard report requires `--target-entity` and `--entity-type`. Entity type accepts `person/人`, `company/公司`, or `product/产品`. Target matching uses contains logic plus aliases, so target `新东方` can consolidate `新东方前途出国` and `前途出国` into the same target row. Short Latin aliases such as `AI` or `GEO` only match standalone tokens to avoid accidental matches inside longer names. Competitors are limited to the same entity type as the target. The report compares the target and competitors by mention rate, average mentions per valid answer, Top 1 / Top 3 / Top 5 probability, average rank, dominant sentiment, negative share, and source mentions. `structured-data.md` and `structured-data.xlsx` contain the same cleaned tables for overview fields, question coverage, entity metrics, source/channel data, title features, and output file paths. The HTML defaults to Simplified Chinese and includes an English summary toggle, Chinese source display names, clickable repeated URLs/titles with unique compact URL labels, normalized target-vs-best-3 radar scoring, solid color-coded click-to-reveal bubble benchmarking, title intent analysis, compact domain treemap, and GEO recommendations with priority charts, conservative trend projections, and concrete methods.

Exploratory legacy mode still supports `--target-kind auto/person/company/product/mixed`, but final reports should use the standard target fields above.

## Offline Verification

```bash
node --check scripts/deepseek_batch_crawl.mjs
python3 -m py_compile scripts/analyze_deepseek_results.py scripts/test_entity_recognition.py
python3 scripts/test_entity_recognition.py
python3 scripts/analyze_deepseek_results.py fixtures/sample-deepseek-crawl.json \
  --target-entity '光引GEO' \
  --entity-type company \
  --brands '光引GEO,源易信息,PallasAI' \
  --out-dir /tmp/yao-deepseek-crawler-report
```

## Package Map

- `SKILL.md`: route trigger and workflow skeleton
- `references/user-setup-and-usage.md`: install prerequisites and end-user runbook
- `references/deepseek-crawl-workflow.md`: local crawler setup and run rules
- `references/report-contract.md`: JSON contract and metric definitions
- `scripts/preflight.mjs`: dependency and login-state checker
- `scripts/deepseek_batch_crawl.mjs`: repeated crawl orchestrator
- `scripts/analyze_deepseek_results.py`: aggregation and Kami-styled HTML rendering
- `fixtures/sample-deepseek-crawl.json`: offline test fixture
