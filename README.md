# Amnesia API — Data Anonymization with K-Anonymity

This repository demonstrates end-to-end usage of the [Amnesia REST API](https://amnesia.openaire.eu/) for k-anonymity data anonymization, with focus on Czech cultural survey data (Kultura 2012).

---

## Authors

* Vojtěch Kaše

## License

CC-BY-SA 4.0, see attached License.md

---

## Overview

This repo contains three Jupyter notebooks demonstrating different aspects of working with the Amnesia anonymization backend:

### Notebooks in `scripts/`

| Notebook | Description |
|----------|-------------|
| `0_amnesia-in-usage.ipynb` | **Basic Amnesia demo** — Shows how to start the Amnesia server, load a simple CSV dataset, auto-generate generalization hierarchies, run k-anonymity anonymization, and download safe solutions. Uses sample test data with age, zipcode, gender, salary columns. |
| `1_culture_parsing&explorations.ipynb` | **SPSS survey data exploration** — Loads the Kultura 2012 cultural survey (3,679 respondents from 3 Czech regions) from SPSS `.sav` format using `pyreadstat`. Explores variable labels, value labels, missing values, and demographic distributions. Demonstrates applying value formats for readable exploration. |
| `2_culture-amnesia-anonymization.ipynb` | **Full survey anonymization workflow** — Combines both previous notebooks: loads the full 275-column survey, extracts quasi-identifiers (region, age, education, town size), generates range-based hierarchies via Amnesia API, runs k-anonymity anonymization, merges anonymized QI columns back with the full dataset, and verifies privacy guarantees. |

### Workflow Summary

1. **Data Loading** — Support for both CSV and SPSS `.sav` files (via `pyreadstat`)
2. **Quasi-Identifier Selection** — Identify demographic columns requiring anonymization
3. **Hierarchy Generation** — Auto-generate range-based generalization hierarchies via Amnesia API
4. **K-Anonymity Enforcement** — Find safe anonymization solutions satisfying k-anonymity constraints
5. **Solution Export** — Download anonymized data and merge back with non-QI columns
6. **Privacy Verification** — Validate minimum group sizes in anonymized output

---

## Getting Started

### Prerequisites

- Java 11+ (for running the Amnesia backend)
- Python 3.8+
- Virtual environment (recommended)

### Installation

```bash
git clone <url-of-the-git-file>
cd <name-of-the-repo>
pip install -r requirements.txt
```

### Dependencies

Key libraries: pandas, nltk, gensim, networkx, scikit-learn, matplotlib, seaborn, sddk, pyreadstat

---

## Running the Demos

### Amnesia Backend Server

The Amnesia server is a Java JAR located outside this repo at:
```
~/notebooks/Amnesia/target/amnesiaBackEnd-1.0-SNAPSHOT.jar
```

### Running Notebooks

Go to `scripts` directory and run the notebooks:

```bash
cd scripts
jupyter notebook
```

Then open the desired notebook:
- `0_amnesia-in-usage.ipynb` — Basic API walkthrough
- `1_culture_parsing&explorations.ipynb` — Survey data exploration
- `2_culture-amnesia-anonymization.ipynb` — Full anonymization workflow

Each notebook starts the Amnesia backend server automatically (with 4GB heap) and cleans up when done.

---

## Data Files

Located in `data/`:

### Sample Data
- `test_data.csv` — Simple demo dataset (age, zipcode, gender, salary)
- `age_hier.txt` — Pre-generated age generalization hierarchy
- `salary_hier.txt` — Pre-generated salary generalization hierarchy
- `anonymized.csv` — Example anonymized output

### Culture Survey Data (`culture_dataverse_files/`)
- `Kult2012_3kraje_UstVysZli_CSDA_pub_nove_bez_jmen.sav` — SPSS file with 275 variables on cultural participation (3,679 respondents)
- PDF documentation files (technical report, codebook, questionnaire)

Generated outputs:
- `culture_full.parquet` — Full survey data exported to Parquet
- `culture_qi.csv` — Quasi-identifier subset for anonymization
- `culture_anonymized.parquet` — Fully anonymized dataset

---

## Architecture

### Amnesia REST API Endpoints Used

| Endpoint | Purpose |
|----------|---------|
| `/getSession` | Obtain session cookie (JSESSIONID) |
| `/loadData` | Upload CSV dataset with column type definitions |
| `/generateHierarchy` | Create range-based generalization hierarchies |
| `/loadHierarchies` | Load pre-defined hierarchy files |
| `/anonymization` | Run k-anonymity algorithm with bound attributes |
| `/getSolution` | Download anonymized dataset for specific solution |
| `/clearSession` | Clean up server-side session state |

### Generalization Hierarchies

Amnesia uses tree-based hierarchies to define generalization levels:
- **Range hierarchies** — Numeric attributes generalized into intervals (e.g., age 20-100 → 20-50, 50-80, 80-100)
- **Fanout control** — Determines branching factor at each level
- **Multi-level generalization** — Each attribute can be generalized to varying depths

### K-Anonymity Guarantee

Each record in the anonymized output belongs to a group of at least `k` records sharing identical quasi-identifier values, preventing re-identification attacks.

---

## How to Cite

[once a release is created and published via zenodo, put its citation here]

## Acknowledgement

[This work has been supported by ...]
