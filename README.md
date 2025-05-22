# Structural Analysis of Distance Education Theses via Knowledge Graphs

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Neo4j](https://img.shields.io/badge/Neo4j-5.x-green.svg)](https://neo4j.com/)

A comprehensive structural analysis of 703 Turkish distance education theses (1997–2025) using GPT-assisted entity-relation extraction and Neo4j-based knowledge graph construction and analysis.

## 📋 Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Methodology](#methodology)
- [Installation](#installation)
- [Usage](#usage)
- [Analysis Types](#analysis-types)
- [Project Structure](#project-structure)
- [Key Findings](#key-findings)
- [Technologies Used](#technologies-used)
- [Contributing](#contributing)
- [Citation](#citation)
- [License](#license)

## 🌟 Overview

This project presents a novel approach to analyzing the conceptual landscape of distance education research through knowledge graph construction and network analysis. By processing 703 Turkish distance education theses spanning nearly three decades (1997-2025), we extract and analyze entities and relationships to uncover research trends, problem-solution patterns, and stakeholder interactions.

### Research Questions Addressed

1. What are the most frequently discussed stakeholders, problems/challenges, solutions/approaches, and focus areas in distance education theses?
2. Which concepts have the highest centrality measures in the distance education knowledge network?
3. What relationship patterns exist between stakeholders' problems and solution approaches?
4. How have conceptual focuses in distance education evolved over time, particularly pre/post-COVID-19?
5. What are the community structures within the distance education knowledge network?

## 🎯 Key Features

- **Large-scale Analysis**: Comprehensive analysis of 703 theses over 28 years
- **AI-Powered Extraction**: GPT-4.1 assisted entity-relation extraction
- **Multi-dimensional Analysis**: Network centrality, community detection, temporal analysis
- **Rich Visualizations**: Interactive Neo4j visualizations and Python-generated plots
- **Reproducible Research**: Complete methodology with Cypher queries and Python notebooks

## 🔬 Methodology

### 1. Data Collection
- **Source**: Turkish National Thesis Database (YÖK Tez Merkezi)
- **Keywords**: "uzaktan eğitim", "çevrimiçi öğrenme", "e-öğrenme", "açık öğretim"
- **Corpus**: 703 thesis abstracts (1997-2025)

### 2. Entity-Relation Extraction
- **Tool**: OpenAI GPT-4.1 (temperature=0.3)
- **Entity Categories**: STAKEHOLDER, PROBLEM_CHALLENGE, SOLUTION_APPROACH, FOCUS_AREA_THEME
- **Relation Types**: USES, FACES, REQUIRES, ADDRESSES, ENHANCES, HINDERS, EXAMINES

### 3. Knowledge Graph Construction
- **Database**: Neo4j Graph Database
- **Nodes**: 3,140 entities with properties (frequency, thesis_ids, temporal data)
- **Edges**: 8,913 relationships with weights and metadata

### 4. Network Analysis
- **Centrality Measures**: Degree, Betweenness, Closeness, PageRank
- **Community Detection**: Louvain algorithm
- **Temporal Analysis**: Pre/post-pandemic comparison
- **Path Analysis**: Stakeholder-problem-solution chains

## 🚀 Installation

### Prerequisites
- Python 3.8+
- Docker (for Neo4j)
- Git

### 1. Clone Repository
```bash
git clone https://github.com/serdarildercaglar/structural-analysis-of-distance-education-theses-via-knowledge-graphs.git
cd structural-analysis-of-distance-education-theses-via-knowledge-graphs
```

### 2. Install Python Dependencies
```bash
pip install -r requirements.txt
```

### 3. Setup Neo4j Database

#### Option 1: Neo4j Enterprise (with GDS)
```bash
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

#### Option 2: Neo4j Community (basic functionality)
```bash
docker run -it \
  --name neo4j-community \
  -p 7474:7474 -p 7687:7687 \
  -v $HOME/neo4j/data:/data \
  --env NEO4J_AUTH=neo4j/12345678 \
  --env NEO4J_PLUGINS='["apoc"]' \
  neo4j:latest
```

### 4. Verify Installation
- Access Neo4j Browser: http://127.0.0.1:7474/browser/
- Login: `neo4j` / `12345678`
- Verify APOC: `CALL apoc.help('apoc');`

## 📊 Usage

### 1. Entity-Relation Extraction
```bash
# Run the extraction notebook
jupyter notebook entity-rel-extraction-openai.ipynb
```

### 2. Load Data to Neo4j
```cypher
// Load entities and relationships from JSON files
// (Refer to individual cypher.md files for specific queries)
```

### 3. Run Analyses
Navigate to specific analysis folders and run the Jupyter notebooks:

```bash
cd neo4j-folder/betimsel-ağ-analizleri/
jupyter notebook dev-1.ipynb
```

## 📈 Analysis Types

### 1. Descriptive Network Statistics
- **Network Density**: 0.0018 (sparse network indicating conceptual diversity)
- **Average Degree**: 5.677 connections per entity
- **Node Distribution**: 44.8% solutions, 27.1% problems, 14.9% focus areas, 13.2% stakeholders

### 2. Centrality Analysis
- **Degree Centrality**: Identifies most connected entities
- **Betweenness Centrality**: Finds bridge concepts connecting different areas
- **Closeness Centrality**: Measures information propagation efficiency
- **PageRank**: Determines overall influence in the network

### 3. Community Detection
- **Communities Found**: 1,107 communities using Louvain algorithm
- **Largest Community**: 329 members (Technology Integration & Digital Competency)
- **Community Structure**: Power-law distribution with few large, many small communities

### 4. Temporal Analysis
- **Pre-pandemic vs. Pandemic**: Dramatic increase in research volume post-2020
- **Emerging Concepts**: Teacher attitudes, student participation, digital competency
- **Growth Patterns**: 27x increase in "classroom teacher" mentions during pandemic

### 5. Relationship Analysis
- **Most Common Relations**: ENHANCES (1,955), FACES (1,702), ADDRESSES (1,606)
- **Problem-Solution Chains**: Student motivation → Gamification approaches
- **Stakeholder Interactions**: Students face motivation problems most frequently

### 6. Path Analysis
- **Longest Chains**: 4-6 step paths from research themes to solutions
- **Common Patterns**: Thesis → Theme → Distance Education → Student → Technology
- **Critical Paths**: Student → Problem → Solution pathways

## 📁 Project Structure

```
├── data/
│   ├── entity_database.json           # Standardized entity database
│   ├── thesis_results-final/
│   │   └── thesis_results.json        # Raw thesis data with extracted entities
│   └── thesis_results-final.zip       # Compressed thesis data
├── entity-rel-extraction-openai.ipynb # GPT-based extraction workflow
├── neo4j-folder/                      # Neo4j analysis results
│   ├── betimsel-ağ-analizleri/        # Descriptive network statistics
│   ├── Merkezilik analizleri/         # Centrality analysis
│   │   ├── derece merkeziliği/        # Degree centrality
│   │   ├── arasındalık merkeziliği/   # Betweenness centrality
│   │   ├── yakınlık merkeziliği/      # Closeness centrality
│   │   └── pagerank analizi/          # PageRank analysis
│   ├── ilişki türleri analizi/        # Relationship type analysis
│   ├── topluluk-analizi/              # Community detection
│   ├── Yol Analizi ve İlişki Zincirleri/ # Path analysis
│   └── zamansal analiz/               # Temporal analysis
├── requirements.txt                   # Python dependencies
├── LICENSE                           # MIT License
└── README.md                         # This file
```

### File Types Explanation
- **cypher.md**: Neo4j Cypher queries for each analysis
- **dev-1.ipynb**: Python notebooks with visualization code
- **\*.json**: Query results exported from Neo4j
- **\*.png**: Generated visualization images

## 🔍 Key Findings

### Network Structure
- The distance education field exhibits a **hub-and-spoke** structure with "distance education" as the central concept
- **Solution-oriented** field: 44.8% of entities are solution approaches
- **Sparse but connected**: Low density (0.0018) indicates conceptual diversity

### Temporal Evolution
- **COVID-19 Impact**: Dramatic research volume increase (2021: 194 theses vs. historical average: 25/year)
- **Paradigm Shift**: From technology-focused to socio-pedagogical concerns
- **New Stakeholders**: Parents and elementary teachers emerged as key actors

### Problem-Solution Patterns
- **Student motivation** is the most critical challenge (53 mentions)
- **Gamification** and **ARCS Model** are primary motivation solutions
- **Technology integration** remains central to solution approaches

### Community Structure
- **Technology Integration** community (329 members) dominates
- **Hybrid Education** patterns emerged post-pandemic
- Strong **inter-community** connections indicate field integration

## 🛠 Technologies Used

- **Python 3.8+**: Data processing and visualization
- **OpenAI GPT-4.1**: Entity-relation extraction
- **Neo4j**: Graph database and analysis
- **Neo4j GDS**: Graph algorithms and centrality measures
- **Jupyter Notebooks**: Interactive analysis environment
- **Docker**: Containerized Neo4j deployment

### Python Libraries
- `openai`: GPT API integration
- `neo4j`: Database connectivity
- `matplotlib/seaborn`: Visualization
- `pandas`: Data manipulation
- `networkx`: Network analysis

## 🤝 Contributing

We welcome contributions! Please see our contributing guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Areas for Contribution
- Additional analysis algorithms
- Interactive web visualizations
- Multi-language entity extraction
- Comparative analysis with other education domains

## 📜 Citation

If you use this work in your research, please cite:

```bibtex
@misc{distance_education_kg_2025,
  title={Structural Analysis of Distance Education Theses via Knowledge Graphs},
  author={[Serdar ÇAĞLAR]},
  year={2025},
  howpublished={\url{https://github.com/serdarildercaglar/structural-analysis-of-distance-education-theses-via-knowledge-graphs}},
  note={Comprehensive analysis of 703 Turkish distance education theses (1997-2025)}
}
```

## 📄 Data Availability

The processed dataset includes:
- **703 thesis abstracts** with metadata
- **3,140 extracted entities** with frequencies and temporal data
- **8,913 relationships** with weights and thesis associations

*Note: Original thesis texts are not included due to copyright restrictions. Only abstracts and extracted entities are provided.*

## ⚖️ License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Turkish Council of Higher Education (YÖK) for thesis database access
- OpenAI for GPT-4.1 API access
- Neo4j community for graph database tools
- Open source community for visualization libraries

---

**Keywords**: Knowledge Graphs, Distance Education, Network Analysis, Entity Extraction, GPT, Neo4j, Educational Technology, Research Trends, Temporal Analysis, Community Detection

**Contact**: [serdarildercaglar@gmail.com]