# Task Code 2.4 — Biochemistry & Oncology: Mutational Impact on Protein Structure & Function

![R](https://img.shields.io/badge/Language-R-276DC3?style=flat&logo=r&logoColor=white)
![RMarkdown](https://img.shields.io/badge/Report-RMarkdown-blue?style=flat)
![HackBio](https://img.shields.io/badge/HackBio-Internship%202025-orange?style=flat)
![License](https://img.shields.io/badge/License-MIT-green?style=flat)

---

## 🎯 Introduction

Protein structures are known to be strongly connected to their functions. However, at the amino acid level, not all amino acids contribute to structure and function equally. [Galardini and colleagues](https://elifesciences.org/articles/31035#abstract) decided to investigate the impact of all possible individual, non-synonymous nonsense mutations on the structure and function of proteins. The functional impact was computed as **SIFT scores** and the structural impact was calculated as **FoldX Score** (in kCal/mol).

| Dataset | Link |
|---|---|
| SIFT Dataset | [sift.tsv](https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/sift.tsv) |
| FoldX Dataset | [foldX.tsv](https://raw.githubusercontent.com/HackBio-Internship/public_datasets/main/R/datasets/foldX.tsv) |

---

## Purpose

The purpose of this analysis is to identify amino acid mutations that are simultaneously harmful to both the **structure** and the **function** of proteins, and to investigate which amino acids are the most impactful when mutated.

---

## ✅ What We Want to Do

- Import and merge the SIFT and FoldX datasets using a unique mutation identifier
- Apply the authors' deleterious thresholds to isolate the most damaging mutations
- Identify which wild-type amino acids generate the most doubly deleterious mutations
- Visualise the frequency of these amino acids using a bar plot and a pie chart
- Describe the biological properties of the most impactful amino acids

---

##📥 Methods

### 1. Data Import
Both `.tsv` files are loaded directly from GitHub. Due to mixed whitespace formatting in the files, `read.table(..., sep = "")` is used instead of `read.delim()` to avoid column mismatch errors.

### 2. Creating a Unique Mutation Key
A new column `specific_Protein_aa` is created in both datasets by concatenating the `Protein` and `Amino_Acid` columns with an underscore:

```
Protein = A5A607 + Amino_Acid = E63D → specific_Protein_aa = A5A607_E63D
```

This composite key ensures that mutations are correctly matched across proteins during the merge — the same mutation code (e.g. `E63D`) can exist in different proteins.

### 3. Merging the Datasets
An **inner join** on `specific_Protein_aa` produces a single dataframe containing only mutations that have both a SIFT score and a FoldX score.

### 4. Filtering Doubly Deleterious Mutations
The authors' criteria are applied:
- **SIFT Score < 0.05** → mutation is deleterious to protein **function**
- **FoldX Score > 2 kCal/mol** → mutation is deleterious to protein **structure**

Mutations satisfying **both** conditions simultaneously are extracted.

### 5. Amino Acid Extraction
The wild-type amino acid is extracted from the `Amino_Acid` column using `substr(Amino_Acid, 1, 1)` — the first character of the mutation notation (e.g. `E` from `E63D` = Glutamic acid).

### 6. Frequency Analysis & Visualisation
A frequency table is generated counting how many doubly deleterious mutations each wild-type amino acid produces. This is visualised as:
- A **bar plot** sorted by descending frequency with a 100-mutation threshold line
- A **pie chart** showing the proportional contribution of each amino acid
- A **SIFT vs FoldX scatter plot** with all mutations color-coded by category

---

## 📊Results

### Doubly Deleterious Mutations
Only a **small fraction** of all mutations satisfies both SIFT < 0.05 AND FoldX > 2 simultaneously, confirming that simultaneous disruption of both structure and function is the exception, not the rule. These mutations are the most biologically dangerous variants and prime candidates for disease-causing mutations.

### Most Impactful Amino Acid
The amino acid appearing most frequently in doubly deleterious mutations occupies **evolutionarily conserved, structurally critical positions** in proteins. Pre-written biological descriptions are provided in the script for the four most commonly observed top amino acids:

| Amino Acid | Key Structural Role | Key Functional Role |
|---|---|---|
| **R — Arginine** | Salt bridges, electrostatic networks | DNA/RNA binding, enzyme active sites |
| **S — Serine** | Hydrogen bonding networks | Phosphorylation sites, serine proteases |
| **G — Glycine** | Backbone flexibility, tight turns | Collagen triple helix, hinge regions |
| **P — Proline** | Structural rigidity, *cis* bonds | Protein-protein interaction interfaces |

### Amino Acids with More Than 100 Occurrences

**Structural property:** These amino acids occupy buried positions in the protein hydrophobic core or form key hydrogen bonds and salt bridges. Any substitution significantly increases the folding free energy (FoldX > 2 kCal/mol), making the protein thermodynamically unstable and prone to misfolding or aggregation.

**Functional property:** These positions are located in or near active sites and are involved in catalysis or cofactor binding. Their low SIFT scores (< 0.05) confirm they are under **strong purifying selection** — nature rarely tolerates any substitution at these positions because it abolishes or severely impairs the protein's biological function.

---

## 🔬 What We Can Retain

> Doubly deleterious mutations are **rare but highly significant** — a single point mutation at the most vulnerable amino acid positions can simultaneously unfold the protein AND destroy its function.

> Amino acids with the highest frequency in doubly deleterious mutations represent the **most vulnerable hotspots** in the human proteome and are strong candidates for disease-causing variants in human genetic disorders.

> These findings directly support **precision medicine** — identifying such positions in patient genomes can guide clinical investigation and therapeutic drug design targeting protein stabilisation.

---

## Skills Learned

- Loading and handling `.tsv` files with mixed whitespace formatting in R
- Creating composite key columns using `paste()` for dataset merging
- Performing inner joins with `dplyr::inner_join()` across two datasets
- Applying multi-condition filters with `dplyr::filter()` to isolate biological subsets
- Extracting substrings with `substr()` to parse amino acid nomenclature
- Building frequency tables with `dplyr::count()` and `arrange()`
- Creating and customising bar plots and pie charts with `ggplot2`
- Combining multiple plots into panels using `patchwork`
- Interpreting SIFT and FoldX scores in the context of protein biochemistry
- Connecting computational results to biological meaning and clinical relevance

---

## 🔧 How to Run
 
### Prerequisites
- **R** (≥ 4.0) and **RStudio** installed
### Steps
 
```r
# 1. Clone or download this repository
# 2. Open task2_4_biochemistry.Rmd in RStudio
# 3. Install pacman if not already installed
install.packages("pacman")
 
# 4. Click "Knit" to generate the full HTML report
# OR run chunk by chunk interactively
```
 
All packages are installed automatically via `pacman::p_load()` — no manual installation needed.
 
---
 
## 📦 Dependencies
 
| Package | Purpose |
|---|---|
| `tidyverse` | Data manipulation (`dplyr`, `tidyr`) + `ggplot2` |
| `ggplot2` | All visualisations |
| `scales` | Axis label formatting (percentages, commas) |
| `patchwork` | Combining multiple plots into panels |
 
---
 
## 🔗 References
 
- Galardini, M. et al. (2017). *The impact of the genetic background on gene deletion phenotypes in Saccharomyces cerevisiae*. [eLife](https://elifesciences.org/articles/31035)
- [HackBio Public Datasets Repository](https://github.com/HackBio-Internship/public_datasets)
- [HackBio Internship 2025](https://github.com/HackBio-Internship)
- [UniProt — Protein Knowledgebase](https://www.uniprot.org)
---

## Authors

**Ange Maxime TCHOUTANG**
HackBio Internship 2025 — Task Code 2.4: Biochemistry & Oncology

## 📜 License
 
This project is licensed under the **MIT License** — see the [LICENSE](LICENSE) file for details.
