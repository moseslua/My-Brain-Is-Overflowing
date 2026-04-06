---
name: paper-ingest
description: >
  Ingest new papers with automatic summarization, key finding extraction,
  and connection mapping. Add papers from arXiv, PDF, or URL.
  Triggers: "read this paper", "add paper", "ingest arxiv",
  "arxiv 2501.12345", "process this pdf", "paper summary",
  "add to reading list", "save paper".
---

# Paper Ingest Skill

Automatically process and integrate new papers into your research vault.

## Capabilities

1. **Fetch from arXiv** — Download metadata, PDF, and source
2. **Extract from PDF** — OCR if needed, extract text
3. **Auto-summarize** — Generate structured summary
4. **Find connections** — Link to existing papers in vault
5. **Update indices** — Add to semantic search, citation graph
6. **Queue for reading** — Add to prioritized reading list

## Usage

```
/ingest arxiv 2501.12345
/ingest https://arxiv.org/abs/2501.12345
/ingest paper.pdf
/ingest "Mixture of Experts with Expert Choice Routing"  # Search and add
```

## Process

1. Fetch/download paper
2. Extract text and metadata
3. Generate structured summary
4. Create paper note in `03-Resources/Papers/[year]/`
5. Update semantic index
6. Find related papers in vault
7. Suggest relevance to active projects
8. Add to reading queue with priority

## Output

- Paper note created
- Summary generated
- Connections suggested
- Reading priority assigned
- Related papers listed
