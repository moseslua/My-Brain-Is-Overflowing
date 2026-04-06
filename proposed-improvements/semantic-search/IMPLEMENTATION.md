# Semantic Search Implementation Guide

## Quick Start

This adds vector-based semantic search to find notes by meaning, not just keywords.

## Files to Add

### 1. New Agent: `semantic-seeker.md`

Location: `agents/semantic-seeker.md`

```markdown
---
name: semantic-seeker
description: >
  Find notes by semantic similarity using vector embeddings.
  Triggers: "find similar to", "what's related to", "semantically similar",
  "conceptually close to", "notes like this", "similar content",
  "find connections by meaning", "meaning similar"
tools: Read, Write, Bash, Glob
model: sonnet
---

# Semantic Seeker — Vector-Based Note Discovery

Always respond in the user's language.

## Core Capability

Find notes by meaning similarity using pre-computed embeddings. Unlike keyword search, this finds conceptually related notes even when they use different words.

## User Profile

Read `Meta/user-profile.md` for language preferences and vault context.

## Index Location

Embeddings are stored in: `Meta/semantic-index.db` (SQLite with sqlite-vec extension)

## Capabilities

### 1. Semantic Search

Find notes similar to a query:

```bash
# Query the semantic index
python3 .claude/plugins/semantic-search/query.py "your search query" --top-k 10
```

### 2. Find Similar Notes

Given a note path, find semantically similar notes:

```bash
python3 .claude/plugins/semantic-search/similar.py "path/to/note.md" --top-k 5
```

### 3. Check Index Status

```bash
python3 .claude/plugins/semantic-search/status.py
```

### 4. Trigger Reindex

```bash
python3 .claude/plugins/semantic-search/index.py --full
```

## When to Suggest Other Agents

- **Connector**: When semantic search reveals notes that should be linked
- **Architect**: If semantic clusters suggest new MOCs or areas
- **Librarian**: If duplicate detection finds near-duplicates

## Output Format

```markdown
## Semantic Search Results

Query: "your query"

### Top Matches

1. **Note Title** (similarity: 0.89)
   - Path: `path/to/note.md`
   - Excerpt: First 150 chars...

2. **Note Title** (similarity: 0.82)
   - Path: `path/to/note2.md`
   - Excerpt: First 150 chars...

### Suggested Connections

Notes that might benefit from cross-linking:
- [[Note A]] and [[Note B]] both discuss [topic]

### Suggested next agent
- **Agent**: connector
- **Reason**: Found 3 semantically related notes with no direct links
- **Context**: Notes about [topic] found in different areas
```

## Tools

Use Bash to call the Python scripts in `.claude/plugins/semantic-search/`.
```

### 2. Plugin Code

Location: `.claude/plugins/semantic-search/index.py`

