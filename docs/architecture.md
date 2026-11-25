# Minimal Research RAG V1

V1 targets a Python 3.11 CLI for searching about 20 English-language research papers and answering questions with page-level citations. This document describes the target design. Indexing and retrieval run locally on CPU. Only answer generation calls Gemini.

## Technical requirements

- **Indexing.** Use `pymupdf4llm` to extract page-aware Markdown. Remove repeated headers, footers, and reference sections from indexed text. Split text within each PDF page into 350-token chunks with 50 tokens of overlap. Use `sentence-transformers` with `BAAI/bge-small-en-v1.5` to produce normalized 384-dimensional embeddings. Rebuild the full index when the corpus changes.
- **Storage.** Keep manually verified metadata and BibTeX in `papers.json`. Store paper and chunk records and the FTS5 index in `research.db`, and vectors in `embeddings.npy`. Each vector row maps to one chunk. Load both artifacts from the same build.
- **Retrieval.** Rank the top 15 cosine matches and top 15 FTS5/BM25 matches. Fuse both rankings with reciprocal rank fusion (`k=60`) and return up to eight unique chunks. Search works without Gemini credentials.
- **Generation.** Use `google-genai` with `gemini-3.5-flash-lite`, temperature `0.2`, and a 4,096-token output limit. Send only the question, prompt instructions, and selected passages. Accept citation IDs only from those passages and display verbatim evidence from SQLite with its PDF page number. Report insufficient evidence when an answer cannot be supported. Use public papers and a Free Tier project, with search grounding and paid fallback disabled.

## Target layout

```text
config/        ingestion.yaml, chunking.yaml, embedding.yaml, storage.yaml,
               retrieval.yaml, generation.yaml, logging.yaml
.env           local Gemini credential
data/          raw/ PDFs, processed/ Markdown, storage/ index artifacts
papers.json    curated paper metadata and BibTeX
prompts/       answer.md for generation instructions
src/
  settings/    YAML loading and per-component validation
  path.py      project path resolution
  logging_config.py  logging setup
  orchestration/     indexing.py and querying.py coordinate the workflows
  ingestion.py, chunking.py, embedding.py, storage.py,
  retrieval.py, generation.py, citation.py  pipeline logic
index.py       indexing CLI entry point
app.py         search and question-answering CLI entry point
tests/         unit and integration tests
```

YAML owns non-secret settings. CLI entry points load and validate each component's settings and initialize logging. Orchestrators pass validated settings and resolved paths to the pipeline modules.

## Implementation order

1. Set up settings, paths, and logging. Test PDF extraction and page numbering on three papers.
2. Add chunking, embeddings, and storage. Verify vector-to-chunk mapping and full rebuilds.
3. Implement and test local retrieval before adding Gemini generation and citation validation.
4. Integrate the CLI and test it on the target collection. Keep Streamlit outside the initial CLI milestone. V1 excludes OCR, incremental indexing, rerankers, RAG frameworks, and dedicated vector databases.
