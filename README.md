# Fairness-Aware Job-Resume Matching Pipeline

> A final-year project implementing semantic matching with fairness constraints to rank job candidates fairly across experience levels.

## 🚀 Quick Start

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Run the complete pipeline
python main.py

# 3. Check outputs
ls -la outputs/
ls -la outputs/plots/
```

## 📋 Setup & Installation

### Requirements
- Python 3.8+
- ~8GB RAM (for Sentence-BERT embeddings)
- ~2GB disk space (for cache + outputs)

### Installation

1. **Clone or download this project**
   ```bash
   cd "path/to/8 sem project"
   ```

2. **Create a virtual environment** (recommended)
   ```bash
   python -m venv .venv
   source .venv/bin/activate      # Linux/Mac
   # or
   .venv\Scripts\activate          # Windows
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Prepare data** (if needed)
   - Place freelancer data in `data/freelancer_profiles.csv`
   - Place job data in `data/job_descriptions.csv`
   - Or use existing `data/jobs_cleaned.csv` (fallback)

5. **Validate setup**
   ```bash
   python smoke_test.py
   ```

## ▶️ Running the Pipeline

### Full Pipeline (Default)
Runs all stages: data → embeddings → baseline ranking → fairness reranking → evaluation → plots
```bash
python main.py
```

### Individual Stages

**Baseline ranking only** (fast)
```bash
python main.py --baseline
```

**Baseline + fairness reranking**
```bash
python main.py --fairness
```

**Full evaluation with metrics and plots**
```bash
python main.py --eval
```

---

## 🌐 Running the Web Dashboard & Backend

You can also run the pipeline through an interactive web dashboard with real-time progress tracking.

### 1. Start the Backend

In PowerShell or terminal:
```powershell
uvicorn app:app --reload
```

The backend starts at `http://127.0.0.1:8000`

### 2. Open the Dashboard

Open your browser and navigate to:
```
http://127.0.0.1:8000
```

### 3. Dashboard Features

**Home Tab:**
- Run pipeline with different modes (baseline, fairness, eval, full)
- Real-time progress tracking with percentage and stage indicator
- Error messages displayed if pipeline fails

**Metrics Tab:**
- View evaluation metrics (NDCG@10, Precision@10)
- View fairness metrics (exposure disparity, fresher share)
- Compare baseline vs fair ranking performance

**Rankings Tab:**
- Preview top 20 baseline rankings
- Preview top 20 fair rankings
- View freelancer names, experience groups, and scores

**Plots Tab:**
- Fairness vs Accuracy trade-off
- Experience group distribution
- Score distribution comparison

### API Endpoints

You can also interact with the backend API directly:

**Health & Status:**
- `GET /health` — Health check
- `GET /status` — Current pipeline status (used by dashboard)

**Run Endpoints:**
- `POST /run/baseline` — Run baseline ranking
- `POST /run/fairness` — Run baseline + fairness reranking
- `POST /run/eval` — Run full evaluation
- `POST /run/full` — Run complete pipeline (baseline → fairness → eval)

**Results:**
- `GET /results/evaluation` — Evaluation metrics (NDCG, Precision)
- `GET /results/bias` — Fairness metrics (exposure disparity, group distribution)
- `GET /results/rankings/baseline` — Baseline ranking preview (top 20)
- `GET /results/rankings/fair` — Fair ranking preview (top 20)

**Plots:**
- `GET /plots/fairness-vs-accuracy` — PNG image of fairness vs accuracy plot
- `GET /plots/fresher-distribution` — PNG image of experience group distribution
- `GET /plots/score-distribution` — PNG image of score distribution

### Example API Usage

```bash
# Check health
curl http://127.0.0.1:8000/health

# Start baseline ranking
curl -X POST http://127.0.0.1:8000/run/baseline

# Check status
curl http://127.0.0.1:8000/status

# Get evaluation metrics
curl http://127.0.0.1:8000/results/evaluation

# Download a plot
curl http://127.0.0.1:8000/plots/fairness-vs-accuracy > fairness.png
```

---

## 📁 Project Structure

