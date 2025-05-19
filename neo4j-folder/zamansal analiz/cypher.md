## 5.5 Zamansal Analiz


### 5.5.1 Zamansal Analiz Sorguları

#### 1. Yıllara Göre Varlık Türleri Dağılımı

```cypher
-- Yıllara göre varlık türlerinin dağılımı
MATCH (t:Thesis)-[:CONTAINS]->(n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
AND t.year IS NOT NULL
RETURN t.year AS year,
       LABELS(n)[0] AS entity_type,
       COUNT(n) AS frequency
ORDER BY year, entity_type
```

#### 2. Pandemi Öncesi vs Sonrası Genel Karşılaştırma

```cypher
-- Pandemi öncesi (2019 ve öncesi) vs sonrası (2020 ve sonrası) karşılaştırması
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
WITH n, [thesis_id IN n.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
WITH n, t,
     CASE WHEN t.year <= 2019 THEN 'Pre-pandemic' ELSE 'Pandemic' END AS period
WITH n, period, COUNT(t) AS frequency
WHERE frequency > 0
RETURN LABELS(n)[0] AS entity_type,
       period,
       COUNT(n) AS unique_entities,
       SUM(frequency) AS total_frequency,
       AVG(frequency) AS avg_frequency_per_entity
ORDER BY entity_type, period
```

#### 3. En Çok Büyüyen ve Küçülen Kavramlar

```cypher
-- Pandemi öncesi vs sonrası en çok değişim gösteren kavramlar
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
WITH n, [thesis_id IN n.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
WITH n,
     SUM(CASE WHEN t.year <= 2019 THEN 1 ELSE 0 END) AS pre_pandemic,
     SUM(CASE WHEN t.year >= 2020 THEN 1 ELSE 0 END) AS pandemic
WHERE pre_pandemic > 0 AND pandemic > 0
WITH n, pre_pandemic, pandemic,
     (pandemic * 1.0 / pre_pandemic) AS growth_ratio,
     (pandemic - pre_pandemic) AS absolute_change
RETURN LABELS(n)[0] AS entity_type,
       n.name AS entity_name,
       pre_pandemic,
       pandemic,
       absolute_change,
       growth_ratio
ORDER BY growth_ratio DESC
LIMIT 20
```

#### 4. Yıl Bazında En Popüler Kavramlar

```cypher
-- Her yıl için en popüler 5 kavram
MATCH (t:Thesis)-[:CONTAINS]->(n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
AND t.year IS NOT NULL
AND t.year >= 2010 AND t.year <= 2025
WITH t.year AS year, n, COUNT(*) AS frequency
ORDER BY year, frequency DESC
WITH year, COLLECT({entity: n, frequency: frequency})[0..5] AS top_entities
RETURN year,
       [entity IN top_entities | {
           name: entity.entity.name,
           type: LABELS(entity.entity)[0],
           frequency: entity.frequency
       }] AS top_entities_info
ORDER BY year
```

#### 5. İlişki Türlerinin Zamansal Değişimi

```cypher
-- İlişki türlerinin yıllara göre dağılımı
MATCH (source)-[r]->(target)
WHERE TYPE(r) <> 'CONTAINS'
WITH r, [thesis_id IN r.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
RETURN t.year AS year,
       TYPE(r) AS relation_type,
       COUNT(r) AS relation_count
ORDER BY year, relation_type
```

#### 6. Belirli Kavramların Zaman Serisi Analizi

```cypher
-- Önemli kavramların yıllara göre değişimi
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
AND n.name IN ['distance education', 'student motivation', 'online learning', 
               'technology integration in education', 'learning management system',
               'pandemic', 'covid-19', 'emergency remote teaching',
               'hybrid education', 'digital competency']
WITH n, [thesis_id IN n.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
RETURN n.name AS concept,
       LABELS(n)[0] AS entity_type,
       t.year AS year,
       COUNT(*) AS frequency
ORDER BY concept, year
```

#### 7. Dönemsel Kavramsal Odaklanmalar

```cypher
-- 5 yıllık dönemlere göre kavramsal odaklanmalar
MATCH (t:Thesis)-[:CONTAINS]->(n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
AND t.year IS NOT NULL
WITH t.year AS year, n,
     CASE 
         WHEN t.year <= 2005 THEN '1997-2005'
         WHEN t.year <= 2010 THEN '2006-2010'
         WHEN t.year <= 2015 THEN '2011-2015'
         WHEN t.year <= 2019 THEN '2016-2019'
         ELSE '2020-2025'
     END AS period
WITH period, n, COUNT(*) AS frequency
WHERE frequency >= 3
RETURN period,
       LABELS(n)[0] AS entity_type,
       n.name AS entity_name,
       frequency
ORDER BY period, frequency DESC
```

#### 8. Covid-19 Spesifik Analiz

```cypher
-- Covid-19 döneminde (2020-2025) öne çıkan yeni kavramlar
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
WITH n, [thesis_id IN n.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
WITH n,
     SUM(CASE WHEN t.year <= 2019 THEN 1 ELSE 0 END) AS pre_covid,
     SUM(CASE WHEN t.year >= 2020 THEN 1 ELSE 0 END) AS covid_era
WHERE covid_era >= 5
RETURN LABELS(n)[0] AS entity_type,
       n.name AS entity_name,
       pre_covid,
       covid_era,
       CASE WHEN pre_covid = 0 THEN 'NEW_CONCEPT' 
            ELSE ROUND(covid_era * 1.0 / pre_covid, 2) 
       END AS covid_impact_ratio
ORDER BY covid_era DESC
LIMIT 30
```

#### 9. Yıllık Tez Sayısı ve Varlık Yoğunluğu

```cypher
-- Yıllara göre tez sayısı ve ortalama varlık sayısı
MATCH (t:Thesis)
WHERE t.year IS NOT NULL
WITH t.year AS year, COUNT(t) AS thesis_count
MATCH (thesis:Thesis {year: year})-[:CONTAINS]->(n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
WITH year, thesis_count, COUNT(n) AS total_entities
RETURN year,
       thesis_count,
       total_entities,
       ROUND(total_entities * 1.0 / thesis_count, 2) AS avg_entities_per_thesis
ORDER BY year
```

#### 10. Teknoloji Kavramlarının Gelişimi

```cypher
-- Teknoloji ile ilgili kavramların zamansal gelişimi
MATCH (n)
WHERE n:STAKEHOLDER OR n:PROBLEM_CHALLENGE OR n:SOLUTION_APPROACH OR n:FOCUS_AREA_THEME
AND (n.name CONTAINS 'technology' OR n.name CONTAINS 'digital' OR 
     n.name CONTAINS 'online' OR n.name CONTAINS 'virtual' OR
     n.name CONTAINS 'mobile' OR n.name CONTAINS 'artificial intelligence' OR
     n.name CONTAINS 'AI' OR n.name CONTAINS 'machine learning')
WITH n, [thesis_id IN n.thesis_ids | thesis_id] AS thesis_list
UNWIND thesis_list AS thesis_id
MATCH (t:Thesis {id: thesis_id})
WHERE t.year IS NOT NULL
RETURN t.year AS year,
       LABELS(n)[0] AS entity_type,
       n.name AS technology_concept,
       COUNT(*) AS frequency
ORDER BY year, frequency DESC
```
