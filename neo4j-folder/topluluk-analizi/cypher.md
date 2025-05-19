Evet, şimdi 5.3 Topluluk Analizine geçelim! Bu analiz, uzaktan eğitim bilgi ağında birbiriyle yoğun bağlantılı varlık gruplarını tespit etmek ve tematik odaklanmaları belirlemek için çok önemli.

## 5.3 Topluluk Analizi

Topluluk analizi için aşağıdaki Cypher sorgularını çalıştıralım:

### 1. Louvain Community Detection

```cypher
-- Louvain algoritması ile topluluk tespiti
CALL gds.louvain.stream('thesis_network')
YIELD nodeId, communityId
WITH communityId, COUNT(nodeId) AS community_size,
     COLLECT(gds.util.asNode(nodeId)) AS nodes
WHERE community_size >= 5
RETURN communityId,
       community_size,
       [node IN nodes[0..10] | {name: node.name, type: labels(node)[0]}] AS sample_nodes
ORDER BY community_size DESC
```

### 2. Topluluk İçi Bağlantı Yoğunluğu

```cypher
-- Toplulukların iç bağlantı yoğunluğu analizi
CALL gds.louvain.stream('thesis_network')
YIELD nodeId, communityId
WITH communityId, COLLECT(nodeId) AS community_nodes
WHERE SIZE(community_nodes) >= 5
UNWIND community_nodes AS node1
UNWIND community_nodes AS node2
WITH communityId, node1, node2, community_nodes
WHERE id(node1) < id(node2)
MATCH (n1)-[r]->(n2)
WHERE id(n1) = node1 AND id(n2) = node2
  AND TYPE(r) <> 'CONTAINS'
RETURN communityId,
       SIZE(community_nodes) AS community_size,
       COUNT(r) AS internal_edges,
       (COUNT(r) * 2.0) / (SIZE(community_nodes) * (SIZE(community_nodes) - 1)) AS density
ORDER BY density DESC
```

### 3. En Büyük Toplulukların Detaylı Analizi

```cypher
-- En büyük 10 topluluğun varlık türü dağılımı
CALL gds.louvain.stream('thesis_network')
YIELD nodeId, communityId
WITH communityId, COUNT(nodeId) AS community_size,
     COLLECT(gds.util.asNode(nodeId)) AS nodes
WHERE community_size >= 5
WITH communityId, community_size, nodes
ORDER BY community_size DESC
LIMIT 10
UNWIND nodes AS node
RETURN communityId,
       community_size,
       labels(node)[0] AS entity_type,
       COUNT(node) AS type_count,
       (COUNT(node) * 100.0 / community_size) AS type_percentage
ORDER BY communityId, type_count DESC
```

### 4. Topluluklar Arası Bağlantılar

```cypher
-- Topluluklar arası ilişki analizi
CALL gds.louvain.stream('thesis_network')
YIELD nodeId, communityId
WITH nodeId, communityId
MATCH (source)-[r]->(target)
WHERE id(source) = nodeId
  AND TYPE(r) <> 'CONTAINS'
WITH source, target, communityId AS source_community, r
CALL gds.louvain.stream('thesis_network')
YIELD nodeId AS targetNodeId, communityId AS target_community
WHERE id(target) = targetNodeId AND source_community <> target_community
RETURN source_community,
       target_community,
       COUNT(r) AS edge_count,
       SUM(r.weight) AS total_weight,
       TYPE(r) AS relation_type
ORDER BY total_weight DESC
LIMIT 20
```

### 5. Topluluk Merkezilik Analizi

```cypher
-- Her topluluktaki en merkezi varlıklar
CALL gds.louvain.stream('thesis_network')
YIELD nodeId, communityId
WITH communityId, COUNT(nodeId) AS community_size, COLLECT(nodeId) AS community_nodes
WHERE community_size >= 10
WITH communityId, community_size, community_nodes
ORDER BY community_size DESC
LIMIT 5
UNWIND community_nodes AS nodeId
CALL gds.pageRank.stream('thesis_network', {
    relationshipWeightProperty: 'weight'
})
YIELD nodeId AS prNodeId, score AS pagerank_score
WHERE nodeId = prNodeId
WITH communityId, community_size, gds.util.asNode(nodeId) AS node, pagerank_score
ORDER BY communityId, pagerank_score DESC
RETURN communityId,
       community_size,
       node.name AS node_name,
       labels(node)[0] AS entity_type,
       pagerank_score
LIMIT 50
```