```python
#!/usr/bin/env python3
"""Build and maintain semantic index for vault notes."""

import sqlite3
import sqlite_vec
import hashlib
import json
from pathlib import Path
from datetime import datetime
from typing import List, Tuple, Optional

try:
    from sentence_transformers import SentenceTransformer
except ImportError:
    print("Installing sentence-transformers...")
    import subprocess
    subprocess.run(["pip", "install", "-q", "sentence-transformers"], check=True)
    from sentence_transformers import SentenceTransformer

VAULT_ROOT = Path.cwd()
INDEX_PATH = VAULT_ROOT / "Meta" / "semantic-index.db"
MODEL_NAME = "all-MiniLM-L6-v2"  # 384 dimensions, fast, good quality

def get_model():
    """Load or download the embedding model."""
    return SentenceTransformer(MODEL_NAME)

def init_db():
    """Initialize the vector database."""
    INDEX_PATH.parent.mkdir(parents=True, exist_ok=True)
    db = sqlite3.connect(INDEX_PATH)
    db.enable_load_extension(True)
    sqlite_vec.load(db)
    db.enable_load_extension(False)
    
    # Create tables
    db.execute("""
        CREATE TABLE IF NOT EXISTS notes (
            path TEXT PRIMARY KEY,
            content_hash TEXT,
            modified_time TIMESTAMP,
            title TEXT,
            word_count INTEGER
        )
    """)
    
    # Create vector table (384 dimensions for all-MiniLM-L6-v2)
    db.execute("""
        CREATE VIRTUAL TABLE IF NOT EXISTS embeddings USING vec0(
            path TEXT PRIMARY KEY,
            embedding FLOAT[384]
        )
    """)
    
    db.commit()
    return db

def get_content_hash(content: str) -> str:
    """Get hash of content for change detection."""
    return hashlib.sha256(content.encode()).hexdigest()[:16]

def extract_text_from_note(path: Path) -> Tuple[str, dict]:
    """Extract searchable text from a markdown note."""
    content = path.read_text(encoding='utf-8')
    
    # Simple frontmatter removal
    if content.startswith('---'):
        parts = content.split('---', 2)
        if len(parts) >= 3:
            content = parts[2]
    
    # Extract metadata
    title = path.stem
    word_count = len(content.split())
    
    return content.strip(), {
        'title': title,
        'word_count': word_count
    }

def index_note(db, model, note_path: Path) -> bool:
    """Index a single note. Returns True if indexed, False if unchanged."""
    try:
        content, meta = extract_text_from_note(note_path)
        content_hash = get_content_hash(content)
        
        # Check if already indexed and unchanged
        cursor = db.execute(
            "SELECT content_hash FROM notes WHERE path = ?",
            (str(note_path.relative_to(VAULT_ROOT)),)
        )
        row = cursor.fetchone()
        
        if row and row[0] == content_hash:
            return False  # Unchanged
        
        # Generate embedding
        embedding = model.encode(content, show_progress_bar=False)
        
        # Convert to bytes for sqlite-vec
        embedding_bytes = embedding.tobytes()
        
        # Insert or update
        db.execute(
            """INSERT OR REPLACE INTO notes (path, content_hash, modified_time, title, word_count)
               VALUES (?, ?, ?, ?, ?)""",
            (str(note_path.relative_to(VAULT_ROOT)), content_hash, datetime.now(), meta['title'], meta['word_count'])
        )
        
        db.execute(
            """INSERT OR REPLACE INTO embeddings (path, embedding)
               VALUES (?, ?)""",
            (str(note_path.relative_to(VAULT_ROOT)), embedding_bytes)
        )
        
        return True
    except Exception as e:
        print(f"Error indexing {note_path}: {e}")
        return False

def index_vault(full: bool = False):
    """Index all notes in the vault."""
    model = get_model()
    db = init_db()
    
    # Find all markdown files
    notes = list(VAULT_ROOT.rglob("*.md"))
    # Exclude Meta directory and hidden files
    notes = [n for n in notes if not str(n.relative_to(VAULT_ROOT)).startswith('Meta/') 
             and not n.name.startswith('.')]
    
    print(f"Found {len(notes)} notes to index...")
    
    indexed = 0
    skipped = 0
    
    for note_path in notes:
        if index_note(db, model, note_path):
            indexed += 1
        else:
            skipped += 1
        
        if (indexed + skipped) % 100 == 0:
            print(f"  Progress: {indexed + skipped}/{len(notes)}")
    
    db.commit()
    db.close()
    
    print(f"\nDone! Indexed: {indexed}, Skipped (unchanged): {skipped}")

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("--full", action="store_true", help="Force full reindex")
    args = parser.parse_args()
    
    if args.full:
        # Clear existing index
        if INDEX_PATH.exists():
            INDEX_PATH.unlink()
    
    index_vault(full=args.full)
```

### 3. Query Script

Location: `.claude/plugins/semantic-search/query.py`

