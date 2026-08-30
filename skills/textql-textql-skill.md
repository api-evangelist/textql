---
name: Textql
description: Use when helping users query data, build automated analyses, create dashboards, set up data connectors, configure ontologies, or deploy agents. TextQL is an AI data platform where Ana (the AI agent) answers questions in plain language, writes SQL, runs Python, and produces reports, dashboards, and alerts across your entire data stack.
metadata:
    mintlify-proj: textql
    version: "1.0"
---

# TextQL Skill

## Product Summary

TextQL is an AI data platform where Ana—your AI data scientist—connects to your entire data stack (warehouses, databases, APIs, BI tools) and answers questions in plain language. Ana writes SQL, runs Python, searches the web, and produces charts, dashboards, playbooks (scheduled reports), and agents (autonomous monitors). Use TextQL when users need to query data without SQL knowledge, automate recurring analyses, build persistent dashboards, or deploy agents that watch data and post insights to a shared feed. Primary docs: https://docs.textql.com

**Key files and concepts:**
- **Threads**: Conversational sessions with Ana—ask questions, refine, get answers
- **Connectors**: Live connections to data sources (Snowflake, BigQuery, PostgreSQL, Salesforce, HubSpot, etc.)
- **Ontology**: Semantic layer defining metrics, entities, and business logic (stored as `.md`, `.tql`, `.csv`, `.py` files)
- **Playbooks**: Scheduled automated analyses that deliver to Slack, email, or feed
- **Agents**: Long-running monitors that watch data, remember findings, and post to the feed
- **Feed**: Shared activity stream where agents and teams publish insights
- **Dashboards/Data Apps**: Persistent interactive views built from Ana's outputs

## When to Use

Reach for this skill when:
- A user wants to **ask questions about their data** without writing SQL (start a thread, enable Text-to-SQL tool)
- A user needs to **set up a data connector** (database, warehouse, API, BI tool)
- A user wants to **automate a recurring report** (create a playbook with a schedule)
- A user needs to **define consistent metrics** across their organization (build ontology with `.tql` files)
- A user wants to **deploy an agent** that monitors data and posts alerts to Slack or the feed
- A user needs to **build a dashboard or data app** from Ana's analysis
- A user is **configuring access control** (RBAC, ontology folder permissions)
- A user wants to **embed Ana or a dashboard** into their own product
- A user needs to **query data programmatically** (v2 or v3 API)

## Quick Reference

### Core Tools (Enable in Chat)

| Tool | Use When | How to Nudge Ana |
|------|----------|-----------------|
| **Text-to-SQL** | Querying your warehouse/database | "Query the database for...", "Pull from the orders table..." |
| **Python** | Calculations, visualizations, file exports | "Build a chart showing...", "Calculate the 30-day rolling average..." |
| **Web Search** | Current events, competitor research, external benchmarks | "Search for...", "What does the market say about..." |
| **TQL Query** | Querying your ontology for consistent metrics | "Use the ontology to...", "What's our revenue definition..." |
| **Forms** | Collecting structured user input in chat | "Create a form that asks for..." |
| **Thread Search** | Reusing prior analysis from past threads | "Find my previous analysis on..." |

### Playbook Workflow

1. **Create**: Start a chat, ask Ana a question, get results
2. **Save as Playbook**: Click "Save as Playbook" on the response
3. **Configure**: Set schedule (daily, weekly, monthly), delivery (Slack channel, email, feed)
4. **Deploy**: Click "Deploy" to activate
5. **Monitor**: View runs in Playbook history; edit anytime

### Agent Workflow

1. **Create**: Go to Feed → Create Agent
2. **Configure**: Set prompt, schedule, connectors, output channels
3. **Deploy**: Agent runs on schedule, publishes to feed and optional Slack
4. **Monitor**: View posts in feed; use Mission Control for live operational view

### Ontology File Types

