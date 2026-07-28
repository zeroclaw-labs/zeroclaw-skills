# Knowledge Graph Skill

Extract structured knowledge graphs, entity relationships, call graphs, and architectural dependency maps from codebases and unstructured documents.

Powered by **Graphifyy** (`graphify` CLI + Tree-sitter) for fast local codebase parsing, with automatic LLM fallback for prose documents.

## Installation

```bash
zeroclaw skills install knowledge-graph
```

## Prerequisite (Optional but Recommended)

For high-speed, local AST-based parsing of codebases without LLM token overhead:

```bash
pip install graphifyy
# or using uv:
uv tool install graphifyy
```

## Quick Start Command Patterns

### 1. Keyless Code-Only AST Indexing
```bash
# Index AST structures locally
graphify . --code-only

# Perform community clustering & generate report/docs
graphify cluster-only .
```

### 2. Custom Provider Registration (e.g., NVIDIA NIM, vLLM, Ollama)
Register custom OpenAI-compatible providers dynamically with `graphify provider`:

```bash
# Add custom provider
graphify provider add nvidia \
  --base-url https://integrate.api.nvidia.com/v1 \
  --default-model minimaxai/minimax-m2.7 \
  --env-key NVIDIA_API_KEY

# Manage providers
graphify provider list
graphify provider show nvidia
graphify provider remove nvidia

# Execute extraction using custom provider
export NVIDIA_API_KEY="nvapi-..."
graphify . --backend nvidia
```

## Capabilities

- **Codebase Knowledge Graphs**: Parse Python, JavaScript, TypeScript, Go, Rust, C++, and more into graph representations.
- **Custom Provider Support**: Easily register custom OpenAI-compatible backends (NVIDIA NIM, Ollama, vLLM, LiteLLM) via `graphify provider`.
- **Architectural Insights**: Detect "God Nodes" (overly coupled modules), community clusters, and circular dependencies.
- **Visual Artifacts**:
  - `graphify-out/graph.html`: Interactive force-directed web graph.
  - `graphify-out/GRAPH_REPORT.md`: Markdown summary report.
  - `graphify-out/graph.json`: Machine-readable graph data.
  - **Inline Mermaid Diagrams**: Rendered in chat for visual inspection.
- **Unstructured Document Extraction**: Extract entity relationships and evidence provenance from Markdown, PDFs, and documentation.

## Permissions

| Permission | Reason |
|------------|--------|
| `shell_exec` | Run local `graphify` CLI commands to generate project knowledge graphs. |

## Usage Examples

- *"Generate a knowledge graph of this repository."*
- *"Identify god nodes and circular dependencies in `src/`."*
- *"Extract entities and relationships from `architecture.md`."*
- *"Show a Mermaid dependency diagram for the auth service."*