```python
#!/usr/bin/env python3
"""Query the semantic index."""

import sqlite3
import sqlite_vec
import struct
from pathlib import Path
from sentence_transformers import SentenceTransformer

VAULT_ROOT = Path.cwd()
INDEX_PATH = VAULT_ROOT / "Meta" / "semantic-index.db"
MODEL_NAME = "all-MiniLM-L6-v2"

def query_index(query: str, top_k: int = 10):
    """Search for semantically similar notes."""
    if not INDEX_PATH.exists():
        print("Index not found. Run: python3 .claude/plugins/semantic-search/index.py --full")
        return
    
    model = SentenceTransformer(MODEL_NAME)
    query_embedding = model.encode(query)
    query_bytes = query_embedding.tobytes()
    
    db = sqlite3.connect(INDEX_PATH)
    db.enable_load_extension(True)
    sqlite_vec.load(db)
    db.enable_load_extension(False)
    
    # Search using vector similarity
    results = db.execute(
        """
        SELECT 
            e.path,
            n.title,
            distance
        FROM embeddings e
        JOIN notes n ON e.path = n.path
        WHERE embedding MATCH ?
        ORDER BY distance
        LIMIT ?
        """,
        (query_bytes, top_k)
    ).fetchall()
    
    print(f"## Semantic Search Results\n")
    print(f'Query: "{query}"\n')
    print(f"### Top {len(results)} Matches\n")
    
    for i, (path, title, distance) in enumerate(results, 1):
        # Convert distance to similarity score (0-1)
        similarity = max(0, 1 - distance)
        print(f"{i}. **{title}** (similarity: {similarity:.2f})")
        print(f"   - Path: `{path}`")
        
        # Get excerpt
        try:
            note_path = VAULT_ROOT / path
            content = note_path.read_text(encoding='utf-8')
            # Remove frontmatter
            if content.startswith('---'):
                parts = content.split('---', 2)
                if len(parts) >= 3:
                    content = parts[2]
            excerpt = content.strip()[:150].replace('\n', ' ')
            print(f"   - Excerpt: {excerpt}...")
        except:
            pass
        print()
    
    db.close()

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("query", help="Search query")
    parser.add_argument("--top-k", type=int, default=10, help="Number of results")
    args = parser.parse_args()
    
    query_index(args.query, args.top_k)
```

### 4. Similar Notes Script

Location: `.claude/plugins/semantic-search/similar.py`

```python
#!/usr/bin/env python3
"""Find notes similar to a given note."""

import sqlite3
import sqlite_vec
from pathlib import Path

VAULT_ROOT = Path.cwd()
INDEX_PATH = VAULT_ROOT / "Meta" / "semantic-index.db"

def find_similar(note_path: str, top_k: int = 5):
    """Find notes similar to the given note."""
    if not INDEX_PATH.exists():
        print("Index not found. Run indexing first.")
        return
    
    db = sqlite3.connect(INDEX_PATH)
    db.enable_load_extension(True)
    sqlite_vec.load(db)
    db.enable_load_extension(False)
    
    # Get the embedding for the source note
    result = db.execute(
        "SELECT embedding FROM embeddings WHERE path = ?",
        (note_path,)
    ).fetchone()
    
    if not result:
        print(f"Note not found in index: {note_path}")
        print("Run: python3 .claude/plugins/semantic-search/index.py")
        return
    
    embedding_bytes = result[0]
    
    # Find similar notes (excluding self)
    results = db.execute(
        """
        SELECT 
            e.path,
            n.title,
            distance
        FROM embeddings e
        JOIN notes n ON e.path = n.path
        WHERE embedding MATCH ? AND e.path != ?
        ORDER BY distance
        LIMIT ?
        """,
        (embedding_bytes, note_path, top_k)
    ).fetchall()
    
    print(f"## Notes Similar to: {note_path}\n")
    
    for i, (path, title, distance) in enumerate(results, 1):
        similarity = max(0, 1 - distance)
        print(f"{i}. **{title}** (similarity: {similarity:.2f})")
        print(f"   - Path: `{path}`")
        print()
    
    db.close()

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument("note", help="Path to note (relative to vault root)")
    parser.add_argument("--top-k", type=int, default=5)
    args = parser.parse_args()
    
    find_similar(args.note, args.top_k)
```

