# 🔍 Elasticsearch API Cheat Sheet
## Instance: https://192.168.8.147:9200

---

## 📋 Table of Contents
1. [Cluster APIs](#cluster-apis)
2. [Index APIs](#index-apis)
3. [Document APIs](#document-apis)
4. [Search APIs](#search-apis)
5. [Mapping APIs](#mapping-apis)
6. [Snapshot & Restore](#snapshot--restore)
7. [Ingest Pipelines](#ingest-pipelines)
8. [Security APIs](#security-apis)
9. [Watcher / Alerting](#watcher--alerting)
10. [Browser / Direct URL Access](#browser--direct-url-access)
11. [Useful cURL Commands](#useful-curl-commands)

---

## 🔗 Base URL
```
https://192.168.8.147:9200
```

> ⚠️ **Note**: If authentication is enabled, add `-u username:password` to all cURL commands.

---

## 1. Cluster APIs

### Check Cluster Health
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/health?pretty"
```

### Cluster Health (with wait)
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/health?wait_for_status=yellow&timeout=30s&pretty"
```

### Cluster State
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/state?pretty"
```

### Cluster Stats
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/stats?pretty"
```

### List Nodes
```bash
curl -X GET "https://192.168.8.147:9200/_cat/nodes?v"
```

### Node Info
```bash
curl -X GET "https://192.168.8.147:9200/_nodes?pretty"
```

### Node Stats
```bash
curl -X GET "https://192.168.8.147:9200/_nodes/stats?pretty"
```

### Cluster Settings
```bash
# Get current settings
curl -X GET "https://192.168.8.147:9200/_cluster/settings?pretty"

# Get settings including defaults
curl -X GET "https://192.168.8.147:9200/_cluster/settings?include_defaults=true&pretty"

# Update transient setting
curl -X PUT "https://192.168.8.147:9200/_cluster/settings" -H 'Content-Type: application/json' -d'
{
  "transient": {
    "cluster.routing.allocation.disk.watermark.low": "85%"
  }
}'

# Update persistent setting
curl -X PUT "https://192.168.8.147:9200/_cluster/settings" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.max_shards_per_node": 1000
  }
}'
```

### Pending Tasks
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/pending_tasks?pretty"
```

### Reroute Shards
```bash
curl -X POST "https://192.168.8.147:9200/_cluster/reroute?pretty" -H 'Content-Type: application/json' -d'
{
  "commands": [
    {
      "move": {
        "index": "my_index",
        "shard": 0,
        "from_node": "node1",
        "to_node": "node2"
      }
    }
  ]
}'
```

---

## 2. Index APIs

### List All Indices
```bash
curl -X GET "https://192.168.8.147:9200/_cat/indices?v"
```

### List Indices (with size, docs count)
```bash
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&s=index"
```

### List Indices (detailed)
```bash
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&health=yellow"
```

### Get Index Info
```bash
curl -X GET "https://192.168.8.147:9200/my_index?pretty"
```

### Create Index
```bash
curl -X PUT "https://192.168.8.147:9200/my_index" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  }
}'
```

### Create Index with Mapping
```bash
curl -X PUT "https://192.168.8.147:9200/my_index" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "title": { "type": "text" },
      "created_at": { "type": "date" },
      "views": { "type": "integer" }
    }
  }
}'
```

### Delete Index
```bash
curl -X DELETE "https://192.168.8.147:9200/my_index"
```

### Delete Multiple Indices
```bash
curl -X DELETE "https://192.168.8.147:9200/index1,index2,index3"
```

### Delete by Pattern (wildcard)
```bash
curl -X DELETE "https://192.168.8.147:9200/logstash-*"
```

### Open/Close Index
```bash
# Close index
curl -X POST "https://192.168.8.147:9200/my_index/_close"

# Open index
curl -X POST "https://192.168.8.147:9200/my_index/_open"
```

### Refresh Index
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_refresh"
```

### Flush Index
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_flush"
```

### Force Merge
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_forcemerge?max_num_segments=1"
```

### Index Stats
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_stats?pretty"
```

### Index Segments
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_segments?pretty"
```

### Index Recovery
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_recovery?pretty"
```

### Shrink Index
```bash
# First set to read-only
curl -X PUT "https://192.168.8.147:9200/my_index/_settings" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.blocks.write": true
  }
}'

# Then shrink
curl -X POST "https://192.168.8.147:9200/my_index/_shrink/my_index_shrunk" -H 'Content-Type: application/json' -d'
{
  "settings": {
    "index.number_of_shards": 1,
    "index.number_of_replicas": 1
  }
}'
```

### Clone Index
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_clone/my_index_clone"
```

### Reindex
```bash
curl -X POST "https://192.168.8.147:9200/_reindex?pretty" -H 'Content-Type: application/json' -d'
{
  "source": {
    "index": "old_index"
  },
  "dest": {
    "index": "new_index"
  }
}'
```

### Reindex with Query
```bash
curl -X POST "https://192.168.8.147:9200/_reindex?pretty" -H 'Content-Type: application/json' -d'
{
  "source": {
    "index": "old_index",
    "query": {
      "match": {
        "status": "active"
      }
    }
  },
  "dest": {
    "index": "new_index"
  }
}'
```

### Update Index Settings
```bash
curl -X PUT "https://192.168.8.147:9200/my_index/_settings" -H 'Content-Type: application/json' -d'
{
  "index": {
    "number_of_replicas": 2,
    "refresh_interval": "30s"
  }
}'
```

### Get Index Settings
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_settings?pretty"
```

### Index Aliases
```bash
# Add alias
curl -X POST "https://192.168.8.147:9200/_aliases" -H 'Content-Type: application/json' -d'
{
  "actions": [
    { "add": { "index": "my_index_v1", "alias": "my_index" } }
  ]
}'

# Remove alias
curl -X POST "https://192.168.8.147:9200/_aliases" -H 'Content-Type: application/json' -d'
{
  "actions": [
    { "remove": { "index": "my_index_v1", "alias": "my_index" } }
  ]
}'

# Swap alias (atomic)
curl -X POST "https://192.168.8.147:9200/_aliases" -H 'Content-Type: application/json' -d'
{
  "actions": [
    { "remove": { "index": "my_index_v1", "alias": "my_index" } },
    { "add": { "index": "my_index_v2", "alias": "my_index" } }
  ]
}'

# List aliases
curl -X GET "https://192.168.8.147:9200/_cat/aliases?v"
```

### Index Templates (Legacy)
```bash
# Create template
curl -X PUT "https://192.168.8.147:9200/_template/my_template" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["logs-*"],
  "settings": {
    "number_of_shards": 1
  },
  "mappings": {
    "properties": {
      "timestamp": { "type": "date" }
    }
  }
}'

# Get template
curl -X GET "https://192.168.8.147:9200/_template/my_template?pretty"

# Delete template
curl -X DELETE "https://192.168.8.147:9200/_template/my_template"
```

### Index Templates (Composable - v7.8+)
```bash
# Create composable template
curl -X PUT "https://192.168.8.147:9200/_index_template/my_template" -H 'Content-Type: application/json' -d'
{
  "index_patterns": ["logs-*"],
  "template": {
    "settings": {
      "number_of_shards": 1
    },
    "mappings": {
      "properties": {
        "timestamp": { "type": "date" }
      }
    }
  },
  "priority": 500,
  "composed_of": ["component_template_1"],
  "version": 1,
  "_meta": {
    "description": "My log template"
  }
}'

# Get composable template
curl -X GET "https://192.168.8.147:9200/_index_template/my_template?pretty"

# Delete composable template
curl -X DELETE "https://192.168.8.147:9200/_index_template/my_template"
```

### Component Templates
```bash
# Create component template
curl -X PUT "https://192.168.8.147:9200/_component_template/my_component" -H 'Content-Type: application/json' -d'
{
  "template": {
    "settings": {
      "number_of_replicas": 1
    }
  },
  "version": 1,
  "_meta": {
    "description": "My component template"
  }
}'
```

### Data Streams
```bash
# Create data stream
curl -X PUT "https://192.168.8.147:9200/_data_stream/my_data_stream"

# Get data stream info
curl -X GET "https://192.168.8.147:9200/_data_stream/my_data_stream?pretty"

# List data streams
curl -X GET "https://192.168.8.147:9200/_data_stream?pretty"

# Delete data stream
curl -X DELETE "https://192.168.8.147:9200/_data_stream/my_data_stream"
```

---

## 3. Document APIs

### Index a Document (with auto-generated ID)
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_doc" -H 'Content-Type: application/json' -d'
{
  "title": "Hello World",
  "views": 100
}'
```

### Index a Document (with specific ID)
```bash
curl -X PUT "https://192.168.8.147:9200/my_index/_doc/1" -H 'Content-Type: application/json' -d'
{
  "title": "Hello World",
  "views": 100
}'
```

### Index with Create (fail if exists)
```bash
curl -X PUT "https://192.168.8.147:9200/my_index/_create/1" -H 'Content-Type: application/json' -d'
{
  "title": "Hello World"
}'
```

### Get Document by ID
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_doc/1?pretty"
```

### Get Document Source Only
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_source/1?pretty"
```

### Check if Document Exists
```bash
curl -I "https://192.168.8.147:9200/my_index/_doc/1"
```

### Update Document
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_update/1" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "views": 200
  }
}'
```

### Update with Script
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_update/1" -H 'Content-Type: application/json' -d'
{
  "script": {
    "source": "ctx._source.views += params.increment",
    "lang": "painless",
    "params": {
      "increment": 10
    }
  }
}'
```

### Upsert (Update or Insert)
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_update/1" -H 'Content-Type: application/json' -d'
{
  "doc": {
    "title": "Updated Title",
    "views": 1
  },
  "doc_as_upsert": true
}'
```

### Delete Document
```bash
curl -X DELETE "https://192.168.8.147:9200/my_index/_doc/1"
```

### Bulk Operations
```bash
curl -X POST "https://192.168.8.147:9200/_bulk" -H 'Content-Type: application/json' -d'
{ "index" : { "_index" : "my_index", "_id" : "1" } }
{ "title" : "Doc 1", "views": 100 }
{ "index" : { "_index" : "my_index", "_id" : "2" } }
{ "title" : "Doc 2", "views": 200 }
{ "update" : { "_index" : "my_index", "_id" : "1" } }
{ "doc" : { "views" : 150 } }
{ "delete" : { "_index" : "my_index", "_id" : "2" } }
'
```

### Multi-Get (mget)
```bash
curl -X GET "https://192.168.8.147:9200/_mget?pretty" -H 'Content-Type: application/json' -d'
{
  "docs": [
    { "_index": "my_index", "_id": "1" },
    { "_index": "my_index", "_id": "2" }
  ]
}'
```

### Delete by Query
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_delete_by_query" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "status": "deleted"
    }
  }
}'
```

### Update by Query
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_update_by_query" -H 'Content-Type: application/json' -d'
{
  "script": {
    "source": "ctx._source.views++",
    "lang": "painless"
  },
  "query": {
    "match_all": {}
  }
}'
```

---

## 4. Search APIs

### Basic Search
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match_all": {}
  }
}'
```

### Search with Size
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 10,
  "query": {
    "match_all": {}
  }
}'
```

### Search with Pagination
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "from": 0,
  "size": 10,
  "query": {
    "match_all": {}
  }
}'
```

