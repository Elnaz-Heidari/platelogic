# PlateLogic 🍽️  
### High-Protein, Low-Carb Recipe Rewriting with Data Annotation, QA, and LoRA Fine-Tuning

PlateLogic is an end-to-end NLP pipeline built to demonstrate practical skills in:

- Data cleaning & preprocessing  
- Structured manual annotation  
- Rule-based teacher modeling  
- Quality-assurance workflows  
- LoRA fine-tuning on a compact LLM  
- Model evaluation on held-out data  

The task: **rewrite recipes into high-protein, low-carb versions while preserving core flavor**.

This project intentionally uses small models and lightweight methods so it can run on a standard laptop.

---

## 📁 Project Structure
.
├── data/
│ ├── platelogic_annotation_ready.csv # cleaned dataset (~9k recipes)
│ ├── platelogic_annotation_pool_300.csv # sampled pool for annotation
│ ├── platelogic_annotated_300.csv # unified human + rule-based annotations
│ ├── training_pairs.jsonl # instruction–response training data
│ ├── platelogic_eval_results.csv # per-sample evaluation (base vs LoRA)
│ └── platelogic_eval_summary.json # aggregate evaluation metrics
│
├── models/
│ └── platelogic_qwen_lora/ # fine-tuned LoRA checkpoint
│
├── notebooks/
│ ├── 01_cleaning.ipynb # dataset parsing + filtering
│ ├── 02_annotation.ipynb # manual + rule-based annotation
│ ├── 03_finetune_lora.ipynb # LoRA training on Qwen1.5-0.5B-Chat
│ └── 04_evaluation.ipynb # evaluation on held-out set
│
└── app.py (optional future step)

---

## 1. 🧹 Dataset Cleaning

Starting from the **Food.com RAW_recipes** dataset:

- Parsed lists (ingredients, steps, nutrition)
- Converted nutrient %DV → grams  
- Applied structural filters:  
  - 3–25 ingredients  
  - ≥3 steps  
  - 150–900 kcal  
  - 10–120 g protein, 0–80 g carbs  
- Applied ratio constraints:  
  - ≥25% calories from protein  
  - ≤25% calories from carbs  
- Applied semantic filters to detect protein anchors  
- Removed duplicate/suspicious entries

**Output:**  
`platelogic_annotation_ready.csv` (~9,192 recipes)

---

## 2. 📝 Annotation Pipeline

### **Sampling**
Stratified recipes by protein amount → sampled 300 examples.

Output:  
`platelogic_annotation_pool_300.csv`

### **Manual Annotation (100 recipes)**
Two batches of 50 each:

- protein & carb sources  
- macro categories  
- constraint compliance  
- modification potential  
- improved cooking steps  
- complexity score  

### **Rule-Based Teacher**
A lightweight rule-based annotator checked:

- protein presence  
- high-carb violations  
- constraint adherence  

A disagreement report (`platelogic_QA_report.csv`) showed ~311 inconsistencies, used for labeling QA.

### **Unified Dataset**
Human annotations override rule-based outputs →  
`platelogic_annotated_300.csv`

---

## 3. 🧪 Building Training Data

Every annotated row was converted into an **instruction–response** pair:

You are a cooking assistant that rewrites recipes to be high-protein and low-carb.

Instruction:
{input block}

Response:
{improved cooking steps}

Saved as:  
`training_pairs.jsonl` (300 examples)

---

## 4. ⚙️ LoRA Fine-Tuning

Fine-tuned **Qwen1.5-0.5B-Chat** with LoRA:

- Target modules: q_proj, k_proj, v_proj, o_proj, gate_proj, up_proj, down_proj  
- LoRA hyperparameters:  
  - r=8  
  - alpha=16  
  - dropout=0.05  
- Training runtime: ~1 epoch on CPU  
- Loss:  
  - train_loss ≈ 1.09  
  - eval_loss ≈ 0.31  

Checkpoint saved:  
`models/platelogic_qwen_lora/checkpoint-67`

---

## 5. 📊 Evaluation (Base vs LoRA)

Held-out sample size: **50 recipes**

Evaluation checks:

- **Protein retention**  
- **High-carb ingredient violations**  
- **Mentions of high-protein/low-carb cues**  
- **Overall pass rate** (keeps protein AND avoids high-carb ingredients)

### **Results Summary**

```json
{
  "n_eval": 50,
  "base": {
    "pass_rate": 0.46,
    "protein_retention_rate": 0.82,
    "high_carb_violation_rate": 0.38,
    "hp_lc_mention_rate": 0.32
  },
  "lora": {
    "pass_rate": 0.10,
    "protein_retention_rate": 0.92,
    "high_carb_violation_rate": 0.90,
    "hp_lc_mention_rate": 0.84
  }
}
Interpretation

The base model successfully followed constraints ~46% of the time.

The LoRA model dramatically improved protein retention, but

Introduced high-carb violations in 90% of outputs

Spoke extensively about “high-protein” and “low-carb” without enforcing constraints

Overall constraint compliance dropped to 10%

In other words:
the LoRA overfit stylistic cues rather than learning nutritional constraints.

This evaluation step is crucial, and it shows the importance of:

dataset consistency

more fine-grained supervision

potentially larger models for rule-heavy tasks

6. 🚧 Next Steps
A. Improve the training dataset

Clean & enforce constraints in all outputs

Expand dataset to 800–1500 examples

Strengthen negative examples

B. Retrain LoRA with stronger hyperparameters

Use r=16, alpha=32

Increase dropout to 0.1

C. Upgrade to a larger base model

Qwen 1.5B or 4B will generalize much better

D. Add rule-based self-checking

Regenerate if the model violates constraints.

7. 🧑‍🍳 Optional UI

A lightweight Gradio app can wrap the model:

User enters ingredients

Model outputs the rewritten high-protein/low-carb steps

Includes a rule-based violation warning

⭐ Summary

PlateLogic demonstrates:

A complete ML workflow from data → annotation → training → evaluation

Integration of human labels + rule-based QA

LoRA fine-tuning on a compact model

A rigorous evaluation pipeline that reveals both strengths and limitations

This isn’t “just a recipe bot”—it’s a practical showcase of applied NLP engineering with real constraint-checking and model quality analysis.

---

If you want, I can also produce:

- A **short version** for PyPI / HuggingFace  
- A **professional README banner**  
- A **diagram** of the pipeline  
- A **portfolio-friendly summary section**

Just let me know which style you want.
