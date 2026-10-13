# Graph Report - .  (2026-10-13)

## Corpus Check
- Corpus is ~1,218 words - fits in a single context window. You may not need a graph.

## Summary
- 102 nodes · 101 edges · 10 communities (8 shown, 2 thin omitted)
- Extraction: 99% EXTRACTED · 1% INFERRED · 0% AMBIGUOUS · INFERRED: 1 edges (avg confidence: 0.95)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Runtime Dependencies|Runtime Dependencies]]
- [[_COMMUNITY_Expo App Configuration|Expo App Configuration]]
- [[_COMMUNITY_Package Scripts and Tooling|Package Scripts and Tooling]]
- [[_COMMUNITY_Development Guidelines|Development Guidelines]]
- [[_COMMUNITY_TypeScript Configuration|TypeScript Configuration]]
- [[_COMMUNITY_Android Icon Configuration|Android Icon Configuration]]
- [[_COMMUNITY_Project Notes|Project Notes]]
- [[_COMMUNITY_Home Screen|Home Screen]]
- [[_COMMUNITY_Lint Configuration|Lint Configuration]]

## God Nodes (most connected - your core abstractions)
1. `expo` - 13 edges
2. `Project development guidelines` - 10 edges
3. `scripts` - 7 edges
4. `adaptiveIcon` - 5 edges
5. `Codexgrams project notes` - 5 edges
6. `Expo project README` - 4 edges
7. `android` - 3 edges
8. `web` - 3 edges
9. `experiments` - 3 edges
10. `compilerOptions` - 3 edges

## Surprising Connections (you probably didn't know these)
- `File-based routing` --semantically_similar_to--> `Expo Router`  [INFERRED] [semantically similar]
  README.md → AGENTS.md
- `Expo project README` --references--> `Expo`  [EXTRACTED]
  README.md → AGENTS.md
- `Development build` --conceptually_related_to--> `Expo Go`  [EXTRACTED]
  AGENTS.md → README.md
- `Decisions` --references--> `Codexgrams project notes`  [EXTRACTED]
  notes/Decisions.md → notes/Home.md
- `Codexgrams project notes` --references--> `Ideas`  [EXTRACTED]
  notes/Home.md → notes/Ideas.md

## Import Cycles
- None detected.

## Communities (10 total, 2 thin omitted)

### Community 0 - "Runtime Dependencies"
Cohesion: 0.08
Nodes (24): dependencies, expo, expo-constants, expo-device, expo-font, expo-glass-effect, expo-image, expo-linking (+16 more)

### Community 1 - "Expo App Configuration"
Cohesion: 0.11
Nodes (17): reactCompiler, typedRoutes, expo, experiments, icon, ios, name, orientation (+9 more)

### Community 2 - "Package Scripts and Tooling"
Cohesion: 0.12
Nodes (15): devDependencies, eslint-config-expo, @types/react, typescript, main, name, private, scripts (+7 more)

### Community 3 - "Development Guidelines"
Cohesion: 0.18
Nodes (15): Continuous Native Generation, Development build, Expo Application Services (EAS), Expo, Expo Router, Graph freshness, Graphify, Project development guidelines (+7 more)

### Community 4 - "TypeScript Configuration"
Cohesion: 0.25
Nodes (7): compilerOptions, paths, strict, extends, include, @/*, @/assets/*

### Community 5 - "Android Icon Configuration"
Cohesion: 0.29
Nodes (7): backgroundColor, backgroundImage, foregroundImage, monochromeImage, adaptiveIcon, predictiveBackGestureEnabled, android

### Community 6 - "Project Notes"
Cohesion: 0.47
Nodes (6): Decisions, Codexgrams project notes, Project notes vault, Obsidian, Ideas, Tasks

## Knowledge Gaps
- **68 isolated node(s):** `name`, `slug`, `version`, `orientation`, `icon` (+63 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **2 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `dependencies` connect `Runtime Dependencies` to `Package Scripts and Tooling`?**
  _High betweenness centrality (0.123) - this node is a cross-community bridge._
- **Why does `expo` connect `Expo App Configuration` to `Android Icon Configuration`?**
  _High betweenness centrality (0.049) - this node is a cross-community bridge._
- **What connects `name`, `slug`, `version` to the rest of the system?**
  _68 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Runtime Dependencies` be split into smaller, more focused modules?**
  _Cohesion score 0.08333333333333333 - nodes in this community are weakly interconnected._
- **Should `Expo App Configuration` be split into smaller, more focused modules?**
  _Cohesion score 0.1111111111111111 - nodes in this community are weakly interconnected._
- **Should `Package Scripts and Tooling` be split into smaller, more focused modules?**
  _Cohesion score 0.125 - nodes in this community are weakly interconnected._
## Extraction accounting and limitations

Semantic extraction used the current Codex session. Its token usage is unavailable; token counts are not measured. No external LLM API was called.

Graph health warning: 38 dangling endpoint edges (external imports and config references) were omitted by Graphify, and 3 undirected same-endpoint relationships collapsed. The graph is incomplete; verify detailed claims against source files. See graph-health.json.
