## 5.4 İlişki Türleri Analizi

İ

### 5.4.1 İlişki Türleri Analizi Sorguları

#### 1. Genel İlişki Türleri Dağılımı

```cypher
-- Genel ilişki türleri istatistikleri
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
RETURN TYPE(r) as relation_type,
       COUNT(r) as count,
       SUM(r.weight) as total_weight,
       AVG(r.weight) as avg_weight,
       MIN(r.weight) as min_weight,
       MAX(r.weight) as max_weight,
       STDEV(r.weight) as stdev_weight
ORDER BY count DESC
```

#### 2. Paydaşların Karşılaştığı Sorunlar (FACES)

```cypher
-- En çok karşılaşılan paydaş-problem ilişkileri
MATCH (s:STAKEHOLDER)-[r:FACES]->(p:PROBLEM_CHALLENGE)
RETURN s.name AS stakeholder,
       p.name AS problem,
       r.weight AS frequency,
       SIZE(r.thesis_ids) AS thesis_count
ORDER BY r.weight DESC
LIMIT 20
```

#### 3. Çözümlerin Ele Aldığı Sorunlar (ADDRESSES)

```cypher
-- En etkili çözüm-problem ilişkileri
MATCH (sol:SOLUTION_APPROACH)-[r:ADDRESSES]->(p:PROBLEM_CHALLENGE)
RETURN sol.name AS solution,
       p.name AS problem,
       r.weight AS frequency,
       SIZE(r.thesis_ids) AS thesis_count
ORDER BY r.weight DESC
LIMIT 20
```

#### 4. Paydaşların Kullandığı Çözümler (USES)

```cypher
-- En yaygın paydaş-çözüm kullanım ilişkileri
MATCH (s:STAKEHOLDER)-[r:USES]->(sol:SOLUTION_APPROACH)
RETURN s.name AS stakeholder,
       sol.name AS solution,
       r.weight AS frequency,
       SIZE(r.thesis_ids) AS thesis_count
ORDER BY r.weight DESC
LIMIT 20
```

#### 5. İlişki Türlerinin Varlık Türleri Arası Dağılımı

```cypher
-- Hangi varlık türleri arasında hangi ilişki türleri var
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN LABELS(source)[0] as source_type,
       TYPE(r) as relation_type,
       LABELS(target)[0] as target_type,
       COUNT(r) as frequency,
       SUM(r.weight) as total_weight,
       AVG(r.weight) as avg_weight
ORDER BY frequency DESC
LIMIT 30
```

#### 6. En Güçlü İlişkiler (Ağırlık Bazında)

```cypher
-- En yüksek ağırlığa sahip ilişkiler
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN source.name AS source_entity,
       LABELS(source)[0] AS source_type,
       TYPE(r) AS relation_type,
       target.name AS target_entity,
       LABELS(target)[0] AS target_type,
       r.weight AS weight
ORDER BY r.weight DESC
LIMIT 25
```

#### 7. İlişki Türleri Performans Analizi

```cypher
-- İlişki türlerinin genel performansı
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
WITH TYPE(r) AS relation_type,
     AVG(r.weight) AS avg_weight,
     MAX(r.weight) AS max_weight,
     COUNT(r) AS relation_count,
     SUM(r.weight) AS total_weight
RETURN relation_type,
       relation_count,
       avg_weight,
       max_weight,
       total_weight,
       (avg_weight * relation_count) AS impact_score
ORDER BY impact_score DESC
```

#### 8. Varlık Türleri Arası İlişki Matrisi

```cypher
-- Varlık türleri arası ilişki yoğunluğu matrisi
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
WITH LABELS(source)[0] AS source_type,
     LABELS(target)[0] AS target_type,
     COUNT(r) AS edge_count,
     SUM(r.weight) AS total_weight
RETURN source_type,
       target_type,
       edge_count,
       total_weight,
       total_weight / edge_count AS avg_weight_per_edge
ORDER BY total_weight DESC
```

#### 9. Yönlü İlişki Analizi

```cypher
-- Belirli ilişki türlerinin yön analizi
MATCH (s:STAKEHOLDER)-[r:ENHANCES]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN s.name AS stakeholder,
       LABELS(target)[0] AS enhanced_type,
       target.name AS enhanced_entity,
       r.weight AS frequency
ORDER BY r.weight DESC
LIMIT 15
```

```cypher
-- HINDERS ilişkileri analizi
MATCH (source)-[r:HINDERS]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN LABELS(source)[0] AS source_type,
       source.name AS source_entity,
       LABELS(target)[0] AS target_type,
       target.name AS target_entity,
       r.weight AS frequency
ORDER BY r.weight DESC
LIMIT 15
```

#### 10. İlişki Zinciri Analizi

```cypher
-- Paydaş -> Problem -> Çözüm zincirlerinin en güçlüleri
MATCH (s:STAKEHOLDER)-[r1:FACES]->(p:PROBLEM_CHALLENGE)<-[r2:ADDRESSES]-(sol:SOLUTION_APPROACH)
RETURN s.name AS stakeholder,
       p.name AS problem,
       sol.name AS solution,
       r1.weight AS faces_weight,
       r2.weight AS addresses_weight,
       (r1.weight + r2.weight) AS total_chain_weight
ORDER BY total_chain_weight DESC
LIMIT 15
```
