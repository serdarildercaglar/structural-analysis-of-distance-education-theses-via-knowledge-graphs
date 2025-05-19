Anlaşıldı! Sonraki analiz için **Arasındalık Merkeziliği (Betweenness Centrality)** ile devam edelim. Entity ve relation kategorilerini İngilizce kullanacağım.

## 5.2.2 Arasındalık Merkeziliği Analizi

Arasındalık merkeziliği için aşağıdaki Cypher sorgularını çalıştıralım:

### 1. Genel Arasındalık Merkeziliği

```cypher
-- Genel arasındalık merkeziliği (tüm varlıklar için top 20)
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
RETURN gds.util.asNode(nodeId).name AS name,
       labels(gds.util.asNode(nodeId))[0] AS entity_type,
       score AS betweenness_centrality
ORDER BY score DESC
LIMIT 20
```

### 2. Varlık Türlerine Göre Arasındalık Merkeziliği

```cypher
-- STAKEHOLDER - Top 10
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:STAKEHOLDER
RETURN node.name AS name,
       'STAKEHOLDER' AS entity_type,
       score AS betweenness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- PROBLEM_CHALLENGE - Top 10
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:PROBLEM_CHALLENGE
RETURN node.name AS name,
       'PROBLEM_CHALLENGE' AS entity_type,
       score AS betweenness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- SOLUTION_APPROACH - Top 10
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:SOLUTION_APPROACH
RETURN node.name AS name,
       'SOLUTION_APPROACH' AS entity_type,
       score AS betweenness_centrality
ORDER BY score DESC
LIMIT 10
```

```cypher
-- FOCUS_AREA_THEME - Top 10
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE node:FOCUS_AREA_THEME
RETURN node.name AS name,
       'FOCUS_AREA_THEME' AS entity_type,
       score AS betweenness_centrality
ORDER BY score DESC
LIMIT 10
```

### 3. Varlık Türlerine Göre Arasındalık Merkeziliği İstatistikleri

```cypher
-- Varlık türlerine göre arasındalık merkeziliği dağılım istatistikleri
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
RETURN labels(node)[0] AS entity_type,
       COUNT(node) AS node_count,
       AVG(score) AS avg_betweenness,
       MIN(score) AS min_betweenness,
       MAX(score) AS max_betweenness,
       STDEV(score) AS stdev_betweenness
ORDER BY avg_betweenness DESC
```

### 4. Yüksek Arasındalık Merkeziliği Analizi

```cypher
-- Arasındalık merkeziliği > 0.01 olan varlıklar (meaningful threshold)
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId, score
WITH gds.util.asNode(nodeId) AS node, score
WHERE score > 0.01
RETURN labels(node)[0] AS entity_type,
       node.name AS name,
       score AS betweenness_centrality
ORDER BY score DESC
```

### 5. Derece vs Arasındalık Merkeziliği Karşılaştırması

```cypher
-- Top 20 varlık için derece ve arasındalık merkeziliği karşılaştırması
CALL gds.degree.stream('thesis_network')
YIELD nodeId, score AS degree_score
WITH nodeId, degree_score
CALL gds.betweenness.stream('thesis_network')
YIELD nodeId AS nodeId2, score AS betweenness_score
WITH nodeId, degree_score, betweenness_score
WHERE nodeId = nodeId2
WITH gds.util.asNode(nodeId) AS node, degree_score, betweenness_score
ORDER BY betweenness_score DESC
RETURN node.name AS name,
       labels(node)[0] AS entity_type,
       degree_score AS degree_centrality,
       betweenness_score AS betweenness_centrality,
       (betweenness_score / degree_score) AS betweenness_per_degree
LIMIT 20
```

Bu sorguları sırasıyla çalıştırıp sonuçlarını JSON formatında paylaşır mısın? Her sorgunun sonucunu hangi sorguya ait olduğunu belirterek göndereceğin için analiz metnini daha doğru yazabilirim.