### Match Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "hello world"
    }
  }
}'
```

### Term Query (exact match)
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "term": {
      "status": "active"
    }
  }
}'
```

### Bool Query (must, should, must_not, filter)
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "hello" } }
      ],
      "filter": [
        { "term": { "status": "active" } },
        { "range": { "views": { "gte": 100 } } }
      ],
      "must_not": [
        { "match": { "title": "spam" } }
      ],
      "should": [
        { "match": { "title": "world" } }
      ]
    }
  }
}'
```

### Range Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "range": {
      "created_at": {
        "gte": "2024-01-01",
        "lte": "2024-12-31",
        "format": "yyyy-MM-dd"
      }
    }
  }
}'
```

### Wildcard Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "wildcard": {
      "title": "hel*"
    }
  }
}'
```

### Prefix Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "prefix": {
      "title": "hel"
    }
  }
}'
```

### Exists Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "exists": {
      "field": "title"
    }
  }
}'
```

### Multi-Match Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "multi_match": {
      "query": "hello",
      "fields": ["title^3", "description"]
    }
  }
}'
```

### Query String Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "query_string": {
      "query": "(hello OR world) AND active",
      "fields": ["title", "description"]
    }
  }
}'
```

### Aggregations
```bash
# Simple aggregation
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "by_status": {
      "terms": {
        "field": "status"
      }
    }
  }
}'
```

### Nested Aggregations
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "by_status": {
      "terms": {
        "field": "status"
      },
      "aggs": {
        "avg_views": {
          "avg": {
            "field": "views"
          }
        }
      }
    }
  }
}'
```

