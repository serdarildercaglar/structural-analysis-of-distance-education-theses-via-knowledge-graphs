## 5.2.4 PageRank Analizi

PageRank analizi için aşağıdaki Cypher sorgularını çalıştıralım:

### 1. Genel PageRank Analizi

```cypher
-- Genel PageRank (tüm varlıklar için top 20)
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name,
       labels(gds.util.asNode(nodeId))[0] AS entity_type,
       score AS pagerank_score
ORDER BY score DESC
LIMIT 20
```

### 2. Varlık Türlerine Göre PageRank

```cypher
-- STAKEHOLDER - Top 10
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:STAKEHOLDER
RETURN node.name AS name,
       'STAKEHOLDER' AS entity_type,
       score AS pagerank_score
ORDER BY score DESC
LIMIT 10
```

```cypher
-- PROBLEM_CHALLENGE - Top 10
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:PROBLEM_CHALLENGE
RETURN node.name AS name,
       'PROBLEM_CHALLENGE' AS entity_type,
       score AS pagerank_score
ORDER BY score DESC
LIMIT 10
```

```cypher
-- SOLUTION_APPROACH - Top 10
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:SOLUTION_APPROACH
RETURN node.name AS name,
       'SOLUTION_APPROACH' AS entity_type,
       score AS pagerank_score
ORDER BY score DESC
LIMIT 10
```

```cypher
-- FOCUS_AREA_THEME - Top 10
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:FOCUS_AREA_THEME
RETURN node.name AS name,
       'FOCUS_AREA_THEME' AS entity_type,
       score AS pagerank_score
ORDER BY score DESC
LIMIT 10
```

### 3. Varlık Türlerine Göre PageRank İstatistikleri

```cypher
-- Varlık türlerine göre PageRank dağılım istatistikleri
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
RETURN labels(node)[0] AS entity_type,
       COUNT(node) AS node_count,
       AVG(score) AS avg_pagerank,
       MIN(score) AS min_pagerank,
       MAX(score) AS max_pagerank,
       STDEV(score) AS stdev_pagerank
ORDER BY avg_pagerank DESC
```

### 4. Yüksek PageRank Analizi

```cypher
-- PageRank > 1.0 olan varlıklar (meaningful threshold)
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE score > 1.0
RETURN labels(node)[0] AS entity_type,
       node.name AS name,
       score AS pagerank_score
ORDER BY score DESC
```

### 5. Dört Merkezilik Türünün Karşılaştırması

```cypher
-- Top 15 varlık için dört merkezilik türünün karşılaştırması
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score AS degree_score
WITH nodeId, degree_score
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId AS nodeId2, score AS betweenness_score
WHERE nodeId = nodeId2
WITH nodeId, degree_score, betweenness_score
CALL gds.closeness.stream('thesis_network')
YIELD nodeId AS nodeId3, score AS closeness_score
WHERE nodeId = nodeId3
WITH nodeId, degree_score, betweenness_score, closeness_score
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId AS nodeId4, score AS pagerank_score
WHERE nodeId = nodeId4
WITH gds.util.asNode(nodeId) AS node, degree_score, betweenness_score, closeness_score, pagerank_score
ORDER BY pagerank_score DESC
RETURN node.name AS name,
       labels(node)[0] AS entity_type,
       degree_score AS degree_centrality,
       betweenness_score AS betweenness_centrality,
       closeness_score AS closeness_centrality,
       pagerank_score AS pagerank_score
LIMIT 15
```
