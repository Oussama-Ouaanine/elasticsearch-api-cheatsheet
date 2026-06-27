<div align="center">

# 🔍 Elasticsearch API Cheat Sheet

**A comprehensive, copy-paste ready reference for Elasticsearch APIs**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Elasticsearch](https://img.shields.io/badge/Elasticsearch-8.x-green?logo=elasticsearch)](https://www.elastic.co/)
[![Made with ❤️](https://img.shields.io/badge/Made%20with-%E2%9D%A4%EF%B8%8F-red)](https://github.com)

</div>

---

## 📖 Table of Contents

- [About](#about)
- [What's Inside](#whats-inside)
- [Quick Start](#quick-start)
- [Browser Access](#browser-access)
- [cURL Commands](#curl-commands)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)
- [License](#license)

---

## 📌 About

This repository contains a **comprehensive Elasticsearch API cheat sheet** designed for:

- 🔧 **DevOps Engineers** managing Elasticsearch clusters
- 🛡️ **SOC Analysts** querying security logs and alerts
- 👨‍💻 **Developers** integrating Elasticsearch into applications
- 📊 **Data Engineers** building search and analytics pipelines
- 🎓 **Students** learning Elasticsearch fundamentals

All commands are tested and ready to use with your local or remote Elasticsearch instance.

---

## 📦 What's Inside

| File | Description |
|------|-------------|
| [`elasticsearch_api_cheat_sheet.md`](elasticsearch_api_cheat_sheet.md) | **The main cheat sheet** — 1,600+ lines covering all major Elasticsearch APIs |

### Covered API Categories

- ✅ **Cluster APIs** — Health, state, stats, nodes, settings
- ✅ **Index APIs** — Create, delete, open, close, aliases, templates, data streams
- ✅ **Document APIs** — Index, get, update, delete, bulk operations
- ✅ **Search APIs** — Match, term, bool, range, aggregations, scroll
- ✅ **Mapping APIs** — Field types, multi-fields, dynamic mapping
- ✅ **Snapshot & Restore** — Backup and recovery operations
- ✅ **Ingest Pipelines** — Data transformation and enrichment
- ✅ **Security APIs** — Users, roles, API keys, authentication
- ✅ **Watcher / Alerting** — Automated monitoring and alerts
- ✅ **Browser Access** — Direct URL endpoints for quick browser checks
- ✅ **cURL Commands** — Ready-to-copy terminal commands

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/elasticsearch-api-cheatsheet.git
cd elasticsearch-api-cheatsheet
```

### 2. Open the Cheat Sheet

```bash
# Open in your default editor
code elasticsearch_api_cheat_sheet.md

# Or view in terminal
cat elasticsearch_api_cheat_sheet.md
```

### 3. Start Using Commands

Replace `https://192.168.8.147:9200` with your actual Elasticsearch endpoint:

```bash
# Check cluster health
curl -k -X GET "https://YOUR_ES_HOST:9200/_cluster/health?pretty"

# List all indices
curl -k -X GET "https://YOUR_ES_HOST:9200/_cat/indices?v"
```

---

## 🌐 Browser Access

Many Elasticsearch APIs can be accessed **directly from your browser** — no terminal needed!

### Quick Browser URLs

| Action | URL |
|--------|-----|
| Cluster Health | `https://YOUR_ES_HOST:9200/_cluster/health?pretty` |
| List Indices | `https://YOUR_ES_HOST:9200/_cat/indices?v` |
| List Nodes | `https://YOUR_ES_HOST:9200/_cat/nodes?v` |
| Disk Allocation | `https://YOUR_ES_HOST:9200/_cat/allocation?v` |
| Index Mapping | `https://YOUR_ES_HOST:9200/INDEX_NAME/_mapping?pretty` |
| Get Document | `https://YOUR_ES_HOST:9200/INDEX_NAME/_doc/ID?pretty` |

> 💡 **Tip**: Bookmark these URLs for one-click access during daily operations.

---

## 🔧 cURL Commands

### Prerequisites

- `curl` installed (comes pre-installed on macOS/Linux, available on Windows)
- Access to your Elasticsearch instance

### Basic Usage

```bash
# Without authentication
curl -k -X GET "https://192.168.8.147:9200/_cluster/health?pretty"

# With authentication
curl -k -u elastic:password -X GET "https://192.168.8.147:9200/_cluster/health?pretty"

# With verbose output (for debugging)
curl -kv -u elastic:password -X GET "https://192.168.8.147:9200/_cluster/health?pretty"
```

### Common Options Explained

| Option | Description |
|--------|-------------|
| `-k` | Skip SSL certificate verification (for self-signed certs) |
| `-u user:pass` | HTTP Basic Authentication |
| `-X METHOD` | HTTP method (GET, POST, PUT, DELETE) |
| `-H 'Content-Type: application/json'` | Set request header |
| `-d '{...}'` | Send JSON data in request body |
| `-o file.json` | Save response to file |

---

## 📁 Repository Structure

```
elasticsearch-api-cheatsheet/
├── README.md                              # This file
├── elasticsearch_api_cheat_sheet.md       # Full cheat sheet (1,600+ lines)
└── LICENSE                                # MIT License
```

---

## 🤝 Contributing

Contributions are welcome! If you find an error or want to add new commands:

1. Fork the repository
2. Create a new branch: `git checkout -b feature/add-new-api`
3. Make your changes
4. Submit a pull request

---

## 📄 License

This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.

---

<div align="center">

**Made with ❤️ for the Elasticsearch community**

⭐ Star this repo if you find it useful!

</div>
