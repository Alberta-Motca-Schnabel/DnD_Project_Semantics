# D&D 5e Character Sheet Generator & Validator

The workflow is divided into two main stages implemented in separate notebooks:
1.  **Data Preparation:** Cleaning, validating, and formatting raw data into specific tasks.
2.  **Training & Validation:** Fine-tuning and performing semantic evaluation.

---

## Notebook 1: Data Preparation
**File:** `data_preparation.ipynb`

This notebook handles the process to create a high-quality dataset from raw character sheets.

### Key Phases
1.  **Knowledge Base Acquisition**
    * Fetches official rules (Classes, Races, Spells, Stats) from the [D&D 5e API](https://www.dnd5eapi.co).
    * Establishes the "Ground Truth" for validation.

2.  **Strict Validation & Gap Analysis**
    * Validates raw inputs (`dnd_chars_unique.json`) against the API rules.
    * Performs **Gap Analysis** to identify homebrew content or errors.
    * Separates valid characters from invalid ones.

3.  **Data Integration & Logic Repair**
    * Integrates a manual whitelist (`DnD_Integrated.docx`) to recover valid non-APIcontent.
    * **Mathematical Enforcement:** Checks Hit Points (HP), Stat limits (max 20/30), and Spell Slot limits based on level.
    * **Augmentation:** Repairs high-level casters with empty spell lists by injecting valid, class-appropriate spells.

4.  **Task Engineering**
    Splits the data into three distinct instruction-tuning tasks:
    * **Generation:** Create a full JSON sheet from a text description.
    * **Completion:** Fill in `NULL` fields (masked attributes) correctly.
    * **Refusal:** Reject invalid/homebrew inputs (using the negative dataset).

**Output:** `dnd_train.json` and `dnd_test.json`.

---

## Notebook 2: Training & Validation
**File:** `Training&Validation.ipynb`

This notebook executes the training pipeline and the custom evaluation logic.

### Key Phases
1.  **Environment Setup**
    * Uses **Unsloth** for faster training and memory efficiency.

2.  **Baseline Inference (Zero-Shot)**
    * Evaluates base models (Llama-3.2-1B, Qwen, Gemma) before training to establish a performance baseline.

3.  **Fine-Tuning**
    * Trains adapters using **LoRA**.
    * Uses the **Alpaca** prompt format (`### Instruction`, `### Input`, `### Response`).

4.  **Inference & Semantic Validation**
   custom **Rule-Based Evaluator**:
    * **JSON Syntax:** Checks if the output is parseable.
    * **Game Rules:** Verifies if the generated Spells, Stats, and Subclasses match the input Level and Class.
    * **Safety (Refusal):** Verifies if the model correctly refuses invalid inputs.

5.  **Reporting**
    * Generates a detailed comparison report (`PROJECT_FINAL_COMPARISON_REPORT.txt`) analyzing success rates across all tasks.

---

##  Directory Structure


```text
/DnD_Project_Data
├── dnd_chars_unique.json          # Raw input data
├── dataset_integrated/            
├── processed_dataset/             
├── fine_tuned_models_unsloth/     
├── inference_results/             
└── analysis_reports_gap/          