### Date Histogram Aggregation
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 0,
  "aggs": {
    "over_time": {
      "date_histogram": {
        "field": "created_at",
        "calendar_interval": "day"
      }
    }
  }
}'
```

### Sorting
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "sort": [
    { "created_at": "desc" },
    { "views": "desc" }
  ],
  "query": {
    "match_all": {}
  }
}'
```

### Source Filtering
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "_source": ["title", "views"],
  "query": {
    "match_all": {}
  }
}'
```

### Highlighting
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "hello"
    }
  },
  "highlight": {
    "fields": {
      "title": {}
    }
  }
}'
```

### Multi-Search (msearch)
```bash
curl -X GET "https://192.168.8.147:9200/_msearch?pretty" -H 'Content-Type: application/json' -d'
{}
{ "query": { "match_all": {} } }
{"index": "my_index"}
{ "query": { "match": { "title": "hello" } } }
'
```

### Count API
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_count?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "status": "active"
    }
  }
}'
```

### Explain API
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_explain/1" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "hello"
    }
  }
}'
```

### Validate Query
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_validate/query?explain=true&pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "match": {
      "title": "hello"
    }
  }
}'
```

### Scroll API (for large result sets)
```bash
# Initial scroll request
curl -X GET "https://192.168.8.147:9200/my_index/_search?scroll=1m&pretty" -H 'Content-Type: application/json' -d'
{
  "size": 100,
  "query": {
    "match_all": {}
  }
}'

