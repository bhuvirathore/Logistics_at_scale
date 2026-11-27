## Overview

Most KPI dashboards show a number with full confidence even when the data behind it is broken.
This project is a logistics data platform that treats **data trust as a first-class output**:
every KPI is reported together with a reliability status, traced back to the exact
data-quality checks it depends on.

It models a Transport Management System (TMS) that streams trips, milestone events and
consignments through Kafka CDC, joined with ERP freight bills, and computes KPIs across five
modules: **TAT, Adoption, Detention, Utilization, and Payment & Cost**.

### What it does
- **Medallion pipeline:** Bronze (raw CDC) → Silver (deduplicated, keys resolved with lineage) → Gold (KPI-ready tables)
- **Data contracts in YAML:** a single source of truth for schema, business meaning, FK relationships and quality rules
- **DQ engine:** uniqueness, completeness, referential integrity, business rules, coverage and multi-hop chain-integrity checks, rolled up into a weighted DQ score per entity
- **KPI impact engine:** rates every KPI as RELIABLE, USE_WITH_CAUTION, UNRELIABLE or BLOCKED based on its weakest data dependency
- **Root-cause analysis:** finds where failures concentrate, when they started and what the orphan keys look like
- **Lineage:** upstream ("what feeds this KPI?") and downstream ("what breaks if this table degrades?") built from metadata
- **AI agent:** answers questions like *"Why did on-time delivery drop last month?"* by calling deterministic tools, then explains whether the change is operational or a data artefact
- **MCP server:** exposes all 12 tools to Claude Desktop, Claude Code or any MCP client
- **Databricks port:** generates Unity Catalog COMMENTs, DLT expectations and Genie Space instructions from the same contracts

### Core principle
**The LLM reasons; the code computes.** The agent classifies, retrieves and explains, but every
number comes from SQL, the DQ engine or the KPI engine. Without an API key the agent runs offline
with a deterministic planner, which is also the baseline for the evaluation suite (10/10 on
intent, KPI resolution and tool recall).

### Example finding
> On-time delivery fell 4.45 pts. Carrier V007 drives most of the drop, and its delivery-event
> coverage collapsed from 79% to 34% starting the week of 3 Aug. Part of the decline is real
> (longer transit), part is missing data. Fix the feed before re-baselining the KPI.

### Tech stack
Python · DuckDB · SQL · pandas · scikit-learn (TF-IDF retrieval) · Anthropic Claude (tool use) · Model Context Protocol · Databricks (Unity Catalog, Lakeflow/DLT, Genie)

### Background
This is a synthetic reconstruction inspired by enterprise TMS analytics work. All data is
generated, and real-world defects (missing gate events, sparsely populated carrier codes, orphan
master records, weak ERP–TMS bill matching) are re-created as configurable parameters. No
proprietary schema, code or data is included.
