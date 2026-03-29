# 🧠 LLM Training Data Quality Pipeline

> An end-to-end automated pipeline for curating, validating, and scoring large-scale datasets used to train Large Language Models — built on AWS Bedrock and Amazon S3.

![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
![AWS Bedrock](https://img.shields.io/badge/AWS-Bedrock-orange?logo=amazonaws)
![Amazon S3](https://img.shields.io/badge/Amazon-S3-green?logo=amazonaws)
![QuickSight](https://img.shields.io/badge/Amazon-QuickSight-purple?logo=amazonaws)
![License](https://img.shields.io/badge/license-MIT-lightgrey)

---

## 📌 Overview

High-quality training data is the foundation of reliable LLMs. This project implements a **multi-layered, automated data quality pipeline** that ingests raw text datasets from **Amazon S3**, applies validation and curation logic in Python, uses **AWS Bedrock** for semantic quality scoring, and surfaces pipeline health metrics via an **Amazon QuickSight** dashboard.

Built from real-world experience curating LLM training data at **AWS Bedrock**, this pipeline mirrors production-grade standards used to prepare data for foundation model training.

---

## 🏗️ Architecture

```
Amazon S3 (Raw Data)
        │
        ▼
┌───────────────────────┐
│  Ingestion Layer      │  → Batch loader with schema detection
│  (Python + Boto3)     │
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│  Validation Engine    │  → Deduplication, schema checks,
│  (Pandas + Python)    │    toxicity filtering, format normalization
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│  Semantic Scorer      │  → AWS Bedrock (Claude) rates sample
│  (AWS Bedrock API)    │    quality: coherence, factuality, safety
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│  Curated Output       │  → Cleaned dataset written back to S3
│  (Amazon S3)          │    (partitioned by quality tier)
└───────────┬───────────┘
            │
            ▼
┌───────────────────────┐
│  Monitoring Dashboard │  → Pass rates, error breakdown,
│  (Amazon QuickSight)  │    throughput, quality score trends
└───────────────────────┘
```

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 🔁 **Deduplication** | MinHash-based near-duplicate detection across 2M+ records |
| ✅ **Schema Validation** | Enforces field types, lengths, and required keys per dataset spec |
| 🚫 **Toxicity Filtering** | Flags and removes harmful/unsafe content before model ingestion |
| 🤖 **Semantic Scoring** | Uses AWS Bedrock (Claude) to rate coherence, factuality & safety |
| 📊 **QuickSight Dashboard** | Real-time pipeline health: pass rates, throughput, quality tiers |
| 🪣 **S3-Native I/O** | Partitioned output by quality tier for efficient downstream access |

---

## 📁 Repository Structure

```
llm-training-data-pipeline/
│
├── src/
│   ├── ingestion/
│   │   ├── s3_loader.py          # Batch loader from Amazon S3
│   │   └── schema_detector.py    # Auto-detect and enforce schema
│   │
│   ├── validation/
│   │   ├── deduplicator.py       # MinHash near-duplicate removal
│   │   ├── schema_validator.py   # Field-level validation rules
│   │   ├── toxicity_filter.py    # Content safety filtering
│   │   └── format_normalizer.py  # Encoding, whitespace, structure fixes
│   │
│   ├── scoring/
│   │   └── bedrock_scorer.py     # AWS Bedrock semantic quality scoring
│   │
│   ├── output/
│   │   └── s3_writer.py          # Partitioned write back to S3
│   │
│   └── pipeline.py               # Orchestration: runs full pipeline end-to-end
│
├── dashboard/
│   └── quicksight_manifest.json  # QuickSight dataset manifest for S3 source
│
├── config/
│   └── pipeline_config.yaml      # S3 paths, Bedrock model ID, thresholds
│
├── tests/
│   ├── test_validator.py
│   ├── test_deduplicator.py
│   └── test_bedrock_scorer.py
│
├── notebooks/
│   └── exploratory_analysis.ipynb  # EDA on raw dataset quality distribution
│
├── requirements.txt
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
- Python 3.10+
- AWS account with access to **Amazon S3** and **AWS Bedrock**
- AWS CLI configured (`aws configure`)

### Installation

```bash
git clone https://github.com/Madhuchandra-7/llm-training-data-pipeline.git
cd llm-training-data-pipeline
pip install -r requirements.txt
```

### Configuration

Edit `config/pipeline_config.yaml`:

```yaml
s3:
  input_bucket: your-raw-data-bucket
  input_prefix: raw/dataset/
  output_bucket: your-curated-data-bucket
  output_prefix: curated/

bedrock:
  model_id: anthropic.claude-3-sonnet-20240229-v1:0
  region: us-east-1

validation:
  min_text_length: 50
  max_text_length: 4096
  dedup_threshold: 0.85
  quality_pass_threshold: 0.80
```

### Run the Pipeline

```bash
python src/pipeline.py --config config/pipeline_config.yaml
```

---

## 📊 Results

| Metric | Value |
|---|---|
| Records Processed | 2,000,000+ |
| Data Quality Pass Rate | **98%** (benchmark: 95%) |
| Duplicate Records Removed | ~12% of raw corpus |
| Downstream Model Error Reduction | **30%** |
| Pipeline Throughput | ~50K records/min on EC2 |

---

## 🛠️ Tech Stack

- **Language:** Python 3.10
- **Cloud:** Amazon S3, AWS Bedrock, Amazon EC2, Amazon QuickSight
- **Libraries:** Boto3, Pandas, NumPy, datasketch (MinHash)
- **Monitoring:** Amazon QuickSight (connected to S3 output manifest)

---
