# CODEBASE.md — GBrain
> Auto-generated codebase memory for AI agents. Last updated: 2026-06-19.

## Purpose
Postgres-native personal knowledge brain with hybrid RAG search, self-wiring knowledge graph, and synthesis layer. Provides 30+ MCP tools for AI coding agents (Claude Code, Codex, Cursor). Features vector + BM25 keyword search, graph traversal, gap analysis, and an autonomous dream cycle for overnight enrichment.

## Tech Stack
- **Languages**: TypeScript (Bun ≥1.3.10)
- **Database**: PGLite (Postgres 17 WASM, zero-config) or PostgreSQL + pgvector (Supabase/self-hosted)
- **Key Libraries**: @electric-sql/pglite, pgvector, @modelcontextprotocol/sdk, bullmq, pino, zod, marked, commander
- **Build**: Bun, TypeScript
- **Admin UI**: Vite-based admin dashboard

## Entry Points
- **CLI**: `src/cli.ts` — Main CLI (`gbrain` command, 103KB)
- **Core**: `src/core/engine.ts` — BrainEngine interface (~47 operations)
- **MCP Server**: `gbrain serve` — stdio or HTTP MCP server
- **Admin**: `admin/` — Web admin dashboard

## Directory Structure
```
src/                  Core source code
  cli.ts              CLI entry point (all commands)
  core/               Core engine, types, operations
    engine.ts         BrainEngine interface (contract)
    pglite-engine.ts  PGLite engine implementation
    search/           Hybrid search (vector + BM25 + RRF)
    ai/               AI gateway (LLM calls for synthesis)
    ingestion/        Content ingestion pipeline
    minions/          Job queue (BullMQ-shaped, Postgres-native)
    embedding.ts      Embedding provider integration
    config.ts         Configuration management
    markdown.ts       Markdown parser
    link-extraction.ts Entity/link extraction
  mcp/                MCP server implementation
  commands/           CLI command implementations
  eval/               Evaluation framework
  types/              TypeScript type definitions
  schema.sql          Database schema (71KB)
skills/               43 curated skill modules
  RESOLVER.md         Skill routing configuration
recipes/              Integration recipes (voice, email, calendar, etc.)
templates/            Document templates (HEARTBEAT, SOUL, USER, ACCESS_POLICY)
scripts/              Build, test, migration, and ops scripts
tools/                Developer tools
tests/                Test suites (heavy, standard)
test/                 Additional tests (840+ test files)
docs/                 Tutorials, architecture, integrations, MCP guides
admin/                Admin dashboard (Vite + React)
evals/                Eval framework configs (skillopt, embedding)
examples/             Example skillpacks
```

## Key Files
| File | Purpose |
|------|---------|
| `src/cli.ts` | CLI entry point (all gbrain commands) |
| `src/core/engine.ts` | BrainEngine interface (~47 operations) |
| `src/core/pglite-engine.ts` | PGLite engine (default, zero-config) |
| `src/core/search/hybrid.ts` | Hybrid search (vector + BM25 + RRF) |
| `src/core/minions/` | Job queue (durable subagents) |
| `src/schema.sql` | Full database schema (71KB) |
| `gbrain.yml` | Brain configuration |
| `skills/RESOLVER.md` | Skill routing rules |
| `llms.txt` | Documentation map for AI agents |
| `AGENTS.md` | Agent instructions |

## Architecture
```
Signal → Brain-first Search → Respond → Write → Auto-link → Sync
  ↓
Hybrid Search: Vector (HNSW/pgvector) + BM25 + RRF + Source-tier boost
  ↓
Knowledge Graph: Entity extraction → typed edges (works_at, invested_in, founded)
  ↓
Synthesis Layer: Retrieved pages → LLM synthesis with citations + gap analysis
  ↓
Dream Cycle (cron): Dedup, fix citations, score salience, find contradictions
```
Two engines: PGLite (personal, ≤50K pages, zero-config) and PostgreSQL+pgvector (team/large). Brain repo is the system of record (markdown files). Schema packs define brain shape (15-type DRY/MECE taxonomy). Minions job queue for durable subagents.

## Run Commands
```bash
# Install
bun install -g github:garrytan/gbrain

# Quick start (local brain, no server)
gbrain init --pglite            # 2-second setup
gbrain doctor                   # Verify health
gbrain import ~/notes/          # Index markdown
gbrain search "query"           # Raw retrieval
gbrain think "query"            # Synthesized answer with citations

# MCP server
gbrain serve                    # stdio (for Claude Code, Cursor)
gbrain serve --http             # HTTP + OAuth 2.1 + admin dashboard

# Connect to coding agent
claude mcp add gbrain -- gbrain serve
gbrain connect https://host/mcp --token gbrain_xxx --install

# Development
bun test                        # Run tests
bun run typecheck               # Type checking
```

## Environment Variables
| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | PostgreSQL connection string (or PGLite for local) |
| `OPENAI_API_KEY` | OpenAI API key (default embedding provider) |
| `ZEROENTROPY_API_KEY` | ZeroEntropy API key (alternative embeddings) |
| `VOYAGE_API_KEY` | Voyage AI API key (alternative embeddings) |
| `ANTHROPIC_API_KEY` | Anthropic API key (for synthesis) |
| `GBRAIN_REMOTE_TOKEN` | Bearer token for remote brain access |
| `GBRAIN_CONTRIBUTOR_MODE` | Enable eval/contributor features |
