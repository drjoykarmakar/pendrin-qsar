# Pendrin (SLC26A4) QSAR Pipeline

**Author:** Dr. Joy Karmakar  
**Date:** April 2026  
**Version:** 5.0 (Final)  
**Status:** Proof-of-concept | Actively expanding dataset

---

## Run instantly in Google Colab — no installation needed

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/drjoykarmakar/pendrin-qsar/blob/main/pendrin-qsar.ipynb)

Click the badge → Runtime → Run all → done in ~90 seconds.  
Generates 6 publication figures + 2 CSV files automatically.

---

## What this is

An open-source cheminformatics and machine learning pipeline for
**pendrin (SLC26A4) inhibitors** — a validated drug target for:

- Edema and diuretic resistance
- Hypertension
- Cystic fibrosis
- Asthma and inflammatory lung diseases

The pipeline covers the full QSAR workflow: data curation,
feature engineering, model training, rigorous validation,
applicability domain analysis, conformal prediction, and
virtual screening of novel analogs.

---

## Dataset

**18 raw entries → 16 unique structures** after canonical SMILES
deduplication. All SMILES were manually verified and corrected
against primary literature and ChEMBL.

| Scaffold Class | n | Best IC50 | Source |
|---|---|---|---|
| Benzofuran | 10 | 0.48 µM (1d) | Master et al. *Eur J Med Chem* 2025 |
| Pyrazolopyridine | 2 | 3.3 µM (Zhu-18) | Zhu et al. *Bioorg Med Chem Lett* 2019 |
| Tetrahydropyrazolopyridine | 1 | 2.5 µM (PDSinh-A01) | Haggie et al. *FASEB J* 2016 |
| Pyrazolothiophenesulfonamide | 1 | 1.8 µM (PDSinh-C01) | Haggie et al. *FASEB J* 2016 |
| Oxazolone | 1 | 4.7 µM (YS-01) | Park et al. *J Allergy Clin Immunol* 2019 |
| Niflumic acid | 1 | 15.5 µM (NFA) | Wang et al. *Nat Commun* 2024 |

---

## Methods

### Features
- **11 physicochemical descriptors** via RDKit: MW, LogP, TPSA, HBD,
  HBA, RotB, QED, ArRings, HeavyAtoms, FractionCSP3, MolMR
- **ECFP4 Morgan fingerprints** (radius=2, 2048 bits)
  via `rdFingerprintGenerator` (current RDKit API)

### Models
| Model | Feature set | Notes |
|---|---|---|
| Random Forest | Descriptors + FP | 500 trees, sqrt features |
| XGBoost | Descriptors + FP | 300 estimators, lr=0.05, depth=3 |
| Ridge Regression | Descriptors + FP | alpha=10, StandardScaler |
| PLS Regression | Descriptors only | n=2 components; best for small n |

### Validation
- **LOO-CV** (Leave-One-Out Cross-Validation) — appropriate for n < 20;
  random train/test split not used
- **Y-scrambling** (n=50 permutations) — confirms no chance correlation
- **Scaffold-based external validation** — benzofurans as training set,
  non-benzofurans as external test set
- **Applicability Domain** — Williams plot (leverage vs standardized residuals)
- **Conformal prediction** — 80% and 90% prediction intervals per compound

---

## Results

### LOO-CV performance

| Model | Q² | RMSE | MAE | Pearson r |
|---|---|---|---|---|
| Random Forest | −0.100 | 0.435 | 0.330 | +0.009 |
| XGBoost | −0.392 | 0.490 | 0.360 | +0.151 |
| Ridge | −0.132 | 0.442 | 0.325 | +0.149 |
| PLS (n=2) | −0.468 | 0.503 | 0.448 | −0.163 |

**Q² < 0 is expected** with n=16 and only 1.5 log units of activity
range. These models are proof-of-concept; ≥50 compounds are needed
for predictive QSAR.

### Validation summary

