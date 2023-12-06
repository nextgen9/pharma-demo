# Elasticsearch Step-by-Step Guide with Pharma Dataset

## Step 1: Install and Start Elasticsearch

Make sure you have Elasticsearch installed. Download it from the [official Elasticsearch website](https://www.elastic.co/downloads/elasticsearch) and follow the installation instructions for your operating system.

Start Elasticsearch:

```bash
./bin/elasticsearch

Step 2: Install and Start Kibana (Optional)
Kibana is a powerful visualization tool for Elasticsearch. Download it from the official Kibana website.

Start Kibana:

./bin/kibana

Step 3: Indexing Pharma Dataset
Create a sample JSON file, pharma_data.json, with your pharmaceutical dataset. Ensure each document has a unique identifier (e.g., "id").

Example pharma_data.json:
{"id": 1, "name": "Medicine A", "category": "Pain Relief", "price": 20.99, "manufacturer": "PharmaCorp"}
{"id": 2, "name": "Medicine B", "category": "Antibiotics", "price": 15.49, "manufacturer": "HealthMeds"}
{"id": 3, "name": "Medicine C", "category": "Allergy", "price": 12.99, "manufacturer": "BioCare"}
# Add more data as needed

Step 4: Index Data into Elasticsearch
Use the following curl command to index the data into Elasticsearch:
curl -X POST "localhost:9200/pharma_index/_doc/1" -H 'Content-Type: application/json' -d @pharma_data.json

Step 5: Basic Queries
Match Query:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "match": {
      "name": "Medicine A"
    }
  }
}'

Filter Query:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "bool": {
      "filter": {
        "range": {
          "price": {"gte": 15.00}
        }
      }
    }
  }
}'

Bool Query:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "query": {
    "bool": {
      "must": [
        { "match": { "category": "Pain Relief" }},
        { "range": { "price": { "lte": 20.00 }}}
      ]
    }
  }
}'

Step 6: Generate Reports
Top Selling Medicines:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "size": 5,
  "sort": {"sales": {"order": "desc"}}
}'

Average Price by Manufacturer:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "avg_price_by_manufacturer": {
      "terms": {"field": "manufacturer.keyword"},
      "aggs": {"average_price": {"avg": {"field": "price"}}}
    }
  }
}'

Medicines in Stock (Filtering by Availability):
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "query": {"term": {"availability": true}}
}'

Category-wise Sales Percentage:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "category_sales": {
      "terms": {"field": "category.keyword"},
      "aggs": {"sales_percentage": {"avg": {"field": "sales"}}}
    }
  }
}'

Price Range Analysis:
curl -X GET "localhost:9200/pharma_index/_search" -H 'Content-Type: application/json' -d '{
  "size": 0,
  "aggs": {
    "price_ranges": {
      "range": {"field": "price", "ranges": [{"to": 10}, {"from": 10, "to": 20}, {"from": 20}]}
    }
  }
}'
