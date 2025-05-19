
## 5.1 Betimsel Ağ İstatistikleri - Cypher Sorguları

### 1. Temel Ağ Metrikleri

```cypher
-- Toplam düğüm sayısı (entity'ler)
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
RETURN COUNT(n) as total_nodes
```

```cypher
-- Toplam kenar sayısı (CONTAINS hariç)
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
RETURN COUNT(r) as total_edges
```

```cypher
-- Ağ yoğunluğu hesaplama
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
WITH COUNT(n) as node_count
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
WITH node_count, COUNT(r) as edge_count
RETURN edge_count, node_count,
       (edge_count * 2.0) / (node_count * (node_count - 1)) as network_density
```

```cypher
-- Ortalama derece
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
OPTIONAL MATCH (n)-[r_out]->()
WHERE TYPE(r_out) <> 'CONTAINS'
OPTIONAL MATCH (n)<-[r_in]-()
WHERE TYPE(r_in) <> 'CONTAINS'
WITH n, COUNT(DISTINCT r_out) as out_degree, COUNT(DISTINCT r_in) as in_degree
RETURN AVG(out_degree + in_degree) as average_degree
```

### 2. Varlık Türleri Dağılımı

```cypher
-- Varlık türlerinin sayısal dağılımı
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
RETURN LABELS(n)[0] as entity_type,
       COUNT(n) as count,
       (COUNT(n) * 100.0 / 3140) as percentage
ORDER BY count DESC
```

```cypher
-- Varlık türlerinin tezlere göre dağılımı
MATCH (t:Thesis)-[:CONTAINS]->(n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
RETURN LABELS(n)[0] as entity_type,
       COUNT(DISTINCT t) as thesis_count,
       COUNT(n) as total_occurrences,
       (COUNT(n) * 1.0 / COUNT(DISTINCT t)) as avg_per_thesis
ORDER BY thesis_count DESC
```

```cypher
-- En sık geçen varlıklar (top 10 her kategori için)
MATCH (n:STAKEHOLDER)
RETURN 'STAKEHOLDER' as category, n.name as entity_name, n.frequency
ORDER BY n.frequency DESC
LIMIT 10

UNION ALL

MATCH (n:PROBLEM_CHALLENGE)
RETURN 'PROBLEM_CHALLENGE' as category, n.name as entity_name, n.frequency
ORDER BY n.frequency DESC
LIMIT 10

UNION ALL

MATCH (n:SOLUTION_APPROACH)
RETURN 'SOLUTION_APPROACH' as category, n.name as entity_name, n.frequency
ORDER BY n.frequency DESC
LIMIT 10

UNION ALL

MATCH (n:FOCUS_AREA_THEME)
RETURN 'FOCUS_AREA_THEME' as category, n.name as entity_name, n.frequency
ORDER BY n.frequency DESC
LIMIT 10
```

### 3. İlişki Türleri Dağılımı

```cypher
-- İlişki türlerinin genel dağılımı
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
RETURN TYPE(r) as relation_type,
       COUNT(r) as count,
       SUM(r.weight) as total_weight,
       AVG(r.weight) as avg_weight,
       MIN(r.weight) as min_weight,
       MAX(r.weight) as max_weight
ORDER BY count DESC
```

```cypher
-- Varlık türleri arası ilişki matrisi
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN LABELS(source)[0] as source_type,
       TYPE(r) as relation_type,
       LABELS(target)[0] as target_type,
       COUNT(r) as frequency,
       SUM(r.weight) as total_weight
ORDER BY frequency DESC
LIMIT 30
```

```cypher
-- Thesis sayısı analizi
MATCH (t:Thesis)
RETURN COUNT(t) as total_thesis_count,
       MIN(t.year) as earliest_year,
       MAX(t.year) as latest_year
```

Mükemmel! Dosya isimlerinden hangi sorguya ait olduklarını anlayabiliyorum. Şu ana kadar aldığımız sonuçlar çok değerli. Ancak daha kapsamlı bir analiz için aşağıdaki eksik sorguların sonuçlarına da ihtiyacım var:

## Eksik Sorgular:

```cypher
-- İlişki türlerinin genel dağılımı
MATCH ()-[r]->()
WHERE TYPE(r) <> 'CONTAINS'
RETURN TYPE(r) as relation_type,
       COUNT(r) as count,
       SUM(r.weight) as total_weight,
       AVG(r.weight) as avg_weight,
       MIN(r.weight) as min_weight,
       MAX(r.weight) as max_weight
ORDER BY count DESC
```

```cypher
-- Varlık türleri arası ilişki matrisi
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
RETURN LABELS(source)[0] as source_type,
       TYPE(r) as relation_type,
       LABELS(target)[0] as target_type,
       COUNT(r) as frequency,
       SUM(r.weight) as total_weight
ORDER BY frequency DESC
LIMIT 30
```

```cypher
-- Thesis sayısı analizi
MATCH (t:Thesis)
RETURN COUNT(t) as total_thesis_count,
       MIN(t.year) as earliest_year,
       MAX(t.year) as latest_year
```

