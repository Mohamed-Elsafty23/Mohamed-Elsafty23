# Mohamed Elsafty

**Data engineer — Munich, Germany**

I build data platforms end to end: ingestion, modeling, the infrastructure underneath, and the checks that catch it when something breaks quietly. Currently on the AI & Data Platform team at **EOS GmbH**, leading a from-scratch Databricks and Azure platform from requirements through production rollout, and finishing an **M.Sc. in Management & Data Science** at Leuphana University Lüneburg.

I've also built Arabic NLP and LLM dataset pipelines at ASAS AI, trained 500+ students in Python for Udacity's DECI program, and delivered 13+ scraping and automation contracts on Upwork. 2x Kaggle Master.

Open to **Data Engineer**, **Analytics Engineer** and **AI / Data Platform** roles in Germany and the EU.

---

## Featured work

### [FactoryPulse](https://github.com/Mohamed-Elsafty23/FactoryPulse) — manufacturing reliability lakehouse on Azure

`Azure` `Databricks` `PySpark` `Delta Lake` `Unity Catalog` `Azure Data Factory` `Terraform` `DuckDB`

10M machine sensor readings move through Bronze → Silver → Gold in Delta Lake into a star schema with SCD2 machine history via Delta `MERGE`, and 100K downtime intervals derived with the gaps-and-islands pattern. Ingestion is one metadata-driven ADF pipeline using closed watermark windows that advance only on success — the fix for the classic incremental-loading data-loss bug. 69 Azure resources under Terraform, Unity Catalog grants for three personas. Full run: **6m 37s** on serverless.

The detail worth opening the repo for: 10 data-quality gates run on every execution, and the Silver↔Gold row reconciliation caught two silent defects during the cloud migration — a point-in-time join quietly dropping 2.6M rows, and a machine left with two open SCD2 versions. Neither threw an error. The serving layer runs on DuckDB over exported Parquet behind a unit-tested semantic layer, so the dashboard still works with the Azure estate fully decommissioned.

### [Supply Chain Risk & Compliance Monitor](https://github.com/Mohamed-Elsafty23/Supply-Chain-Risk-Compliance-Monitor) — streaming risk detection on GDELT

`Kafka` `Avro` `Snowflake` `dbt` `Airflow` `AWS` `Terraform` `Power BI`

Global event data from GDELT every 15 minutes, serialized to Avro against Confluent Schema Registry, streamed through Kafka, landed as Hive-partitioned Parquet on S3 that Snowpipe auto-ingests into Snowflake. Twelve incremental dbt models build composite risk scores across severity, sentiment, velocity and density, resolve affected organizations, and aggregate country-level risk with SCD2 history.

Three Airflow DAGs orchestrate it through Cosmos, so failures surface per model rather than as one opaque `dbt run` task, with freshness checks that alert before stale data reaches the dashboard. Terraform provisions both sides — AWS and Snowflake, including RBAC.

### [AskNature BioRAG Chatbot](https://github.com/Mohamed-Elsafty23/AskNature-RAG-based-Chatbot) — hybrid retrieval over 1,400+ biomimicry innovations

`RAG` `FAISS` `BM25` `RRF` `HyDE` `LangChain` `ragas`

BM25 and FAISS run in parallel and fuse with Reciprocal Rank Fusion; HyDE expands the query first by having the model write a hypothetical ideal answer to retrieve against. Before indexing, an LLM pass with Llama-3.3-70B enriched every record with structured metadata — biological mechanisms, industry applications, sustainability impact — so retrieval stops depending on how the user happened to phrase things.

Evaluated with `ragas` on faithfulness, answer relevancy and context precision/recall, because "the demo looks good" isn't a measurement.

---

## Data engineering & SQL

### [Real-Time Banking Data Pipeline](https://github.com/Mohamed-Elsafty23/Real-Time-Banking-Data-Pipeline) — change data capture from Postgres to Snowflake

`Debezium` `Kafka` `PostgreSQL` `MinIO` `Airflow` `dbt` `Snowflake` `Docker`