# Subsequent scroll requests (use scroll_id from previous response)
curl -X POST "https://192.168.8.147:9200/_search/scroll?pretty" -H 'Content-Type: application/json' -d'
{
  "scroll": "1m",
  "scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ=="
}'

# Clear scroll
curl -X DELETE "https://192.168.8.147:9200/_search/scroll" -H 'Content-Type: application/json' -d'
{
  "scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAAAD4WYm9laVYtZndUQlNsdDcwakFMNjU1QQ=="
}'
```

### Search After (better than scroll for real-time)
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "size": 10,
  "query": {
    "match_all": {}
  },
  "sort": [
    { "created_at": "asc" },
    { "_id": "asc" }
  ],
  "search_after": ["2024-01-01T00:00:00Z", "doc_id_123"]
}'
```

---

## 5. Mapping APIs

### Get Mapping
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_mapping?pretty"
```

### Get Field Mapping
```bash
curl -X GET "https://192.168.8.147:9200/my_index/_mapping/field/title?pretty"
```

### Put Mapping (add new fields)
```bash
curl -X PUT "https://192.168.8.147:9200/my_index/_mapping" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "new_field": {
      "type": "keyword"
    }
  }
}'
```

### Common Field Types
```json
{
  "properties": {
    "text_field": { "type": "text" },
    "keyword_field": { "type": "keyword" },
    "integer_field": { "type": "integer" },
    "long_field": { "type": "long" },
    "float_field": { "type": "float" },
    "double_field": { "type": "double" },
    "boolean_field": { "type": "boolean" },
    "date_field": { "type": "date" },
    "object_field": { "type": "object" },
    "nested_field": { "type": "nested" },
    "geo_point_field": { "type": "geo_point" },
    "ip_field": { "type": "ip" },
    "completion_field": { "type": "completion" }
  }
}
```

### Multi-Field Mapping
```bash
curl -X PUT "https://192.168.8.147:9200/my_index/_mapping" -H 'Content-Type: application/json' -d'
{
  "properties": {
    "title": {
      "type": "text",
      "fields": {
        "keyword": {
          "type": "keyword"
        }
      }
    }
  }
}'
```

---

## 6. Snapshot & Restore

### Create Snapshot Repository
```bash
curl -X PUT "https://192.168.8.147:9200/_snapshot/my_backup" -H 'Content-Type: application/json' -d'
{
  "type": "fs",
  "settings": {
    "location": "/mnt/backup",
    "compress": true
  }
}'
```

### List Repositories
```bash
curl -X GET "https://192.168.8.147:9200/_snapshot?pretty"
```

### Create Snapshot
```bash
curl -X PUT "https://192.168.8.147:9200/_snapshot/my_backup/snapshot_1?wait_for_completion=true&pretty" -H 'Content-Type: application/json' -d'
{
  "indices": "my_index",
  "ignore_unavailable": true,
  "include_global_state": false
}'
```

### Get Snapshot Info
```bash
curl -X GET "https://192.168.8.147:9200/_snapshot/my_backup/snapshot_1?pretty"
```

### List All Snapshots
```bash
curl -X GET "https://192.168.8.147:9200/_snapshot/my_backup/_all?pretty"
```

### Restore Snapshot
```bash
curl -X POST "https://192.168.8.147:9200/_snapshot/my_backup/snapshot_1/_restore?pretty" -H 'Content-Type: application/json' -d'
{
  "indices": "my_index",
  "ignore_unavailable": true,
  "include_global_state": false,
  "rename_pattern": "my_index",
  "rename_replacement": "restored_my_index"
}'
```

### Delete Snapshot
```bash
curl -X DELETE "https://192.168.8.147:9200/_snapshot/my_backup/snapshot_1"
```

### Delete Repository
```bash
curl -X DELETE "https://192.168.8.147:9200/_snapshot/my_backup"
```

### Snapshot Status
```bash
curl -X GET "https://192.168.8.147:9200/_snapshot/_status?pretty"
```

---

## 7. Ingest Pipelines

### Create Pipeline
```bash
curl -X PUT "https://192.168.8.147:9200/_ingest/pipeline/my_pipeline" -H 'Content-Type: application/json' -d'
{
  "description": "My ingest pipeline",
  "processors": [
    {
      "set": {
        "field": "processed_at",
        "value": "{{_ingest.timestamp}}"
      }
    },
    {
      "lowercase": {
        "field": "email"
      }
    }
  ]
}'
```

### Get Pipeline
```bash
curl -X GET "https://192.168.8.147:9200/_ingest/pipeline/my_pipeline?pretty"
```

### Delete Pipeline
```bash
curl -X DELETE "https://192.168.8.147:9200/_ingest/pipeline/my_pipeline"
```

### Simulate Pipeline
```bash
curl -X POST "https://192.168.8.147:9200/_ingest/pipeline/_simulate?pretty" -H 'Content-Type: application/json' -d'
{
  "pipeline": {
    "processors": [
      {
        "set": {
          "field": "processed",
          "value": true
        }
      }
    ]
  },
  "docs": [
    {
      "_source": {
        "message": "test message"
      }
    }
  ]
}'
```

### Index with Pipeline
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_doc?pipeline=my_pipeline" -H 'Content-Type: application/json' -d'
{
  "message": "Hello World"
}'
```

