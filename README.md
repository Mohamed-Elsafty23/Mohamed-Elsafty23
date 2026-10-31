# Mohamed Elsafty

Data engineer in Munich. Working student on the AI & Data Platform team at **EOS GmbH**, and finishing an **M.Sc. in Management & Data Science** at Leuphana University Lüneburg (Feb 2027).

At work I build on Databricks and Azure — ingestion and transformation pipelines in PySpark, medallion data models in Delta Lake, Unity Catalog governance, and the RAG and agent components that sit on top of them. The repos here are mostly the opposite: the parts I don't get to touch at work, built end to end so I actually understand them. Kafka and Debezium CDC, dbt on Snowflake, Terraform, SCD Type 2 written by hand before I let a tool do it for me.

Before Germany I did Arabic NLP and dataset work at ASAS AI, trained 500+ students in Python for Udacity's DECI program, and delivered 13+ scraping contracts on Upwork. 2x Kaggle Master.

**If you only open three:** [FactoryPulse](https://github.com/Mohamed-Elsafty23/FactoryPulse) for data engineering, [AskNature BioRAG](https://github.com/Mohamed-Elsafty23/AskNature-RAG-based-Chatbot) for AI engineering, [Supply Chain Risk Monitor](https://github.com/Mohamed-Elsafty23/Supply-Chain-Risk-Compliance-Monitor) if you want to see streaming and dbt.

---

## Data engineering

**[FactoryPulse](https://github.com/Mohamed-Elsafty23/FactoryPulse)** — Manufacturing reliability lakehouse on Azure
Takes ~10M machine sensor readings (real AI4I 2020 data, expanded by a seeded PySpark simulator across 20 machines, 2 plants and 12 months) through Bronze → Silver → Gold in Delta Lake, ending in a star schema with SCD2 machine history via Delta `MERGE` and 100K downtime intervals derived with gaps-and-islands. Ingestion is a single metadata-driven ADF pipeline with closed watermark windows that advance only on success — the fix for the classic incremental-loading data-loss bug. The whole Azure estate is Terraform: 69 resources, Unity Catalog grants for three personas. Full run: 6m 37s on serverless.

The part I'd actually point at: 10 data-quality gates run on every execution, and the Silver↔Gold reconciliation caught two silent defects during the cloud migration — a point-in-time join quietly dropping 2.6M rows, and a machine left with two open SCD2 versions. Neither threw an error. Serving runs on DuckDB over exported Parquet behind a unit-tested semantic layer, so the dashboard still works with Azure fully decommissioned.

`Azure` · `Databricks` · `PySpark` · `Delta Lake` · `Unity Catalog` · `ADF` · `Terraform` · `DuckDB` · `Streamlit` · `68 tests in CI`

**[Supply Chain Risk & Compliance Monitor](https://github.com/Mohamed-Elsafty23/Supply-Chain-Risk-Compliance-Monitor)** — Streaming risk detection on GDELT
Pulls global event data from the GDELT 2.0 API every 15 minutes, serializes it to Avro against Confluent Schema Registry, streams it through Kafka, and lands Hive-partitioned Parquet on S3 that Snowpipe auto-ingests into Snowflake. Twelve incremental dbt models build composite risk scores across severity, sentiment, velocity and density, identify affected organizations, and aggregate country risk with SCD2 history. Three Airflow DAGs orchestrate it through Cosmos, so you get per-model visibility and freshness alerting instead of one opaque `dbt run` task. Terraform covers both AWS and Snowflake; Power BI drills into the gold schema.

`Kafka` · `Avro` · `Snowflake` · `dbt` · `Airflow` · `AWS` · `Terraform` · `Power BI`

**[Real-Time Banking Data Pipeline](https://github.com/Mohamed-Elsafty23/Real-Time-Banking-Data-Pipeline)** — CDC from Postgres to Snowflake
Debezium captures row-level changes off a Postgres OLTP database through a dedicated `pgoutput` replication slot, Kafka carries them, and a consumer batches `payload.after` into date-partitioned Parquet on MinIO. Airflow syncs to Snowflake every minute; dbt snapshots handle SCD2 on customers and accounts, and the gold layer exposes `is_current` dimensions plus an incremental fact table. Financial amounts use Python `Decimal` with `ROUND_DOWN` end to end. Eight containers in Docker Compose with health checks and startup ordering, because getting Debezium, Kafka and Connect to come up in the right sequence is half the work.

`Debezium` · `Kafka` · `PostgreSQL` · `MinIO` · `Airflow` · `dbt` · `Snowflake` · `Docker`

## AI & LLM engineering

**[AskNature BioRAG Chatbot](https://github.com/Mohamed-Elsafty23/AskNature-RAG-based-Chatbot)** — Hybrid retrieval over 1,400+ biomimicry innovations
BM25 and FAISS run in parallel and get fused with Reciprocal Rank Fusion; HyDE expands the query first by having the LLM write a hypothetical ideal answer to retrieve against. Before indexing, an LLM pass with Llama-3.3-70B enriched every record with structured metadata — mechanisms, industry applications, sustainability impact — so retrieval depends less on surface wording. Embeddings from `e5-mistral-7b-instruct`, multi-provider LLM routing with retry and caching, evaluated with `ragas` on faithfulness, answer relevancy and context precision/recall.

`RAG` · `FAISS` · `BM25` · `RRF` · `HyDE` · `LangChain` · `ragas` · `Gradio`

**[DBpedia KGQA](https://github.com/Mohamed-Elsafty23/DBpedia_KGQA)** — Natural language → SPARQL → DBpedia
Five stages: entity linking via DBpedia Spotlight with cascading confidence thresholds (0.35 → 0.2), LLM SPARQL generation primed with multi-hop, aggregate, ASK and UNION examples, execution with error-driven query repair and up to two retries, and — when SPARQL genuinely can't answer — a fallback that pulls the 1-hop and 2-hop neighborhood around the linked entities and lets the model reason over raw triples. Answers come back with the execution path and latency attached, so you can see which route produced them. Gradio front end on HuggingFace Spaces, evaluated on 445 LC-QuAD questions.

`SPARQL` · `Knowledge Graphs` · `Entity Linking` · `LLM` · `HuggingFace Spaces`

**LLM-Powered Life Cycle Assessment** — ISO 14040/44 hotspot analysis for manufactured products *(private repo — happy to walk through it)*
Five modules in sequence: an LLM hotspot analyzer identifies environmental impacts across production, distribution, use and end-of-life; an arXiv downloader pulls up to 10 relevant papers; a PDF processor extracts quantitative benchmarks; a solutions generator proposes eco-improvements grounded in that literature; and a deep analyzer revisits the highest-priority hotspots. FAISS backs semantic retrieval over the papers. Threaded processing with a `queue.Queue` log stream keeps the Streamlit UI live during long runs, and multi-key API failover keeps it from dying on a rate limit.

`LLM` · `FAISS` · `arXiv retrieval` · `Streamlit` · `Plotly` · `reportlab`

**[FineWeb-Edu Scorer](https://github.com/Mohamed-Elsafty23/fineweb-edu-scorer)** — Teacher–student distillation, made inspectable
Reproduces the scoring pipeline from the FineWeb-Edu paper (Penedo et al., 2024): Llama-3.3-70B scores web text 0–5 for educational value, and a Ridge regressor trained on 150 labeled samples learns to approximate it — 96.6% F1 on the hold-out at a fraction of the inference cost. Text extraction via `trafilatura` with BeautifulSoup fallback. The Streamlit app shows teacher and student side by side so you can see where the cheap model disagrees with the expensive one.

`Knowledge Distillation` · `scikit-learn` · `Streamlit` · `HuggingFace datasets`

**[LLM FMEA Analyzer](https://github.com/Mohamed-Elsafty23/LLM-FMEA-Analyzer)** — Risk analysis from unstructured review text
Implements the method from El Hassani et al. (2025). Customer car reviews go in; a structured FMEA table comes out with failure modes, root causes, Severity/Occurrence/Detection ratings and computed RPN, plus a Pareto chart ranking which 20% of failure modes drive 80% of the risk. Mostly an exercise in getting reliable structured JSON out of an LLM and validating it before it reaches a DataFrame.

`Structured output` · `Prompt engineering` · `Plotly` · `Streamlit`

**Instruction Dataset Evaluation Platform** — Multi-user annotation tooling for Arabic LLM training data *(private repo)*
Streamlit app behind Google OAuth where annotators review instruction–completion pairs and rate model outputs to generate preference signals for RLHF/DPO fine-tuning. Firestore stores user profiles, per-user progress and annotation records; cookie-based session state survives page navigation. Annotations export as JSONL and feed into the HuggingFace Hub side of the pipeline. Built at ASAS AI, where the bottleneck was never the model — it was getting consistent labels out of a team of humans.

`Streamlit` · `Google OAuth` · `Firestore` · `RLHF/DPO data`

## Data warehousing & SQL

**[Government Services Data Warehouse](https://github.com/Mohamed-Elsafty23/services-dwh)** — PostgreSQL 15, star schema, five dimensions on SCD2
Citizen service requests across portal, call center and branch, modeled as a central fact with six dimensions covering resolution time, fees, satisfaction and reopened-request flags. The interesting half is the Silver layer: the source had channel names spelled several ways (`CallCentr`, `callcenter`, …), office codes split between `DXB_01` and `DXB-01`, and resolution timestamps landing before submission. Each gets handled explicitly — normalized, flagged, or excluded from averages — rather than filtered away. Incremental loads use `MERGE` with `UpdatedAt` watermarks. Seven analytics modules surfaced the finding the warehouse was built for: Dubai resolving 30.6% of requests against 92% elsewhere.

**[Sales Data Warehouse — Northwind](https://github.com/Mohamed-Elsafty23/sales-dwh-northwind)** — Bronze/Silver/Gold in pure SQL
Classic Northwind source, full Medallion build in PostgreSQL 15. Six dimensions, four of them SCD2, validation flags carried through Silver as `IsValid`/`ValidationNotes`, and separate initial-load and incremental-load scripts with row-count reconciliation between them. Five analytics suites over 471 orders and $1.18M revenue. ERDs at conceptual, logical and physical levels.

**[Data Quality Profiler](https://github.com/Mohamed-Elsafty23/Data-Quality-Profiler)** — Schema-agnostic DQ monitoring for Postgres
A single install script. A PL/pgSQL procedure introspects `information_schema`, generates SQL at runtime, and profiles every table and column — null counts, numeric min/max/avg, string length ranges — with no per-table configuration, so it drops into any Postgres database unchanged. Triggers re-run profiling on modification, three KPI views expose the results, and Looker Studio reads them directly.

## Data collection

**Arabic News Scraping Pipeline** — Multi-source corpus for Arabic NLP *(private repo)*
Ten-plus major Arabic publications — Al-Masry Al-Youm, Al-Riyadh, Okaz, SPA, Al-Madina and others — each a standalone Scrapy spider, with `selenium-stealth` and `undetected-chromedriver` handling Cloudflare and JavaScript challenges. A merge step consolidates everything into one DataFrame and a CLI tool pushes the result to the HuggingFace Hub as a versioned public dataset for downstream LLM pre-training.

`Scrapy` · `Selenium` · `undetected-chromedriver` · `HuggingFace Hub`

## Research & coursework

**[Music Network Analysis](https://github.com/Mohamed-Elsafty23/Music-Network-Analysis)** — Cultural diffusion through cover songs, modeled as a directed influence network. Centrality measures, community detection, topology analysis, and a network model grounded in diffusion theory rather than assembled ad hoc.

**[Modelling Fire Frequency in Kenya](https://github.com/Mohamed-Elsafty23/Modelling-fire-frequency-in-Kenya)** — Bayesian negative binomial models in PyMC over MODIS hotspot and raster climate data, 2000–2018. TBATS seasonality checks, spatial categorization to improve fit, and a frequentist/Bayesian comparison on both real and simulated data.

**[MSc Management & Data Science](https://github.com/Mohamed-Elsafty23/MScManagementDataScience)** — Applied statistics coursework from Leuphana.

---

## Stack

**Languages** Python, SQL, PySpark, Spark SQL, PL/pgSQL, R, Bash
**Data engineering** Airflow, dbt, Azure Data Factory, Kafka, Debezium CDC, Delta Lake, Medallion architecture, star schema, SCD Type 2, data quality monitoring
**Platforms** Azure, Databricks, Unity Catalog, Snowflake, AWS (S3/IAM/SQS), PostgreSQL, DuckDB, MinIO, Parquet
**Infrastructure** Terraform, Docker, GitHub Actions, Linux
**AI engineering** RAG, FAISS, LangChain, AI agents, prompt engineering, HuggingFace, scikit-learn, NLP
**BI** Power BI (DAX, Power Query), Tableau, Looker Studio, Streamlit, Plotly

---

## Contact

[LinkedIn](https://linkedin.com/in/mohamedelsafty) · [Kaggle](https://kaggle.com/mohamedkhaledelsafty) · mohamed.kh.elsafty@gmail.com

Open to Data Engineer, Analytics Engineer and AI/Data Platform roles in Germany and the EU.
