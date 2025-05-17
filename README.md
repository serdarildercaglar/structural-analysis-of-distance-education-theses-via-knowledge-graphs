# structural-analysis-of-distance-education-theses-via-knowledge-graphs

Structural analysis of 703 Turkish distance education theses (1997–2025) via GPT-assisted entity-relation extraction and Neo4j-based knowledge graph construction and analysis.

# Neo4j Docker Command

```
docker run -it --rm \
  --publish=7474:7474 --publish=7687:7687 \
  --user="$(id -u):$(id -g)" \
  --volume=$HOME/neo4j/licenses:/licenses \
  --env NEO4J_AUTH=neo4j/12345678 \
  --env NEO4J_PLUGINS='["graph-data-science", "apoc"]' \
  --env NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
  --env NEO4J_gds_enterprise_license__file=/licenses/gds \
  neo4j:enterprise
```

```
docker run -it \
  --name neo4j-interactive \
  -p 7474:7474 -p 7687:7687 \
  -v $HOME/neo4j/data:/data \
  -v $HOME/neo4j/logs:/logs \
  -v $HOME/neo4j/import:/import \
  -v $HOME/neo4j/plugins:/plugins \
  -v $HOME/neo4j/licenses:/licenses \
  --env NEO4J_AUTH=neo4j/12345678 \
  --env NEO4J_PLUGINS='["graph-data-science", "apoc"]' \
  --env NEO4J_ACCEPT_LICENSE_AGREEMENT=yes \
  neo4j:enterprise
```

Container yeniden başlatıldıktan sonra, APOC'un yüklendiğini doğrulamak için Neo4j browser'da şu sorguyu çalıştırabilirsiniz:

``CALL apoc.help('apoc');``

neo4j browser link: http://127.0.0.1:7474/browser/

Kullanıcı adı: neo4j

şifre: 12345678