---

## 8. Security APIs

### Check Authentication
```bash
curl -X GET "https://192.168.8.147:9200/_security/_authenticate?pretty" -u elastic:password
```

### Create User
```bash
curl -X POST "https://192.168.8.147:9200/_security/user/my_user" -H 'Content-Type: application/json' -d'
{
  "password": "my_password",
  "roles": ["superuser"],
  "full_name": "My User"
}'
```

### Get User
```bash
curl -X GET "https://192.168.8.147:9200/_security/user/my_user?pretty"
```

### List Users
```bash
curl -X GET "https://192.168.8.147:9200/_security/user?pretty"
```

### Delete User
```bash
curl -X DELETE "https://192.168.8.147:9200/_security/user/my_user"
```

### Create Role
```bash
curl -X PUT "https://192.168.8.147:9200/_security/roles/my_role" -H 'Content-Type: application/json' -d'
{
  "cluster": ["monitor"],
  "indices": [
    {
      "names": ["my_index*"],
      "privileges": ["read", "view_index_metadata"]
    }
  ]
}'
```

### Get Role
```bash
curl -X GET "https://192.168.8.147:9200/_security/role/my_role?pretty"
```

### List Roles
```bash
curl -X GET "https://192.168.8.147:9200/_security/role?pretty"
```

### Create API Key
```bash
curl -X POST "https://192.168.8.147:9200/_security/api_key" -H 'Content-Type: application/json' -d'
{
  "name": "my_api_key",
  "expiration": "1d"
}'
```

### Invalidate API Key
```bash
curl -X DELETE "https://192.168.8.147:9200/_security/api_key" -H 'Content-Type: application/json' -d'
{
  "id": "VnVhQ2ZHY0JDZGJrU..."
}'
```

