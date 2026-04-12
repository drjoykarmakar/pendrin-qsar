# Pendrin (SLC26A4) QSAR Pipeline

**Author:** Dr. Joy Karmakar  
**Date:** April 2026  
**Status:** Proof-of-concept | Expanding dataset

---

## What this is

An open-source cheminformatics pipeline for **pendrin (SLC26A4) inhibitors** — 
a drug target for edema, hypertension, and cystic fibrosis.

Uses RDKit + Machine Learning (Random Forest, XGBoost, Ridge Regression) to 
model structure-activity relationships (QSAR) across 18 literature inhibitors 
from 5 scaffold classes.

---

## Run it instantly (no installation needed)

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/drjoykarmakar/pendrin-qsar/blob/main/pendrin-qsar.ipynb)

Click the badge above → Runtime → Run all → done in ~60 seconds.

---

## Dataset (18 compounds, 5 scaffold classes)

| Scaffold | n | Best IC50 | Reference |
|---|---|---|---|
| Benzofuran | 10 | 0.48 µM (1d) | Cil et al. Eur J Med Chem 2025 |
| Pyrazolopyridine | 4 | 3.1 µM (Zhu-19) | Zhu et al. Bioorg Med Chem Lett 2019 |
| Pyrazolothiophene urea | 1 | 1.8 µM (PDSinh-C01) | Haggie et al. FASEB J 2016 |
| Anthranilic acid | 1 | 2.5 µM (PDSinh-A01) | Haggie et al. FASEB J 2016 |
| Oxazolone | 1 | 4.7 µM (YS-01) | Park et al. J Allergy Clin Immunol 2019 |
| Niflumic acid | 1 | 15.5 µM (NFA) | Wang et al. Nat Commun 2024 |

---

## Results (LOO-CV, n=16 unique structures)

| Model | Q² | RMSE | Pearson r |
|---|---|---|---|
| Random Forest | ~-0.17 | ~0.45 | ~+0.02 |
| XGBoost | ~-0.58 | ~0.53 | ~+0.11 |
| Ridge Regression | ~-0.25 | ~0.47 | ~+0.09 |

**Q² < 0 is expected** with n=16 and only 1.5 log units of activity range.  
This is a proof-of-concept pipeline — accuracy will improve as the dataset grows.

---

## Figures

| Figure | Description |
|---|---|
| fig1 | Chemical space (MW, LogP, TPSA vs pIC50) |
| fig2 | LOO-CV predicted vs experimental pIC50 |
| fig3 | Descriptor importance + correlation heatmap |
| fig4 | Activity distribution by scaffold class |

---

## Data quality fixes applied

| Issue | Fix |
|---|---|
| PDSinh-C01 SMILES invalid (kekulization error) | Corrected |
| 1g SMILES was duplicate of 1c | Fixed to 2,3-dichloro regioisomer |
| NFA SMILES had benzene ring (wrong) | Corrected to pyridine (true niflumic acid) |
| NFA IC50 updated | 15.5 µM from Wang 2024 structural paper |
| Zhu-17/19/22 identical SMILES | Averaged (unresolved stereochemistry) |

---

## Files

| File | Description |
|---|---|
| `pendrin-qsar.ipynb` | Full pipeline notebook |
| `pendrin_inhibitors.csv` | Raw dataset (18 compounds) |
| `pendrin_compound_table_pub.csv` | Publication-ready table with descriptors |
| `fig1–4.png` | Publication figures (300 dpi) |

---

## Citation

Karmakar, J. (2026). *pendrin-qsar: RDKit + ML pipeline for SLC26A4 inhibitors* (v2.0).  
GitHub. https://github.com/drjoykarmakar/pendrin-qsar

---

## References

- Cil et al. *Eur J Med Chem* 283, 117133 (2025)
- Haggie et al. *FASEB J* 30, 2187–2197 (2016)
- Zhu et al. *Bioorg Med Chem Lett* 29, 2019
- Park et al. *J Allergy Clin Immunol* 144, 1425–1428 (2019)
- Wang et al. *Nat Commun* 15, 346 (2024)
