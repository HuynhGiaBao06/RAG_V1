# Minimal Research RAG V1

A small Python 3.11 project for searching a personal collection of about 20 English-language research papers and answering questions with citations to the original PDF pages.

## Status

The project is in initial setup. The indexing pipeline, search, and CLI are planned.

## How it works

1. **Index papers.** Extract text from PDFs page by page, split it into chunks, and generate embeddings locally on CPU with `BAAI/bge-small-en-v1.5`. Store text and metadata in SQLite and vectors in NumPy. Rebuild the index when the collection changes.
2. **Find evidence.** Combine semantic search with SQLite FTS5 keyword search. Search runs locally and works independently of Gemini.
3. **Answer questions.** Send the question and selected passages to Gemini 3.5 Flash-Lite (`gemini-3.5-flash-lite`). Check citation IDs against the retrieved passages and display supporting text from the index with its PDF page number.

## Project layout

- `data/raw/` holds the source PDFs.
- `papers.json` holds manually checked paper metadata and BibTeX.
- `config/` holds separate settings files for each component.

## Scope

V1 focuses on a CLI for text-based PDFs and uses no RAG framework or dedicated vector database. Use only public, non-confidential material for answer generation. Verify cited passages against the original PDF before using them in research.