| File Type | Purpose | Example |
|-----------|---------|---------|
| `.md` | Business rules, definitions, documentation | `revenue-definition.md`, `fiscal-calendar.md` |
| `.tql` | Metric definitions compiled into SQL | `metrics.tql` with revenue, churn, ARR definitions |
| `.csv` | Reference data, lookup tables | Territory mappings, product catalogs |
| `.py` | Custom calculations, API clients | Custom transformations Ana can call |
| `ANA.md` | Org-wide entry point (auto-loaded every thread) | Navigation index, org rules, context |
| `README.md` | Folder-level index (helps Ana navigate) | What's in this folder, when to use it |

### Connector Setup

1. Navigate to **Connectors Page** → **New Connector**
2. Select data source type (database, API, BI tool, etc.)
3. Enter credentials (hostname, port, username, password, API key, etc.)
4. Test connection
5. Assign to users/roles via RBAC

**Supported sources**: Snowflake, BigQuery, Redshift, Databricks, PostgreSQL, MySQL, MongoDB, Salesforce, HubSpot, Slack, Linear, GitHub, Tableau, Power BI, REST APIs, and 40+ more.

### RBAC Permissions (Key Resources)

| Resource | Default Member | When to Grant `write_private` |
|----------|---|---|
| **Chat** | `read`, `write` (public) | When member needs to create private chats |
| **Connector** | `write` (public) | When member needs to create private data sources |
| **Playbook** | `write` (public) | When member needs private scheduled analyses |
| **Feed** | `write` | When member needs to manage agents/channels |
| **Ontology** | `read` | When member needs to edit ontology files |
| **Dashboard** | `write` (public) | When member needs private dashboards |

## Decision Guidance

### When to Use Playbook vs. Agent

| Aspect | Playbook | Agent |
|--------|----------|-------|
| **Question Type** | Known, fixed ("Send me weekly sales") | Open-ended domain ("Watch for anomalies") |
| **Output** | Same report each run | Posts only when noteworthy |
| **Memory** | Stateless | Remembers prior findings |
| **Audience** | Email/Slack recipients | Org feed (+ optional Slack relay) |
| **Use Case** | Recurring reports, dashboards | Continuous monitoring, anomaly detection |

### When to Use Ontology vs. No Ontology

| Scenario | Use Ontology | Skip Ontology |
|----------|---|---|
| **Team size** | 5+ people sharing data definitions | Solo analyst or small team |
| **Metric consistency** | Critical (finance, compliance) | Nice-to-have |
| **Data model complexity** | Complex joins, many tables | Simple, obvious schema |
| **Governance** | Need audit trail of changes | Ad-hoc analysis |
| **Scale** | 100+ tables, multiple teams | <20 tables, single team |

### When to Use Text-to-SQL vs. Python

| Task | Text-to-SQL | Python |
|------|---|---|
| Fetch data from warehouse | ✓ | — |
| Filter, aggregate, join tables | ✓ | — |
| Statistical analysis | — | ✓ |
| Build charts/visualizations | — | ✓ |
| Export to CSV/PDF | — | ✓ |
| Transform data | Both (SQL for retrieval, Python for shaping) | |

## Workflow

### Typical Task: Build a Recurring Sales Report