```
.
├── main.py                          # CLI entry point
├── app.py                           # FastAPI backend entry point
├── config.py                        # Central configuration
├── smoke_test.py                    # Validation script
├── requirements.txt                 # Dependencies
├── README.md                        # This file
│
├── backend/                         # FastAPI backend
│   ├── __init__.py
│   ├── state.py                     # Progress tracking state
│   └── pipeline_runner.py           # Pipeline functions with progress callbacks
│
├── dashboard/                       # Web dashboard frontend
│   ├── index.html                   # Main page (tabs, UI, styling)
│   └── app.js                       # Frontend logic (polling, data loading)
│
├── data/                            # Input datasets
│   ├── freelancer_profiles.csv      # Resumes (if available)
│   ├── job_descriptions.csv         # Job postings (if available)
│   └── jobs_cleaned.csv             # Fallback legacy job data
│
├── src/                             # Active pipeline modules
│   ├── data/                        # Data loading & preprocessing
│   │   ├── loader.py                # Load freelancer & job data
│   │   └── preprocessing.py         # Normalize & enrich data
│   │
│   ├── nlp/                         # NLP & text processing
│   │   ├── embedding.py             # Sentence-BERT with intelligent caching
│   │   └── skill_extraction.py      # Regex-based skill extraction
│   │
│   ├── features/                    # Feature engineering
│   │   └── feature_engineering.py   # 5 matching features per pair
│   │
│   ├── ranking/                     # Ranking algorithms
│   │   ├── baseline.py              # Weighted scoring
│   │   └── fairness.py              # Group normalization + MMR + UCB
│   │
│   ├── evaluation/                  # Metrics & evaluation
│   │   ├── metrics.py               # NDCG@10, Precision@10
│   │   └── bias.py                  # Fairness metrics (exposure, disparity)
│   │
│   └── visualization/               # Output plots
│       └── plots.py                 # Generate fairness & accuracy plots
│
├── outputs/                         # Generated outputs (created on first run)
│   ├── baseline_ranking.csv         # All pairs ranked by baseline
│   ├── fair_ranking.csv             # All pairs ranked with fairness
│   ├── evaluation_metrics.json      # NDCG, Precision comparisons
│   ├── bias_metrics.json            # Exposure, disparity metrics
│   └── plots/
│       ├── fairness_vs_accuracy.png
│       ├── fresher_distribution.png
│       └── score_distribution.png
│
└── _archive/                        # Legacy code (not used in active pipeline)
    └── [old experimental modules]
```
│   ├── nlp/                         # NLP & text processing
│   │   ├── embedding.py             # Sentence-BERT with intelligent caching
│   │   └── skill_extraction.py      # Regex-based skill extraction
│   │
│   ├── features/                    # Feature engineering
│   │   └── feature_engineering.py   # 5 matching features per pair
│   │
│   ├── ranking/                     # Ranking algorithms
│   │   ├── baseline.py              # Weighted scoring
│   │   └── fairness.py              # Group normalization + MMR + UCB
│   │
│   ├── evaluation/                  # Metrics & evaluation
│   │   ├── metrics.py               # NDCG@10, Precision@10
│   │   └── bias.py                  # Fairness metrics (exposure, disparity)
│   │
│   └── visualization/               # Output plots
│       └── plots.py                 # Generate fairness & accuracy plots
│
├── outputs/                         # Generated outputs (created on first run)
│   ├── baseline_ranking.csv         # All pairs ranked by baseline
│   ├── fair_ranking.csv             # All pairs ranked with fairness
│   ├── evaluation_metrics.json      # NDCG, Precision comparisons
│   ├── bias_metrics.json            # Exposure, disparity metrics
│   └── plots/
│       ├── fairness_vs_accuracy.png
│       ├── fresher_distribution.png
│       └── score_distribution.png
│
└── _archive/                        # Legacy code (not used)
    └── [old experimental modules]