### Enable/Disable User
```bash
# Disable
curl -X PUT "https://192.168.8.147:9200/_security/user/my_user/_disable"

# Enable
curl -X PUT "https://192.168.8.147:9200/_security/user/my_user/_enable"
```

### Change Password
```bash
curl -X POST "https://192.168.8.147:9200/_security/user/my_user/_password" -H 'Content-Type: application/json' -d'
{
  "password": "new_password"
}'
```

---

## 9. Watcher / Alerting

### Create Watch
```bash
curl -X PUT "https://192.168.8.147:9200/_watcher/watch/my_watch" -H 'Content-Type: application/json' -d'
{
  "trigger": {
    "schedule": {
      "interval": "1m"
    }
  },
  "input": {
    "search": {
      "request": {
        "indices": ["my_index"],
        "body": {
          "query": {
            "match_all": {}
          }
        }
      }
    }
  },
  "condition": {
    "compare": {
      "ctx.payload.hits.total": {
        "gt": 0
      }
    }
  },
  "actions": {
    "log": {
      "logging": {
        "text": "Found {{ctx.payload.hits.total}} documents"
      }
    }
  }
}'
```

### Get Watch
```bash
curl -X GET "https://192.168.8.147:9200/_watcher/watch/my_watch?pretty"
```

### Delete Watch
```bash
curl -X DELETE "https://192.168.8.147:9200/_watcher/watch/my_watch"
```

### Execute Watch
```bash
curl -X PUT "https://192.168.8.147:9200/_watcher/watch/my_watch/_execute?pretty"
```

### Acknowledge Watch
```bash
curl -X PUT "https://192.168.8.147:9200/_watcher/watch/my_watch/_ack/action_id"
```

### Watch History
```bash
curl -X GET "https://192.168.8.147:9200/.watcher-history*/_search?pretty" -H 'Content-Type: application/json' -d'
{
  "query": {
    "term": {
      "watch_id": "my_watch"
    }
  }
}'
```

---

## 10. 🌐 Browser / Direct URL Access

You can access many Elasticsearch APIs directly from your browser by simply visiting the URLs. This is great for quick checks without needing cURL or command-line tools.

> 💡 **Tip**: Bookmark these URLs for quick access during daily operations.

### Cluster Health
```
https://192.168.8.147:9200/_cluster/health?pretty
```

### Cluster State
```
https://192.168.8.147:9200/_cluster/state?pretty
```

### Cluster Stats
```
https://192.168.8.147:9200/_cluster/stats?pretty
```

### Cluster Settings
```
https://192.168.8.147:9200/_cluster/settings?pretty
https://192.168.8.147:9200/_cluster/settings?include_defaults=true&pretty
```

### List All Indices
```
https://192.168.8.147:9200/_cat/indices?v
```

### List Indices (sorted by size, descending)
```
https://192.168.8.147:9200/_cat/indices?v&s=store.size:desc
```

### List Indices (sorted by name)
```
https://192.168.8.147:9200/_cat/indices?v&s=index
```

### List Red Indices Only
```
https://192.168.8.147:9200/_cat/indices?v&health=red
```

### List Yellow Indices Only
```
https://192.168.8.147:9200/_cat/indices?v&health=yellow
```

### List Nodes
```
https://192.168.8.147:9200/_cat/nodes?v
```

### List Nodes (detailed)
```
https://192.168.8.147:9200/_cat/nodes?v&h=name,heap.percent,ram.percent,cpu,load_1m,disk.used_percent
```

### List Shards
```
https://192.168.8.147:9200/_cat/shards?v
```

### Shards for Specific Index
```
https://192.168.8.147:9200/_cat/shards/my_index?v
```

### List Aliases
```
https://192.168.8.147:9200/_cat/aliases?v
```

### List Templates (Legacy)
```
https://192.168.8.147:9200/_cat/templates?v
```

### List Composable Index Templates
```
https://192.168.8.147:9200/_index_template?pretty
```

### List Component Templates
```
https://192.168.8.147:9200/_component_template?pretty
```

### List Data Streams
```
https://192.168.8.147:9200/_data_stream?pretty
```

### Disk Allocation
```
https://192.168.8.147:9200/_cat/allocation?v
```

