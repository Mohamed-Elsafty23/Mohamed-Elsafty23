# Mohamed Elsafty

**Data engineer — Munich, Germany**

I build data platforms end to end: ingestion, modeling, the infrastructure underneath, and the checks that catch it when something breaks quietly. Currently on the AI & Data Platform team at **EOS GmbH**, leading a from-scratch Databricks and Azure platform from requirements through production rollout, and finishing an **M.Sc. in Management & Data Science** at Leuphana University Lüneburg.

Before Germany: Arabic NLP and LLM dataset work at ASAS AI, 500+ students trained in Python for Udacity's DECI program, 13+ scraping contracts delivered on Upwork. 2x Kaggle Master.

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

| Project | What it is | Stack |
|---|---|---|
| **[Real-Time Banking Pipeline](https://github.com/Mohamed-Elsafty23/Real-Time-Banking-Data-Pipeline)** | Debezium captures row-level changes off Postgres through a `pgoutput` replication slot; Kafka carries them, a consumer batches date-partitioned Parquet to MinIO, Airflow syncs to Snowflake every minute, dbt snapshots handle SCD2. Eight containers with health checks and startup ordering. | Debezium · Kafka · Postgres · MinIO · Airflow · dbt · Snowflake · Docker |
| **[Government Services DWH](https://github.com/Mohamed-Elsafty23/services-dwh)** | Citizen service requests across three channels, star schema with five SCD2 dimensions. The Silver layer handles what the source actually contained: channel names spelled four ways, office codes split between `DXB_01` and `DXB-01`, resolutions timestamped before submission — normalized or flagged, not filtered away. | PostgreSQL 15 · SQL `MERGE` · star schema · SCD2 |
| **[Sales DWH — Northwind](https://github.com/Mohamed-Elsafty23/sales-dwh-northwind)** | Full Medallion build in pure SQL. Six dimensions, four on SCD2, validation flags carried through Silver, separate initial and incremental loads with row-count reconciliation between them. ERDs at conceptual, logical and physical levels. | PostgreSQL · SQL · ETL · star schema |
| **[Data Quality Profiler](https://github.com/Mohamed-Elsafty23/Data-Quality-Profiler)** | One install script. A PL/pgSQL procedure introspects `information_schema` and generates SQL at runtime, profiling every table and column with zero per-table configuration — so it drops into any Postgres database unchanged. Triggers keep metrics current. | PL/pgSQL · dynamic SQL · Looker Studio |

## AI & LLM engineering

| Project | What it is | Stack |
|---|---|---|
| **[DBpedia KGQA](https://github.com/Mohamed-Elsafty23/DBpedia_KGQA)** | Natural language → SPARQL → DBpedia. Entity linking with cascading confidence thresholds, LLM query generation, execution with error-driven repair and retries, and a fallback that pulls the 1–2 hop neighborhood around linked entities when SPARQL genuinely can't answer. Answers carry their execution path and latency. | SPARQL · knowledge graphs · LLM · Gradio |
| **[FineWeb-Edu Scorer](https://github.com/Mohamed-Elsafty23/fineweb-edu-scorer)** | Teacher–student distillation from the FineWeb-Edu paper: Llama-3.3-70B scores web text 0–5 for educational value, a Ridge regressor learns to approximate it at **96.6% F1** for a fraction of the inference cost. The app shows both side by side, so you can see where the cheap model disagrees. | scikit-learn · distillation · Streamlit |
| **[LLM FMEA Analyzer](https://github.com/Mohamed-Elsafty23/LLM-FMEA-Analyzer)** | Implements El Hassani et al. (2025): unstructured customer reviews in, structured FMEA table out — failure modes, root causes, S/O/D ratings, computed RPN, Pareto ranking of the 20% driving 80% of risk. Mostly an exercise in validating LLM JSON before it reaches a DataFrame. | structured output · Plotly · Streamlit |
| **LLM-Powered LCA Analysis** *(private)* | ISO 14040/44 hotspot analysis for manufactured products. Five chained modules: LLM hotspot identification across the full life cycle, arXiv retrieval and PDF parsing for evidence, solution generation grounded in that literature, then deeper analysis of the top hotspots. Multi-key failover and threaded log streaming keep long runs alive and visible. | LLM · FAISS · arXiv · Streamlit |
| **Instruction Dataset Platform** *(private)* | Multi-user annotation tooling for Arabic LLM training data. Annotators rate instruction–completion pairs to generate preference signals for RLHF/DPO; Firestore tracks per-user progress behind Google OAuth. Built at ASAS AI, where the bottleneck was never the model — it was consistent labels from a team of humans. | Streamlit · OAuth · Firestore · RLHF data |
| **Arabic News Scraping Pipeline** *(private)* | Corpus collection across 10+ Arabic publications, each a standalone Scrapy spider, with `selenium-stealth` and `undetected-chromedriver` for Cloudflare and JS challenges. Merged, versioned and pushed to the HuggingFace Hub for downstream LLM pre-training. | Scrapy · Selenium · HuggingFace Hub |

## Research

**[Music Network Analysis](https://github.com/Mohamed-Elsafty23/Music-Network-Analysis)** — musical influence as a directed network of cover songs; centrality, community detection and topology analysis on a model grounded in cultural diffusion theory rather than assembled ad hoc.

**[Fire Frequency in Kenya](https://github.com/Mohamed-Elsafty23/Modelling-fire-frequency-in-Kenya)** — Bayesian negative binomial models in PyMC over MODIS hotspot and raster climate data, 2000–2018, with TBATS seasonality checks and frequentist/Bayesian comparison.

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