Debezium captures row-level changes off a Postgres OLTP database through a dedicated `pgoutput` replication slot, Kafka carries them, and a consumer batches them into date-partitioned Parquet on MinIO. Airflow syncs to Snowflake every minute; dbt snapshots handle SCD2 on customers and accounts, and the gold layer exposes `is_current` dimensions plus an incremental fact table. Financial amounts use Python `Decimal` with `ROUND_DOWN` end to end. Eight containers in Docker Compose with health checks and startup ordering, because getting Debezium, Kafka and Connect to come up in the right sequence is half the work.

### [Government Services Data Warehouse](https://github.com/Mohamed-Elsafty23/services-dwh) — PostgreSQL star schema with five SCD2 dimensions

`PostgreSQL 15` `SQL MERGE` `star schema` `SCD Type 2` `Medallion`

Citizen service requests across portal, call center and branch, modeled as a central fact covering resolution time, fees, satisfaction and reopened-request flags. The interesting half is the Silver layer, which handles what the source actually contained rather than what it was supposed to: channel names spelled several ways (`CallCentr`, `callcenter`, …), office codes split between `DXB_01` and `DXB-01`, resolution timestamps landing before submission. Each one gets normalized, flagged or excluded from averages explicitly — nothing silently dropped. Incremental loads use `MERGE` with `UpdatedAt` watermarks, and seven analytics modules surfaced the finding the warehouse was built for: Dubai resolving 30.6% of requests against 92% elsewhere.

### [Sales Data Warehouse — Northwind](https://github.com/Mohamed-Elsafty23/sales-dwh-northwind) — Bronze/Silver/Gold in pure SQL

`PostgreSQL` `SQL` `ETL` `star schema` `SCD Type 2`

Classic Northwind OLTP source taken through a full Medallion build. Six dimensions, four of them SCD2, with validation flags carried through Silver as `IsValid`/`ValidationNotes` so bad rows stay visible instead of disappearing. Separate initial-load and incremental-load scripts, plus a validation layer doing row-count reconciliation between them. Five analytics suites over 471 orders and $1.18M revenue, with ERDs at conceptual, logical and physical levels.

### [Data Quality Profiler](https://github.com/Mohamed-Elsafty23/Data-Quality-Profiler) — schema-agnostic monitoring for PostgreSQL

`PL/pgSQL` `dynamic SQL` `triggers` `Looker Studio`

One install script. A PL/pgSQL procedure introspects `information_schema` to enumerate every table and column, then generates and executes SQL at runtime — null counts, numeric min/max/avg, string length ranges — with no per-table configuration, so it drops into any Postgres database unchanged. Triggers re-run profiling when tables are modified to keep metrics current, three KPI views expose the results, and Looker Studio reads them directly.

---

## AI & LLM engineering

### [DBpedia KGQA](https://github.com/Mohamed-Elsafty23/DBpedia_KGQA) — natural language to SPARQL over a live knowledge graph

`SPARQL` `knowledge graphs` `entity linking` `LLM` `Gradio`

Five stages: entity linking via DBpedia Spotlight with cascading confidence thresholds, LLM SPARQL generation primed with multi-hop, aggregate, ASK and UNION examples, execution with error-driven query repair and up to two retries, and — when SPARQL genuinely can't answer — a fallback that pulls the 1-hop and 2-hop neighborhood around the linked entities and lets the model reason over raw triples. Answers come back with their execution path and latency attached, so you can see which route produced them. Gradio front end on HuggingFace Spaces, evaluated on 445 LC-QuAD questions.

### [FineWeb-Edu Scorer](https://github.com/Mohamed-Elsafty23/fineweb-edu-scorer) — teacher-student distillation, made inspectable

`scikit-learn` `knowledge distillation` `Streamlit` `HuggingFace datasets`

Reproduces the scoring pipeline from the FineWeb-Edu paper (Penedo et al., 2024): Llama-3.3-70B scores web text 0–5 for educational value, and a Ridge regressor trained on 150 labeled samples learns to approximate it — **96.6% F1** on the hold-out at a fraction of the inference cost. Text extraction via `trafilatura` with a BeautifulSoup fallback. The Streamlit app puts teacher and student side by side, so you can see exactly where the cheap model disagrees with the expensive one.

### [LLM FMEA Analyzer](https://github.com/Mohamed-Elsafty23/LLM-FMEA-Analyzer) — risk analysis from unstructured review text

`structured output` `prompt engineering` `Plotly` `Streamlit`