### 5. Status Script

Location: `.claude/plugins/semantic-search/status.py`

```python
#!/usr/bin/env python3
"""Check semantic index status."""

import sqlite3
from pathlib import Path

VAULT_ROOT = Path.cwd()
INDEX_PATH = VAULT_ROOT / "Meta" / "semantic-index.db"

def get_status():
    """Show index statistics."""
    if not INDEX_PATH.exists():
        print("❌ Index not found")
        print("\nRun: python3 .claude/plugins/semantic-search/index.py --full")
        return
    
    db = sqlite3.connect(INDEX_PATH)
    
    count = db.execute("SELECT COUNT(*) FROM notes").fetchone()[0]
    last_update = db.execute(
        "SELECT MAX(modified_time) FROM notes"
    ).fetchone()[0]
    
    print("## Semantic Index Status\n")
    print(f"✅ Index exists: {INDEX_PATH}")
    print(f"📊 Indexed notes: {count}")
    print(f"🕐 Last update: {last_update}")
    print(f"🔧 Model: all-MiniLM-L6-v2 (384 dimensions)")
    
    db.close()

if __name__ == "__main__":
    get_status()
```

### 6. Auto-Index Hook

Location: `.claude/hooks/post-note-save.sh`

```bash
#!/usr/bin/env bash
# Auto-update semantic index when notes are saved
# This hook runs after note creation/modification

NOTE_PATH="$1"
if [ -z "$NOTE_PATH" ]; then
    exit 0
fi

# Only index markdown files
if [[ "$NOTE_PATH" != *.md ]]; then
    exit 0
fi

# Don't index files in Meta/
if [[ "$NOTE_PATH" == Meta/* ]]; then
    exit 0
fi

# Quick incremental index
python3 .claude/plugins/semantic-search/index.py 2>/dev/null &
```

### 7. New Skill: `/reindex-semantic`

Location: `skills/reindex-semantic/SKILL.md`

```markdown
---
name: reindex-semantic
description: >
  Rebuild the semantic search index. Use when new notes aren't being found,
  after bulk imports, or when the index seems stale.
  Triggers: "reindex semantic", "rebuild embeddings", "update search index",
  "semantic index rebuild", "refresh embeddings".
---

# Reindex Semantic Search

Rebuilds the vector embeddings index for semantic search.

## When to Run

- After importing many notes
- When new notes aren't appearing in semantic search
- After installing the semantic search feature
- Monthly maintenance (optional)

## Usage

- `/reindex-semantic` - Incremental update (only changed notes)
- `/reindex-semantic full` - Full rebuild (all notes)

## Process

1. Scan all markdown notes in vault
2. Compare content hashes to detect changes
3. Generate embeddings for new/changed notes
4. Update the vector database

## Output

Reports number of notes indexed and skipped.
```

## Installation Steps

1. Copy `semantic-seeker.md` to `agents/`
2. Create directory `.claude/plugins/semantic-search/`
3. Copy Python scripts to that directory
4. Copy hook to `.claude/hooks/post-note-save.sh`
5. Copy skill to `skills/reindex-semantic/SKILL.md`
6. Add to `references/agents-registry.md`
7. Update `CLAUDE.md` dispatcher routing

## Dependencies

```bash
pip install sentence-transformers sqlite-vec
```

## Notes

- First index may take a few minutes for large vaults
- Incremental updates are fast (only changed notes)
- Embeddings stored locally, no cloud API calls after initial model download
- Model size: ~80MB (downloaded once)
