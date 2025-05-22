
## 5.6 Yol Analizi ve İlişki Zincirleri Sorguları

### 5.6.1 Paydaş → Problem → Çözüm Zincirleri

```cypher
-- Paydaş -> Problem -> Çözüm zincirleri
MATCH (s:STAKEHOLDER)-[:FACES]->(p:PROBLEM_CHALLENGE)<-[:ADDRESSES]-(sol:SOLUTION_APPROACH)
RETURN s.name AS stakeholder,
       p.name AS problem,
       sol.name AS solution,
       COUNT(*) AS chain_frequency
ORDER BY chain_frequency DESC
LIMIT 15
```

### 5.6.2 En Kısa Yollar Analizi (Öğrenci Odaklı)

```cypher
-- Öğrenci'den çözümlere en kısa yollar
MATCH path = shortestPath((start:STAKEHOLDER {name: 'student'})-[*..4]-(end:SOLUTION_APPROACH))
WHERE ALL(r IN relationships(path) WHERE TYPE(r) <> 'CONTAINS')
RETURN [node IN nodes(path) | node.name] AS path_nodes,
       [rel IN relationships(path) | TYPE(rel)] AS relationship_types,
       length(path) AS path_length
LIMIT 10
```

### 5.6.3 En Uzun Anlamlı Zincirler

```cypher
-- En uzun anlamlı zincirler
MATCH path = (start)-[*4..6]->(end)
WHERE ALL(r IN relationships(path) WHERE TYPE(r) <> 'CONTAINS')
  AND ALL(n IN nodes(path) WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME)
  AND start <> end
RETURN [node IN nodes(path) | {name: node.name, type: labels(node)[0]}] AS path_details,
       [rel IN relationships(path) | TYPE(rel)] AS relationship_chain,
       length(path) AS chain_length
ORDER BY chain_length DESC
LIMIT 10
```

### 5.6.4 Problem → Çözüm İlişki Zincirleri

```cypher
-- En sık ele alınan problem-çözüm çiftleri
MATCH (p:PROBLEM_CHALLENGE)<-[:ADDRESSES]-(sol:SOLUTION_APPROACH)
RETURN p.name AS problem,
       sol.name AS solution,
       COUNT(*) AS frequency
ORDER BY frequency DESC
LIMIT 20
```

### 5.6.5 Çoklu Yol Analizi (Öğretmen Odaklı)

```cypher
-- Öğretmen'den sorunlara ve çözümlere yollar
MATCH path = (teacher:STAKEHOLDER {name: 'teacher'})-[*2..3]-(target)
WHERE ALL(r IN relationships(path) WHERE TYPE(r) <> 'CONTAINS')
  AND (target:PROBLEM_CHALLENGE OR target:SOLUTION_APPROACH)
RETURN [node IN nodes(path) | {name: node.name, type: labels(node)[0]}] AS path_details,
       [rel IN relationships(path) | TYPE(rel)] AS relationship_chain,
       length(path) AS path_length,
       labels(target)[0] AS target_type
ORDER BY path_length
LIMIT 15
```

### 5.6.6 İlişki Türü Bazında Zincir Analizi

```cypher
-- ENHANCES ilişkisini içeren zincirler
MATCH path = (start)-[:ENHANCES*2..3]->(end)
WHERE start <> end
RETURN [node IN nodes(path) | {name: node.name, type: labels(node)[0]}] AS path_details,
       length(path) AS chain_length
ORDER BY chain_length DESC
LIMIT 10
```