Implements the method from El Hassani et al. (2025). Customer car reviews go in; a structured FMEA table comes out with failure modes, root causes, Severity/Occurrence/Detection ratings and computed RPN, plus a Pareto chart ranking which 20% of failure modes drive 80% of the risk. Mostly an exercise in getting reliable structured JSON out of a language model and validating it before it ever reaches a DataFrame.

### LLM-Powered Life Cycle Assessment — ISO 14040/44 hotspot analysis for manufactured products

`LLM` `FAISS` `arXiv retrieval` `Streamlit` `Plotly`

Five modules in sequence: an LLM hotspot analyzer identifies environmental impacts across production, distribution, use and end-of-life; an arXiv downloader pulls up to 10 relevant papers; a PDF processor extracts quantitative benchmarks; a solutions generator proposes eco-improvements grounded in that literature; and a deep analyzer revisits the highest-priority hotspots. FAISS backs semantic retrieval over the papers. Threaded processing with a `queue.Queue` log stream keeps the Streamlit UI live during long runs, and multi-key API failover keeps the whole thing from dying on a single rate limit.

### Instruction Dataset Evaluation Platform — annotation tooling for Arabic LLM training data

`Streamlit` `Google OAuth` `Firestore` `RLHF/DPO data`

A multi-user platform where annotators review instruction–completion pairs and rate model outputs to generate preference signals for RLHF and DPO fine-tuning. Firestore stores user profiles, per-user progress and annotation records behind a Google OAuth flow, with cookie-based session state surviving page navigation. Annotations export as JSONL and feed the HuggingFace Hub side of the pipeline. Built at ASAS AI, where the bottleneck was never the model — it was getting consistent labels out of a team of humans.

### Arabic News Scraping Pipeline — multi-source corpus for Arabic NLP

`Scrapy` `Selenium` `undetected-chromedriver` `HuggingFace Hub`

Ten-plus major Arabic publications — Al-Masry Al-Youm, Al-Riyadh, Okaz, SPA, Al-Madina and others — each implemented as a standalone Scrapy spider, with `selenium-stealth` and `undetected-chromedriver` handling Cloudflare and JavaScript challenges. A merge step consolidates everything into a single DataFrame, and a CLI tool pushes the result to the HuggingFace Hub as a versioned dataset for downstream LLM pre-training.

---

## Research

### [Music Network Analysis](https://github.com/Mohamed-Elsafty23/Music-Network-Analysis) — musical influence as a directed network

`network science` `centrality` `community detection` `Python`

Cover songs modeled as edges of artistic influence, producing a directed network of cultural diffusion. Full pipeline from raw data through cleaning, network construction, centrality measures, connectivity and topology analysis, with the model specification grounded in diffusion theory rather than assembled ad hoc.

### [Modelling Fire Frequency in Kenya](https://github.com/Mohamed-Elsafty23/Modelling-fire-frequency-in-Kenya) — Bayesian count models over climate data

`PyMC` `Bayesian inference` `geospatial` `remote sensing`

MODIS fire hotspot data joined with raster rainfall and temperature grids across 2000–2018, checked for seasonality with TBATS, then modeled with negative binomial regressions in both frequentist and Bayesian form. Spatial categorization improved fit, and the two approaches are compared against each other on real and simulated data.

---

## Stack

| | |
|---|---|
| **Languages** | Python, SQL, PySpark, Spark SQL, PL/pgSQL, R, Bash |
| **Data engineering** | Airflow, dbt, Azure Data Factory, Kafka, Debezium CDC, Delta Lake, Medallion architecture, star schema, SCD Type 2, data quality monitoring |
| **Platforms** | Azure, Databricks, Unity Catalog, Snowflake, AWS (S3/IAM/SQS), PostgreSQL, DuckDB, MinIO, Parquet |
| **Infrastructure** | Terraform, Docker, GitHub Actions, Linux |
| **AI engineering** | RAG, FAISS, LangChain, AI agents, prompt engineering, HuggingFace, scikit-learn, NLP |
| **BI** | Power BI (DAX, Power Query), Tableau, Looker Studio, Streamlit, Plotly |

---

[LinkedIn](https://linkedin.com/in/mohamedelsafty) · [Kaggle](https://kaggle.com/mohamedkhaledelsafty) · mohamed.kh.elsafty@gmail.com