### Document Count (all indices)
```
https://192.168.8.147:9200/_cat/count?v
```

### Document Count (specific index)
```
https://192.168.8.147:9200/_cat/count/my_index?v
```

### Recovery Status
```
https://192.168.8.147:9200/_cat/recovery?v
```

### Pending Tasks
```
https://192.168.8.147:9200/_cat/pending_tasks?v
```

### Thread Pool Stats
```
https://192.168.8.147:9200/_cat/thread_pool?v
```

### Fielddata Memory Usage
```
https://192.168.8.147:9200/_cat/fielddata?v
```

### Segment Info
```
https://192.168.8.147:9200/_cat/segments?v
```

### Segments for Specific Index
```
https://192.168.8.147:9200/_cat/segments/my_index?v
```

### Master Node
```
https://192.168.8.147:9200/_cat/master?v
```

### Installed Plugins
```
https://192.168.8.147:9200/_cat/plugins?v
```

### Snapshot Repositories
```
https://192.168.8.147:9200/_snapshot?pretty
```

### Snapshots in a Repository
```
https://192.168.8.147:9200/_cat/snapshots/my_repo?v
```

### Ingest Pipelines
```
https://192.168.8.147:9200/_ingest/pipeline?pretty
```

### List Users (Security)
```
https://192.168.8.147:9200/_security/user?pretty
```

### List Roles (Security)
```
https://192.168.8.147:9200/_security/role?pretty
```

### Get Specific Index Info
```
https://192.168.8.147:9200/my_index?pretty
```

### Get Index Settings
```
https://192.168.8.147:9200/my_index/_settings?pretty
```

### Get Index Mapping
```
https://192.168.8.147:9200/my_index/_mapping?pretty
```

### Get Index Stats
```
https://192.168.8.147:9200/my_index/_stats?pretty
```

### Get Index Segments
```
https://192.168.8.147:9200/my_index/_segments?pretty
```

### Get Document by ID
```
https://192.168.8.147:9200/my_index/_doc/1?pretty
```

### Get Document Source Only
```
https://192.168.8.147:9200/my_index/_source/1?pretty
```

### Node Info
```
https://192.168.8.147:9200/_nodes?pretty
```

### Node Stats
```
https://192.168.8.147:9200/_nodes/stats?pretty
```

### Node Stats (specific node)
```
https://192.168.8.147:9200/_nodes/node_name/stats?pretty
```

### Watcher Stats
```
https://192.168.8.147:9200/_watcher/stats?pretty
```

### Task Management
```
https://192.168.8.147:9200/_tasks?pretty
```

### Pending Cluster Tasks
```
https://192.168.8.147:9200/_cluster/pending_tasks?pretty
```

### License Info
```
https://192.168.8.147:9200/_license?pretty
```

### X-Pack Info
```
https://192.168.8.147:9200/_xpack?pretty
```

### Features Info
```
https://192.168.8.147:9200/_xpack/usage?pretty
```

---

### 📝 Browser URL Parameters Cheat Sheet

| Parameter | Description | Example |
|-----------|-------------|---------|
| `?pretty` | Pretty-print JSON | `/_cluster/health?pretty` |
| `?v` | Verbose headers (for _cat) | `/_cat/indices?v` |
| `?s=column` | Sort by column | `/_cat/indices?v&s=index` |
| `?s=column:desc` | Sort descending | `/_cat/indices?v&s=store.size:desc` |
| `?h=col1,col2` | Show only specific columns | `/_cat/nodes?v&h=name,heap.percent` |
| `?format=json` | Output as JSON | `/_cat/indices?format=json` |
| `?health=red` | Filter by health | `/_cat/indices?v&health=red` |
| `?bytes=b` | Show sizes in bytes | `/_cat/indices?v&bytes=b` |
| `?time=ms` | Show time in milliseconds | `/_cat/recovery?v&time=ms` |

---

### ⚠️ Browser Access Notes

1. **Authentication**: If security is enabled, your browser will prompt for username/password, or you can embed credentials in the URL (not recommended for production):
   ```
   https://elastic:password@192.168.8.147:9200/_cluster/health?pretty
   ```

2. **SSL Warnings**: Since you're using `https://` with a local IP, your browser will show a certificate warning. Click **Advanced** → **Proceed** to continue.