```

## 🔧 Configuration

All parameters are centralized in `config.py`. Key settings:

### Data Paths
- `FREELANCER_CSV` — freelancer dataset (primary source)
- `JOB_CSV` — job postings dataset (primary source)
- `SKILL_VOCABULARY` — list of 45+ recognized skills

### Baseline Weighting
```python
FEATURE_WEIGHTS = {
    "semantic_similarity": 0.45,   # Job-resume semantic match
    "skill_coverage":      0.25,   # Fraction of skills matched
    "skill_rarity":        0.12,   # Rare skills valued higher
    "experience_score":    0.10,   # Years of experience alignment
    "skill_depth":         0.08,   # Skill breadth + focus balance
}
```

### Fairness Parameters
- `FAIRNESS_ALPHA = 0.75` — Normalized score weight (higher = stronger fairness)
- `UCB_WEIGHT = 0.12` — Exploration bonus for under-represented groups
- `UCB_C = 1.0` — UCB constant (higher = more exploration)
- `MMR_LAMBDA = 0.78` — Relevance vs diversity (0.78 = prefer relevance slightly)
- `FAIRNESS_CANDIDATE_POOL = 50` — Pool size for MMR reranking

### Experience Groups
```python
EXPERIENCE_GROUPS = ["Fresher", "Early-career", "Mid-level", "Senior"]
```

**Change parameters** by editing `config.py` before running the pipeline.

## 📊 Expected Outputs

### CSV Files
- **`baseline_ranking.csv`** — All freelancer-job pairs ranked by baseline score
  - Columns: job_id, freelancer_id, baseline_score, rank, matched_skills, ...
  - Best for: Analyzing baseline behavior, debugging features

- **`fair_ranking.csv`** — Same pairs reranked with fairness constraints
  - Columns: job_id, freelancer_id, fair_score, rank, experience_group, ...
  - Best for: Analyzing fairness impact, comparing diversity

### JSON Files
- **`evaluation_metrics.json`** — Quality metrics (NDCG, Precision) for both rankings
- **`bias_metrics.json`** — Fairness metrics (exposure disparity, fresher share)

### Plots
- **`fairness_vs_accuracy.png`** — NDCG vs exposure disparity (two-axis plot)
- **`fresher_distribution.png`** — Experience group distribution in top-10
- **`score_distribution.png`** — Score histogram comparison

## 🎯 Pipeline Stages Explained

### 1. Data Loading
Load freelancers (resume/profile) and job postings from CSV or JSON sources.
- Validates required columns (freelancer_id, text, job_id, title, description)
- Falls back to legacy formats if modern CSVs not available

### 2. Preprocessing
Normalize and enrich data:
- Clean text (remove whitespace, standardize encoding)
- Extract skills using regex vocabulary (45+ skills)
- Infer experience groups from years or job title keywords
- Compute experience_group for ranking

### 3. Embedding Generation
Encode all text using Sentence-BERT (`all-MiniLM-L6-v2`):
- Resume text → 384-dim vector
- Job text (title + description) → 384-dim vector
- **Smart caching:** If text unchanged, reuse cached embeddings (by SHA256 signature)
- Supports batch processing with progress bar

### 4. Feature Engineering
Compute 5 features for every freelancer-job pair:

| Feature | Range | Purpose |
|---------|-------|---------|
| semantic_similarity | [0, 1] | Cosine similarity of embeddings |
| skill_coverage | [0, 1] | % of job's required skills matched |
| skill_rarity | [0, 1] | Weighted match (rare skills valued more) |
| experience_score | [0, 1] | Penalty for experience mismatch |
| skill_depth | [0, 1] | Balance of specialization + breadth |

### 5. Baseline Ranking
Weighted sum of 5 features (45% semantic, 25% skill_coverage, ...).
- Per job, ranks all freelancers by baseline_score
- Ties broken by semantic_similarity

### 6. Fairness-Aware Reranking
Multi-stage algorithm to improve representation:

1. **Group Normalization** — Rescale baseline scores within each experience group
   - Prevents one large group (e.g., Seniors) from dominating globally

2. **UCB Exploration** — Boost under-represented groups
   - Formula: `c * sqrt(log(total) / (count + 1))`
   - Higher bonus for groups with fewer placements so far

3. **Candidate Pooling** — Select top 50 candidates per job for reranking
   - Expensive reranking only on likely candidates (keeps runtime reasonable)

4. **MMR Diversity** — Greedily select top-k to maximize relevance + group diversity
   - Formula: `lambda * relevance - (1 - lambda) * diversity_penalty`
   - Ensures representation from multiple groups while staying relevant

5. **Global Exposure Tracking** — Update group exposure counts per job
   - Affects UCB bonus for next job (fairness across all jobs)

### 7. Evaluation
Compute quality and fairness metrics:

**Quality Metrics** (NDCG@10, Precision@10):
- NDCG: Ranking quality (ideal ordering vs actual)
- Precision: Fraction of relevant candidates in top-10

**Fairness Metrics**:
- **Exposure Disparity** — Max % difference between groups in top-10
- **Fresher Share** — % of Freshers in top-10 (indicator of bias)
- **Group Distribution** — Distribution of all groups in top-10

### 8. Visualization
Generate 3 plots comparing baseline vs fairness-aware ranking:
- Fairness vs accuracy trade-off
- Experience group distribution
- Score distribution

## 🧪 Testing & Validation

### Quick Test
Validate setup before full run:
```bash
python smoke_test.py
```

Checks:
- Data files exist or can be created
- Output directories writable
- Dependencies installed
- Configuration reasonable (weights sum to 1, params in ranges)

### Full Test
Run the complete pipeline and verify outputs:
```bash
python main.py
ls -la outputs/          # Should have 4 CSVs/JSONs
ls -la outputs/plots/    # Should have 3 PNGs
```

## 🐛 Troubleshooting

### "No data files found"
**Error:** `FileNotFoundError: No job_descriptions.csv or jobs_cleaned.csv found.`

**Solution:** Ensure data files are in the `data/` directory:
- Either: `data/freelancer_profiles.csv` + `data/job_descriptions.csv`
- Or: Use legacy fallback with `data/jobs_cleaned.csv`

### "Out of memory"
**Error:** `MemoryError` during embedding generation

**Solution:**
- Reduce `BATCH_SIZE` in config.py (default 32)
- Or run on a machine with more RAM (~8GB recommended)

### "Embedding model download fails"
**Error:** `HuggingFace connection timeout`

**Solution:** 
- Download model manually:
  ```python
  from sentence_transformers import SentenceTransformer
  model = SentenceTransformer('all-MiniLM-L6-v2')
  ```
- Check internet connection
- If offline, copy cached model from another machine: `.cache/huggingface/`

### "Wrong number of columns"
**Error:** `ValueError: freelancer data is missing required columns`

**Solution:** 
- Check CSV headers match `config.FREELANCER_REQUIRED_COLUMNS` and `config.JOB_REQUIRED_COLUMNS`
- Required: `freelancer_id, text` for freelancers; `job_id, title, description` for jobs
- Optional: `name, years_experience, experience_group, skills` for freelancers

## 📖 For the Viva

### Key Points to Explain

1. **Pipeline Design** — Why each stage? (Modularity, reusability, testability)
2. **Feature Selection** — Why these 5 features? (Semantic match, skill match, experience fit)
3. **Fairness Mechanism** — Why group normalization + UCB + MMR? (Addresses different bias sources)
4. **Trade-offs** — Accuracy vs fairness (plot shows both metrics matter)
5. **Reproducibility** — Smart caching, fixed seed, configuration-driven

### Demo Commands
```bash
# Show pipeline works
python main.py --baseline

# Show fairness impact
python main.py --fairness

# Show metrics
python main.py --eval
cat outputs/evaluation_metrics.json
cat outputs/bias_metrics.json
```

### Talking Points
- "This approach decomposes fairness into three components..."
- "Group normalization ensures fair comparison within each experience level..."
- "UCB exploration prevents indefinite neglect of minorities..."
- "MMR balances relevance with diversity in the final ranking..."

## 📝 License & Attribution

This is an academic project for final-year submission.

## 🤝 Support

For issues, check:
1. `smoke_test.py` for common configuration problems
2. `outputs/evaluation_metrics.json` for pipeline success indicators
3. Console output for detailed error messages

---

**Last Updated:** May 2026  
**Status:** Ready for production  
**Test Coverage:** Full pipeline end-to-end tested
