# Project graph

This folder contains generated Graphify notes for the project. Open the existing `notes` vault in Obsidian to browse them.

Start with [[_COMMUNITY_Home Screen]], [[_COMMUNITY_Root Navigation]], or [[_COMMUNITY_Development Guidelines]]. Open `Graph/graph.canvas` for the visual map.

## Query from the project terminal

```powershell
graphify query "RootLayout"
graphify explain "Index()"
```

The machine-readable graph lives at `graphify-out/graph.json`; Codex uses it through the Graphify CLI. Obsidian provides a linked Markdown view of the same graph.

## Keep it current

After code changes, run `graphify update .`. Ask Codex to refresh the Obsidian export and HTML map as well. After documentation changes, ask Codex to run the full Graphify skill again so semantic relationships are updated.

Generated notes may be overwritten on export. Keep handwritten notes in the parent vault.

## Current limits

The initial graph has 102 nodes and 101 edges. Its extraction diagnostics found 38 unresolved external/config references and 3 relationships collapsed by the undirected representation. Check source files when precision matters. Semantic extraction token usage is unavailable; no external LLM API was called.