| Test | Result | Interpretation |
|---|---|---|
| Y-scrambling | Real Q²=−0.100 vs 95th pct=+0.071 | No chance correlation confirmed |
| Scaffold split | External Q²=−0.779, RMSE=0.393 | PDSinh-A01 predicted with error=0.001 |
| Applicability Domain | All 16 compounds within AD (h < 2.250) | All predictions trustworthy |
| Conformal prediction | 80% CI = ±0.698 pIC50 (≈5× in IC50) | Quantified uncertainty per compound |

### Virtual screening

13 novel benzofuran analogs were screened using the trained RF model
with 80% conformal prediction intervals and Lipinski Ro5 + Veber
drug-likeness filters.

**Top predicted analog: V-2F4Cl** (2-fluoro, 4-chloro benzyl)  
Predicted IC50 = **0.86 µM** [CI80%: 0.43–1.73 µM]  
Tanimoto similarity to 1d = 0.804

---

## Figures

| Figure | Description |
|---|---|
| `fig1_chemical_space.png` | MW, LogP, TPSA vs pIC50 coloured by scaffold class |
| `fig2_qsar_scatter.png` | LOO-CV predicted vs experimental pIC50 (4 models) |
| `fig3_descriptor_analysis.png` | RF feature importance + descriptor correlation heatmap |
| `fig4_activity_distribution.png` | Potency ranking + scaffold class comparison |
| `fig5_validation.png` | Y-scrambling histogram + Williams plot (AD) |
| `fig6_virtual_screening.png` | Virtual library ranked by predicted potency + novelty plot |

---

## Files

| File | Description |
|---|---|
| `pendrin-qsar.ipynb` | Full Colab notebook |
| `pendrin_inhibitors.csv` | Raw curated dataset (18 entries) |
| `pendrin_compound_table_pub.csv` | Publication table with all descriptors |
| `virtual_screening_results.csv` | 13 virtual analogs with predicted IC50 and CI |
| `fig1–fig6.png` | All figures at 300 dpi |
| `LICENSE` | MIT License |

---

## How to add your own compound

After running the notebook, scroll to the `csv_data` block and add
a new row: MyCompound,CC1=C(C(=O)O)c2cc(OCc3...),2.0,My Lab 2026,Benzofuran

Then Runtime → Run all. The new compound will appear in all
figures and the virtual screening comparison.

---

## Roadmap

- [ ] Expand to ≥50 compounds 
- [ ] Add confirmed inactive compounds (IC50 > 50 µM) as decoys
- [ ] Synthesize and test top virtual screening hit V-2F4Cl
- [ ] Deploy Gradio web app for non-programmer use
- [ ] Structure-based QSAR using pendrin cryo-EM structure
      (Wang et al. *Nat Commun* 2024, PDB: 8E5B)
---

## Citation

If you use or build upon this work, please cite:

> Karmakar, J. (2026). *pendrin-qsar: RDKit + ML pipeline for
> SLC26A4 inhibitors* (v5.0). GitHub.
> https://github.com/drjoykarmakar/pendrin-qsar

---

## References

1. Master RJ, Karmakar J, et al. High potency 3-carboxy-2-methylbenzofuran
   pendrin inhibitors as novel diuretics. *Eur J Med Chem* 283, 117133 (2025).

2. Haggie PM, et al. Inhibitors of pendrin anion exchange identified in a
   small molecule screen increase airway surface liquid volume in cystic
   fibrosis. *FASEB J* 30, 2187–2197 (2016).

3. Zhu J, et al. Synthesis and evaluation of tetrahydropyrazolopyridine
   inhibitors of SLC26A4 (pendrin). *Bioorg Med Chem Lett* 29 (2019).

4. Park J, et al. Novel pendrin inhibitor attenuates airway
   hyperresponsiveness in experimental murine asthma.
   *J Allergy Clin Immunol* 144, 1425–1428 (2019).

5. Wang L, et al. Mechanism of anion exchange and small-molecule
   inhibition of pendrin. *Nat Commun* 15, 346 (2024).

---

## Author

**Dr. Joy Karmakar**    
GitHub: [@drjoykarmakar](https://github.com/drjoykarmakar)

---

*Generated with Python 3, RDKit, scikit-learn, XGBoost | April 2026*