1. **Start a thread**: Click "New Thread" → ask Ana "Show me daily sales by region for the last 30 days"
2. **Verify the data**: Review the chart/table Ana produces; ask follow-up questions to refine
3. **Save as playbook**: Click "Save as Playbook" on the final response
4. **Configure delivery**: Set schedule (daily at 9am), destination (Slack #sales-team channel)
5. **Deploy**: Click "Deploy" to activate
6. **Monitor**: Check Slack for daily reports; edit the playbook anytime to adjust the prompt or schedule

### Typical Task: Set Up a Data Connector

1. **Navigate**: Settings → Connectors → New Connector
2. **Select source**: Choose database type (e.g., Snowflake, PostgreSQL, BigQuery)
3. **Enter credentials**: Hostname, port, username, password (or API key for APIs)
4. **Test**: Click "Test Connection" to verify
5. **Assign access**: Use RBAC to grant users/roles access to this connector
6. **Use in chat**: Users can now query this source by enabling the connector in their thread

### Typical Task: Build Your Ontology

1. **Start small**: Create `ANA.md` with org-wide rules and a navigation table
2. **Add domain folders**: Create folders like `Finance/`, `Sales/`, `Product/` with `README.md` in each
3. **Define metrics**: Add `.tql` files for revenue, churn, ARR (metrics that must be consistent)
4. **Add context**: Add `.md` files for business rules, fiscal calendars, exclusion lists
5. **Review changes**: All edits land in the **Reviews** tab as diffs before going live
6. **Deploy**: Approve diffs; Ana automatically loads relevant files into every thread

### Typical Task: Deploy an Agent

1. **Go to Feed**: Click "Create Agent"
2. **Write prompt**: "Monitor daily revenue. Alert if it drops >10% from 7-day average. Post weekly summary."
3. **Set schedule**: Daily at 6am
4. **Select connectors**: Choose which data sources the agent can query
5. **Configure outputs**: Select Feed channels and optional Slack channel
6. **Deploy**: Agent runs on schedule, publishes posts to feed
7. **Monitor**: View posts in feed; use Mission Control for live operational view

## Common Gotchas

- **Text-to-SQL not working**: Check that the Text-to-SQL toggle is enabled in the chat toolbar. It's off by default for some users; enable it in Settings → Capabilities if needed.
- **Playbook not running**: Verify the playbook is **deployed** (not just saved). Undeployed playbooks don't run on schedule.
- **Agent not posting**: Check that the agent has at least one Feed channel selected in Outputs. If all channels are cleared, the agent is private and won't post.
- **Ontology changes not taking effect**: Changes land in the **Reviews** tab as diffs. They don't go live until approved. Check the Reviews tab to see pending changes.
- **Connector credentials failing**: Ensure the user/role has the correct network access (firewall, IP allowlist). See Network Configuration guide for your database type.
- **Ana not using ontology**: Ontology files are loaded on-demand based on relevance. If Ana isn't using a file, it may not be discoverable from `ANA.md` or folder `README.md`. Add navigation pointers or make the file auto-attach via role/connector scoping.
- **Stale metric definitions**: Unlike human docs, Ana follows ontology definitions precisely. If a metric definition is outdated, it will produce wrong numbers silently. Update the ontology when business logic changes.
- **Private resources not visible**: `read_private` and `write_private` permissions grant org-wide visibility. Members without these permissions cannot see other users' private chats, playbooks, or dashboards.
- **API key role mismatch**: API keys inherit their roles at creation time. If a user is promoted, their old API key still has the old role. They need a new key.

## Verification Checklist

Before submitting work:

- [ ] **Connector**: Test connection succeeds; users have RBAC access; data preview shows expected tables
- [ ] **Playbook**: Deployed (not just saved); schedule is set; delivery destination (Slack/email) is configured; test run produces expected output
- [ ] **Agent**: Deployed; schedule is set; at least one Feed channel selected; test run publishes a post
- [ ] **Ontology**: Changes approved in Reviews tab; `ANA.md` exists and is discoverable; folder `README.md` files guide navigation; no stale definitions
- [ ] **Thread**: Tools enabled match the task (Text-to-SQL for queries, Python for charts, Web Search for external data); connectors selected; ontology enabled if applicable
- [ ] **Dashboard/Data App**: Filters work; data refreshes correctly; sharing/embed links are configured if needed
- [ ] **RBAC**: Users have the minimum permissions needed; `read_private`/`write_private` granted only where necessary; roles reviewed quarterly

## Resources

**Comprehensive page listing**: https://docs.textql.com/llms.txt

**Critical docs**:
1. [Quickstart & Core Concepts](https://docs.textql.com/core/get-started/quickstart) — What TextQL is, what Ana can do, core concepts
2. [Build Your Ontology](https://docs.textql.com/core/ontology/build-your-ontology) — Full setup guide for semantic layer, file types, dynamic loading, version control
3. [Playbooks & Agents](https://docs.textql.com/core/how-it-works/playbooks) and [Feed Overview](https://docs.textql.com/core/how-it-works/feed) — Automation and monitoring workflows

---

> For additional documentation and navigation, see: https://docs.textql.com/llms.txt