3. **Firefox/Chrome Extensions**: Consider installing JSON viewer extensions for better formatting of `?pretty` responses.

4. **Bookmark Folder**: Create a bookmark folder in your browser with all these URLs for one-click access.

---

## 11. Useful cURL Commands

### Quick Health Check
```bash
curl -k -X GET "https://192.168.8.147:9200/_cluster/health?pretty"
```

> Use `-k` flag to skip SSL certificate verification if using self-signed certificates.

### With Authentication
```bash
curl -k -u elastic:your_password -X GET "https://192.168.8.147:9200/_cat/indices?v"
```

### With Verbose Output
```bash
curl -kv -u elastic:password -X GET "https://192.168.8.147:9200/"
```

### With Timeout
```bash
curl -k --max-time 10 -u elastic:password -X GET "https://192.168.8.147:9200/_cluster/health?pretty"
```

### Pretty Print JSON Response
```bash
curl -k -u elastic:password -X GET "https://192.168.8.147:9200/_cluster/health" | python -m json.tool
```

### Save Response to File
```bash
curl -k -u elastic:password -X GET "https://192.168.8.147:9200/_cluster/health?pretty" -o cluster_health.json
```

### Using HTTPie (alternative to cURL)
```bash
http -a elastic:password GET https://192.168.8.147:9200/_cluster/health --verify=no
```

---

## 📊 _cat API Endpoints (Human-Readable)

| Endpoint | Description |
|----------|-------------|
| `/_cat/indices?v` | List all indices |
| `/_cat/indices?v&health=red` | List red indices |
| `/_cat/nodes?v` | List nodes |
| `/_cat/shards?v` | List shards |
| `/_cat/shards/my_index?v` | Shards for specific index |
| `/_cat/aliases?v` | List aliases |
| `/_cat/templates?v` | List templates |
| `/_cat/health?v` | Cluster health (short) |
| `/_cat/allocation?v` | Disk allocation |
| `/_cat/recovery?v` | Recovery status |
| `/_cat/pending_tasks?v` | Pending tasks |
| `/_cat/thread_pool?v` | Thread pool stats |
| `/_cat/fielddata?v` | Fielddata memory usage |
| `/_cat/segments?v` | Segment info |
| `/_cat/count?v` | Document count |
| `/_cat/count/my_index?v` | Count for specific index |
| `/_cat/master?v` | Master node |
| `/_cat/plugins?v` | Installed plugins |
| `/_cat/snapshots/my_repo?v` | Snapshot info |

### Common _cat Parameters
```bash
# Sort by column
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&s=index"

# Reverse sort
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&s=index:desc"

# Filter columns
curl -X GET "https://192.168.8.147:9200/_cat/indices?h=index,docs.count,store.size&v"

# JSON output
curl -X GET "https://192.168.8.147:9200/_cat/indices?format=json&pretty"
```

---

## 🛠️ Common Tasks

### Find Large Indices
```bash
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&s=store.size:desc&h=index,store.size,docs.count"
```

### Find Unassigned Shards
```bash
curl -X GET "https://192.168.8.147:9200/_cat/shards?v&h=index,shard,prirep,state,unassigned.reason"
```

### Clear Cache
```bash
curl -X POST "https://192.168.8.147:9200/my_index/_cache/clear"
```

### Index Stats (docs, size)
```bash
curl -X GET "https://192.168.8.147:9200/_cat/indices?v&h=index,docs.count,store.size,pri.store.size"
```

### Check Disk Usage
```bash
curl -X GET "https://192.168.8.147:9200/_cat/allocation?v"
```

### Get Cluster Settings (all)
```bash
curl -X GET "https://192.168.8.147:9200/_cluster/settings?include_defaults=true&pretty"
```

---

## 🔐 SSL/TLS Notes

Since your instance uses `https://`, you may need to:

1. **Skip certificate verification** (for self-signed certs):
   ```bash
   curl -k https://192.168.8.147:9200
   ```

2. **Provide CA certificate**:
   ```bash
   curl --cacert /path/to/ca.crt https://192.168.8.147:9200
   ```

3. **Provide client certificate** (if mTLS is enabled):
   ```bash
   curl --cert client.crt --key client.key https://192.168.8.147:9200
   ```

---

*Generated for: https://192.168.8.147:9200*
*Elasticsearch API Cheat Sheet*
