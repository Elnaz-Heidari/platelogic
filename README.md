# PlateLogic  
## Constraint-Aware Recipe Rewriting with NLP & LoRA Fine-Tuning

PlateLogic is a small, laptop-friendly NLP project that demonstrates an end-to-end pipeline for **data cleaning, annotation, QA, fine-tuning, and evaluation** of a language model under **explicit nutritional constraints**.

The task is to rewrite cooking recipes so that they become **high-protein and low-carb**, while preserving the core flavor and main protein source.

This project emphasizes **model behavior evaluation and QA**, not just generation.

---

## 🔍 Project Objectives

The goal of PlateLogic is to showcase practical ML engineering skills:

- Structured data cleaning and filtering  
- Manual and rule-based annotation  
- Constraint-driven QA and disagreement analysis  
- LoRA fine-tuning on a small LLM  
- Quantitative evaluation of base vs fine-tuned models  
- Transparent reporting of failures and limitations  

---

## 🧠 Task Definition

**Input:**  
A cooking recipe (title, ingredients, steps)

**Output:**  
A rewritten version of the recipe that:

- Preserves the main protein  
- Avoids high-carb and starchy ingredients  
- Avoids added sugars  
- Maintains the core flavor profile  

---

## 🧱 Pipeline Overview
```
Raw Recipes (Food.com)
        ↓
Data Cleaning & Filtering
        ↓
Annotation Pool Sampling
        ↓
Manual + Rule-Based Annotation
        ↓
QA & Disagreement Analysis
        ↓
Training Pair Construction
        ↓
LoRA Fine-Tuning (Qwen 0.5B)
        ↓
Evaluation (Base vs LoRA)

---

## 📁 Repository Structure
platelogic/
├── data/
│   ├── platelogic_annotation_ready.csv
│   ├── platelogic_annotated_300.csv
│   ├── platelogic_eval_results.csv
│   └── platelogic_eval_summary.json
│
├── notebooks/
│   ├── 01_data_cleaning.ipynb
│   ├── 02_annotation_and_QA.ipynb
│   ├── 03_finetune_lora.ipynb
│   └── 04_evaluation.ipynb
│
├── models/
│   └── platelogic_qwen_lora/
│
├── requirements.txt
└── README.md

## 🧹 Data Cleaning & Filtering

Dataset: Food.com RAW_recipes (Kaggle)

Parsed structured fields using ast.literal_eval

Converted nutrition values into grams

Applied strict structural and nutritional filters:

3–25 ingredients

≥3 steps

150–900 kcal

10–120g protein

≤80g carbs

≥25% calories from protein

≤25% calories from carbs

Semantic filtering for protein anchors

Result:
~9,192 clean, high-quality recipes ready for annotation.

✍️ Annotation & QA
Manual Annotation

100 recipes manually annotated

Labels include:

Protein and carb sources

Constraint compliance

Modification potential

Rewritten low-carb steps

Complexity score

Rule-Based Teacher

Ingredient scanning

Macro-based constraint checks

Automatic labeling for consistency

QA Report

Compared human vs rule-based labels

Identified ~300 disagreement cases

Demonstrates annotation QA and error analysis

🔧 Model Fine-Tuning

Base model: Qwen1.5-0.5B-Chat

Fine-tuning method: LoRA

Target modules:

q_proj, k_proj, v_proj, o_proj

gate_proj, up_proj, down_proj

Training setup:

~300 instruction–response pairs

CPU-friendly training

Prompt format preserved exactly during evaluation

📊 Evaluation Results

Evaluation performed on 50 held-out recipes (not used in training).

Metrics
Metric	Base Model	LoRA Model
Pass Rate (all constraints)	46%	10%
Protein Retention Rate	82%	92%
High-Carb Violation Rate	38%	90%
HP/LC Cue Mentions	32%	84%
Key Findings

The LoRA model learned stylistic cues (“high-protein”, “low-carb”)

Protein retention improved

Constraint compliance worsened significantly

High-carb ingredients were frequently reintroduced

This highlights a common failure mode of small LLM fine-tuning:
surface-level compliance without rule generalization.

🧪 Why This Matters

Rather than hiding poor results, this project demonstrates:

Proper held-out evaluation

Explicit failure analysis

Quantitative QA metrics

Honest reporting of model limitations

This mirrors real-world ML workflows where evaluation often reveals unexpected regressions.

🚧 Limitations & Future Work

Expand training dataset (≥1k examples)

Stronger constraint enforcement in labels

Higher LoRA rank and dropout

Larger base model (1.5B+)

Post-generation rule-based filtering

Optional Gradio demo UI

🛠 Tech Stack

Python

Pandas, NumPy

Hugging Face Transformers

PEFT (LoRA)

Jupyter Notebooks

👤 Author

Fatemeh (Elnaz) Heidari
NLP / Data Annotation / QA
GitHub: https://github.com/Elnaz-Heidari
LinkedIn: https://www.linkedin.com/in/fatemeh-heidari-69900284