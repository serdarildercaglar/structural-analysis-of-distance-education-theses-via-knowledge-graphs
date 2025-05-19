## 5.2.3 Yakınlık Merkeziliği Analizi

1. Genel Yakınlık Merkeziliği

```cypher
-- Genel yakınlık merkeziliği (tüm varlıklar için top 20)
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name,
       labels(gds.util.asNode(nodeId))[0] AS entity_type,
       score AS closeness_centrality
ORDER BY score DESC
LIMIT 20
```

### 2. Varlık Türlerine Göre Yakınlık Merkeziliği

```cypher
-- STAKEHOLDER - Top 10
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:STAKEHOLDER
RETURN node.name AS name,
       'STAKEHOLDER' AS entity_type,
       score AS closeness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- PROBLEM_CHALLENGE - Top 10
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:PROBLEM_CHALLENGE
RETURN node.name AS name,
       'PROBLEM_CHALLENGE' AS entity_type,
       score AS closeness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- SOLUTION_APPROACH - Top 10
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:SOLUTION_APPROACH
RETURN node.name AS name,
       'SOLUTION_APPROACH' AS entity_type,
       score AS closeness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- FOCUS_AREA_THEME - Top 10
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:FOCUS_AREA_THEME
RETURN node.name AS name,
       'FOCUS_AREA_THEME' AS entity_type,
       score AS closeness_centrality
ORDER BY score DESC
LIMIT 10
```

### 3. Varlık Türlerine Göre Yakınlık Merkeziliği İstatistikleri

```cypher
-- Varlık türlerine göre yakınlık merkeziliği dağılım istatistikleri
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
RETURN labels(node)[0] AS entity_type,
       COUNT(node) AS node_count,
       AVG(score) AS avg_closeness,
       MIN(score) AS min_closeness,
       MAX(score) AS max_closeness,
       STDEV(score) AS stdev_closeness
ORDER BY avg_closeness DESC
```

### 4. Yüksek Yakınlık Merkeziliği Analizi

```cypher
-- Yakınlık merkeziliği > 0.3 olan varlıklar (meaningful threshold)
CALL gds.closeness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE score > 0.3
RETURN labels(node)[0] AS entity_type,
       node.name AS name,
       score AS closeness_centrality
ORDER BY score DESC
```

### 5. Üç Merkezilik Türünün Karşılaştırması

```cypher
-- Top 20 varlık için derece, arasındalık ve yakınlık merkeziliği karşılaştırması
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
WITH gds.util.asNode(nodeId) AS node, degree_score, betweenness_score, closeness_score
ORDER BY closeness_score DESC
RETURN node.name AS name,
       labels(node)[0] AS entity_type,
       degree_score AS degree_centrality,
       betweenness_score AS betweenness_centrality,
       closeness_score AS closeness_centrality
LIMIT 20
```
