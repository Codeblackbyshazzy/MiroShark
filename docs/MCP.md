# MCP

MiroShark ships two MCP surfaces: a **standalone MCP server** so you can query your knowledge graphs from Claude Desktop, and a set of **report agent tools** used internally by the ReACT report agent.

## Claude Desktop

`backend/mcp_server.py` exposes MiroShark's graph over MCP so you can query it from Claude Desktop without opening the web UI.

Add to your `claude_desktop_config.json` (Claude Desktop → Settings → Developer → Edit Config):

```json
{
  "mcpServers": {
    "miroshark": {
      "command": "/absolute/path/to/MiroShark/backend/.venv/bin/python",
      "args": ["/absolute/path/to/MiroShark/backend/mcp_server.py"]
    }
  }
}
```

Restart Claude Desktop. The `miroshark` tools appear in the hammer menu:

| Tool | What it does |
|---|---|
| `list_graphs` | Survey graphs + entity/edge counts |
| `search_graph` | Full hybrid + rerank pipeline with `kinds` / `as_of` filters |
| `browse_clusters` | Community zoom-out (auto-builds on first call) |
| `search_communities` | Direct semantic search over cluster summaries |
| `get_community` | Expand one cluster with members |
| `list_reports` | Reports generated on a graph |
| `list_report_sections` | Sections of a report |
| `get_reasoning_trace` | Full ReACT decision chain for one section |

**Example prompt:** *"List my MiroShark graphs, browse clusters on the biggest one for anything about oracle exploits, then show me the reasoning trace from the most recent report on that graph."*

## Report Agent Tools

The ReACT report agent exposes these tools internally (configured via `REPORT_AGENT_MAX_TOOL_CALLS`):

| Tool | Purpose |
|---|---|
| `insight_forge` | Multi-round deep analysis on a specific question |
| `panorama_search` | Hybrid vector + BM25 + graph retrieval |
| `quick_search` | Lightweight keyword search |
| `interview_agents` | Live conversation with sim agents |
| `analyze_trajectory` | Belief drift — convergence, polarization, turning points |
| `analyze_equilibrium` | Nash equilibria on a 2-player stance game fit to the final belief distribution — reveals whether observed outcomes are consistent with self-interested play (requires `nashpy`) |
| `analyze_graph_structure` | Centrality / community / bridge analysis |
| `find_causal_path` | Graph traversal between two entities |
| `detect_contradictions` | Conflicting edges in the graph |
| `simulation_feed` | Raw action log filter by platform / query / round |
| `market_state` | Polymarket prices, trades, portfolios |
| `browse_clusters` | Community zoom-out (orienting) |
