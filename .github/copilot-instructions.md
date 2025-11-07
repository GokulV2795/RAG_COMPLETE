## Quick summary

This repository (project name: `rag-complete`) is a small Python project centered on LangChain-based RAG workflows. Key facts an AI agent should know up front:

- Python runtime: >= 3.12 (see `pyproject.toml`).
- Dependencies are declared in `pyproject.toml` and loosely mirrored in `requirements.txt`; an authoritative lock is present in `uv.lock`.
- Primary artifacts to inspect: `notebook/document.ipynb` (working examples), `data/` (ingested PDFs), and `main.py` (placeholder CLI entrypoint).

## Where to start (high-value files)

- `pyproject.toml` — project metadata and dependency hints (LangChain, LangChain-community, langchain-core, pypdf, pymupdf).
- `uv.lock` — pinned dependency versions; use this when reproducing environments or diagnosing version incompatibilities.
- `notebook/document.ipynb` — exploratory code; contains direct imports from `langchain_core` (example: `from langchain_core.document import Document`) and is the best place to find runnable examples and prototyping patterns.
- `data/` — holds input artifacts (PDFs). The presence of `pypdf` and `pymupdf` indicates the project expects PDF parsing/ingestion.
- `main.py` — minimal entrypoint; currently prints a greeting (not yet a full CLI).

## Project-specific conventions and patterns

- Use the `pyproject.toml` + `uv.lock` combo for dependency/version tracking. When adding or updating packages, update `pyproject.toml` and create/update the lockfile so other developers/agents use the same resolved versions.
- Prototype code lives in the notebook (`notebook/document.ipynb`) rather than in a production package. Look there first for concrete examples of how the project uses LangChain primitives.
- Tracing/instrumentation: LangSmith/LangChain tracing is part of the dependency tree (see references to `LangSmith` in installed packages). Tracing endpoints are controlled via environment variables such as `LANGSMITH_ENDPOINT` or `LANGCHAIN_ENDPOINT` — agents should not assume tracing is enabled unless these are set.

## Common tasks & exact commands (PowerShell)

Install dependencies (fast, local dev):

```powershell
python -m pip install -r requirements.txt
```

Or install the project and its declared deps (editable):

```powershell
python -m pip install -e .
python -m pip install -r requirements.txt  # optional: notebook/runtime extras
```

Run the quick entrypoint (placeholder):

```powershell
python main.py
```

Open the notebook for examples: inspect `notebook/document.ipynb` in Jupyter or VS Code Notebook view. The notebook imports `langchain_core` primitives — use its cells as canonical examples when implementing new functionality.

## Integration points & environment

- LangChain: project depends on `langchain`, `langchain-core`, and `langchain-community` (check `pyproject.toml` and `uv.lock` for exact versions). Prefer to follow the `langchain-core` import patterns seen in the notebook.
- LangSmith / tracing: the installed packages include LangSmith helpers. If you need to enable run tracing, set `LANGSMITH_ENDPOINT` (and authentication env vars as required by LangSmith). Search for `LANGSMITH` or `LANGCHAIN_ENDPOINT` when adding telemetry.
- PDF ingestion: `pypdf` and `pymupdf` are present. Expect the ingestion pipeline to be: PDF -> page text extraction -> text-splitting -> embeddings -> vector store (note: vector store code is not present in the repo root; inspect notebooks before adding one).

## Safe editing rules for AI agents

- Prefer making minimal, focused changes. Update `pyproject.toml` for dependency additions and update `uv.lock` (or request the maintainer to regenerate it) rather than editing `uv.lock` manually.
- Do not remove the lockfile; it provides reproducibility for the environment.
- When adding new source files, keep examples and prototypes in `notebook/` or a new `src/` package; do not place script-only code at the repo root unless creating a proper CLI module.

## Examples to reference when coding

- Notebook import: `notebook/document.ipynb` contains `from langchain_core.document import Document` — follow that import style for new code that manipulates LangChain Document objects.
- Use `data/` as the canonical place for input PDF files. New ingestion scripts should read from and write artifacts back to `data/` (or a new `data/processed` folder) and be idempotent.

## When you’re unsure

- If you need to run or validate code not present in the notebook, ask for a preferred environment setup (virtualenv/venv/conda) and whether the lockfile should be used to install exact versions.
- If a change touches dependencies, note the required `pyproject.toml` edit and request the maintainer or CI to regenerate `uv.lock`.

---

If anything important is missing from this summary or you'd like the instructions to prioritize certain workflows (tests, CI, packaging), tell me what to emphasize and I will iterate.
