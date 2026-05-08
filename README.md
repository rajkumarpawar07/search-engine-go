# 🔍 Full-Text Search Engine — Go

A lightweight, **full-text search engine** built from scratch in Go. Indexes millions of Wikipedia abstract documents and executes queries in milliseconds using an **inverted index** and **Snowball stemming** — no external databases or search frameworks required.

---

## 📋 Table of Contents

- [Features](#features)
- [Tech Stack](#tech-stack)
- [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Download the Dataset](#download-the-dataset)
  - [Installation](#installation)
  - [Running the Search Engine](#running-the-search-engine)
- [CLI Usage](#cli-usage)
- [Example Output](#example-output)
- [Concepts Explained](#concepts-explained)
- [Contributing](#contributing)
- [License](#license)

---

## ✨ Features

- 🗂️ **Inverted index** — maps each token to the documents it appears in for O(1) lookups
- 🌱 **Snowball stemming** — reduces words to their root form (`running` → `run`) for smarter matching
- 📦 **Wikipedia dataset support** — reads the official Wikipedia abstract dump (`.xml.gz`) out of the box
- ⚡ **Fast indexing & querying** — indexes hundreds of thousands of documents in seconds using Go's standard library
- 🔗 **Zero runtime dependencies** — no databases, no search frameworks, just Go
- 🖥️ **Simple CLI interface** — configurable dump path and query via command-line flags

---

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| [Go 1.23+](https://golang.org/) | Core language |
| [kljensen/snowball](https://github.com/kljensen/snowball) | Snowball stemming algorithm |
| Wikipedia Abstract Dump | Source dataset (`.xml.gz`) |

---

## 🔍 How It Works

The engine processes text in three stages — **tokenization**, **stemming**, and **index lookup**:

```
Wikipedia XML Dump (.xml.gz)
         │
         ▼
  ┌─────────────┐
  │  Load Docs  │  Parse compressed XML → extract title + abstract
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │  Tokenize   │  Split text into individual tokens (words)
  └──────┬──────┘
         │
         ▼
  ┌─────────────┐
  │    Stem     │  Normalize tokens via Snowball (e.g. "running" → "run")
  └──────┬──────┘
         │
         ▼
  ┌─────────────────────┐
  │  Build Inverted     │  token → [doc1_id, doc2_id, ...]
  │  Index              │
  └──────┬──────────────┘
         │
         ▼
  ┌─────────────┐
  │  Query      │  Tokenize + stem query, intersect posting lists
  └──────┬──────┘
         │
         ▼
  Matching document IDs → print results
```

**Inverted Index example:**
```
"wildcat"  → [102, 4471, 89302]
"european" → [102, 774, 4471, 12003]

Query: "European wildcat"
Result: intersection → [102, 4471]
```

---

## 📁 Project Structure

```
search-engine-go/
├── utils/
│   ├── loader.go     # Parses and loads Wikipedia XML dump into documents
│   ├── index.go      # Inverted index construction and search logic
│   └── analyzer.go   # Tokenization, stop-word filtering, and Snowball stemming
├── main.go           # CLI entry point — flags, orchestration, and result output
├── go.mod            # Module definition (text-search-go)
└── go.sum            # Dependency checksums
```

---

## 🚀 Getting Started

### Prerequisites

- [Go 1.23+](https://golang.org/dl/) installed

### Download the Dataset

This engine uses the **Wikipedia Abstract Dump** as its document corpus. Download it from Wikimedia:

```bash
# ~800MB compressed, ~6GB uncompressed
wget https://dumps.wikimedia.org/enwiki/latest/enwiki-latest-abstract1.xml.gz
```

> **Note:** The file is large. Smaller numbered dumps (e.g. `abstract1`) cover a subset of Wikipedia and are faster to index during development.

### Installation

```bash
# Clone the repository
git clone https://github.com/rajkumarpawar07/search-engine-go.git
cd search-engine-go

# Install dependencies
go mod tidy
```

### Running the Search Engine

```bash
go run main.go -p <path-to-dump> -q "<your search query>"
```

**Using the default values** (dump in current directory, query: "European wildcat"):
```bash
go run main.go
```

**Custom dump path and query:**
```bash
go run main.go -p /data/enwiki-latest-abstract1.xml.gz -q "machine learning"
```

---

## 🖥️ CLI Usage

| Flag | Default | Description |
|---|---|---|
| `-p` | `enwiki-latest-abstract1.xml.gz` | Path to the Wikipedia abstract dump file |
| `-q` | `European wildcat` | The full-text search query |

```
Usage of search-engine-go:
  -p string
        wiki abstract dump path (default "enwiki-latest-abstract1.xml.gz")
  -q string
        search query (default "European wildcat")
```

---

## 📊 Example Output

```
2024/01/15 10:22:01 Running Full Text Search
2024/01/15 10:22:07 Loaded 612418 documents in 5.812s
2024/01/15 10:22:09 Indexed 612418 documents in 2.004s
2024/01/15 10:22:09 Search found 3 documents in 290µs

102     The European wildcat (Felis silvestris silvestris) is a small wildcat species...
4471    Wildcat (Felis silvestris) is a small cat native to Europe, western Asia and Africa...
89302   The Scottish wildcat is a population of the European wildcat living in Scotland...
```

> Indexing ~600k documents takes ~2 seconds. Individual queries resolve in **microseconds**.

---

## 🧠 Concepts Explained

**Inverted Index** — Instead of scanning every document for a query term, an inverted index pre-maps each token to the list of document IDs that contain it. Searching then becomes a fast set-intersection operation.

**Snowball Stemming** — A rule-based algorithm that strips word suffixes to find a common root form. This ensures that a query for `"running"` also matches documents containing `"runs"` or `"runner"`, improving recall without a dictionary lookup.

**Stop Word Filtering** — Common words like `"the"`, `"is"`, and `"and"` are excluded from the index since they appear everywhere and add no discriminating value to search results.

---

## 🤝 Contributing

Contributions are welcome! Here's how to get started:

```bash
# Fork the repo, then:
git clone https://github.com/<your-username>/search-engine-go.git
cd search-engine-go
git checkout -b feature/your-feature-name
```

Ideas for improvements:
- TF-IDF ranking for better result ordering
- BM25 scoring algorithm
- HTTP API to expose search over a REST endpoint
- Support for custom document corpora beyond Wikipedia

After making changes:
```bash
go test ./...       # Run tests
go vet ./...        # Check for issues
```

Then open a Pull Request.

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

---

<p align="center">Built with ❤️ using Go — no frameworks, no databases, just the fundamentals.</p>
