## 5.2 Merkezilik Analizleri - Derece Merkeziliği

1. Graph Projection Oluşturma (Eğer Mevcut Değilse)

```cypher
-- Önce mevcut projection'ı kontrol et
CALL gds.graph.exists('thesis_network') YIELD exists
```

Eğer projection mevcut değilse:

```cypher
-- Graph projection oluşturma
CALL gds.graph.project('thesis_network',
    ['STAKEHOLDER', 'PROBLEM_CHALLENGE', 'SOLUTION_APPROACH', 'FOCUS_AREA_THEME'],
    ['USES', 'FACES', 'REQUIRES', 'ADDRESSES', 'ENHANCES', 'HINDERS', 'EXAMINES'],
    {
        relationshipProperties: 'weight'
    }
)
```

### 2. Derece Merkeziliği Hesaplama

```cypher
-- Genel derece merkeziliği (tüm varlıklar için top 20)
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name,
       labels(gds.util.asNode(nodeId))[0] AS entity_type,
       score AS degree_centrality
ORDER BY score DESC
LIMIT 20
```

### 3. Varlık Türlerine Göre Derece Merkeziliği

```cypher
-- STAKEHOLDER - Top 10
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:STAKEHOLDER
RETURN node.name AS name,
       'STAKEHOLDER' AS entity_type,
       score AS degree_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- PROBLEM_CHALLENGE - Top 10
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:PROBLEM_CHALLENGE
RETURN node.name AS name,
       'PROBLEM_CHALLENGE' AS entity_type,
       score AS degree_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- SOLUTION_APPROACH - Top 10
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:SOLUTION_APPROACH
RETURN node.name AS name,
       'SOLUTION_APPROACH' AS entity_type,
       score AS degree_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- FOCUS_AREA_THEME - Top 10
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:FOCUS_AREA_THEME
RETURN node.name AS name,
       'FOCUS_AREA_THEME' AS entity_type,
       score AS degree_centrality
ORDER BY score DESC
LIMIT 10
```

### 4. Varlık Türlerine Göre Derece Merkeziliği İstatistikleri

```cypher
-- Varlık türlerine göre derece merkeziliği dağılım istatistikleri
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
RETURN labels(node)[0] AS entity_type,
       COUNT(node) AS node_count,
       AVG(score) AS avg_degree,
       MIN(score) AS min_degree,
       MAX(score) AS max_degree,
       STDEV(score) AS stdev_degree
ORDER BY avg_degree DESC
```

### 5. Yüksek Derece Merkeziliği Analizi

```cypher
-- Derece merkeziliği > 20 olan varlıklar
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE score > 20
RETURN labels(node)[0] AS entity_type,
       node.name AS name,
       score AS degree_centrality
ORDER BY score DESC
```
