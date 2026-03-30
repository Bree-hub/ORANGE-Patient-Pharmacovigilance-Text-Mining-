# ORANGE-Patient-Pharmacovigilance-Text-Mining-
# Text Mining in Healthcare — Drug Review Analysis

A text mining project analysing 65,000+ patient drug reviews using Orange Data Mining, structured around the CRISP-DM framework. The project runs two parallel analytical branches on the same dataset.

---

## Project Overview

| Branch | Type | Question |
|---|---|---|
| Supervised | Classification | Can review text alone predict whether a patient had a positive or negative drug experience? |
| Unsupervised | Topic Modelling | What adverse event themes emerge from negative Birth Control drug reviews, and what safety signals do they reveal? |

---

## Tools & Technologies

- **Platform:** Orange Data Mining 3.x
- **Add-on:** Orange3-Text
- **Framework:** CRISP-DM
- **Dataset:** drugsComTrain — Drugs.com patient reviews

---

## Dataset

- 65,000+ reviews after augmentation
- 900+ conditions, 3,400+ drugs
- Key columns: `drugName`, `condition`, `review`, `rating`, `sentiment` (derived)
- Sentiment label rule: Rating ≤ 4 = Negative | Rating ≥ 7 = Positive | 5–6 Excluded

---

## Supervised Branch

**Goal:** Classify drug review sentiment using text features only.

**Pipeline:**
```
CSV Import → Select Rows → Corpus → Preprocess Text
→ Bag of Words (TF-IDF) → Select Columns → Test & Score
→ Confusion Matrix | ROC Analysis
```

**Classifiers compared:**
- Naive Bayes
- Logistic Regression
- Random Forest *(best performer)*
- SVM (Linear kernel)

**Evaluation:** 10-fold stratified cross-validation | Primary metric: MCC

| Model | AUC | MCC |
|---|---|---|
| Random Forest | 0.806 | 0.436 |
| Logistic Regression | 0.799 | 0.387 |
| Naive Bayes | 0.788 | 0.383 |
| SVM | 0.538 | 0.042 |

> Baseline results. Expected AUC 0.93–0.97 after full optimisation.

---

## Unsupervised Branch

**Goal:** Detect adverse event themes in negative Birth Control drug reviews using NMF topic modelling.

**Drugs analysed (negative reviews only):**

| Drug | Reviews |
|---|---|
| Etonogestrel | 1,692 |
| Ethinyl estradiol / Norethindrone | 1,258 |
| Nexplanon | 1,178 |
| Levonorgestrel | 803 |

**Pipeline (per drug):**
```
CSV Import → Select Rows [drug + negative] → Corpus
→ Preprocess Text (per-drug tuned) → Bag of Words
→ Word Cloud | NMF Topic Modelling → Corpus Viewer
```

**Key findings:**
- 7 class-level adverse effects confirmed across all 4 drugs (mood swings, bleeding, weight gain, acne, headache, nausea, libido)
- Drug-specific pharmacovigilance signals identified including pregnancy anxiety in implant users, ovarian cyst co-occurring with depression, and standalone libido suppression

---

## Preprocessing Configuration

| Step | Setting |
|---|---|
| Tokenisation | Word Punctuation |
| Normalisation | WordNet Lemmatizer |
| Stop words | English built-in + custom medical file |
| N-gram range | 1–2 |
| Document frequency | Absolute min 5–10 (per corpus) |
| Weighting | TF-IDF (Smooth IDF + L2) |

---

## Key Lessons

- **Data leakage:** Rating column caused AUC = 1.000 — removed via Select Columns
- **Stop word language:** Defaulted to Finnish — changed to English
- **Topic modelling:** LDA failed on short review texts — NMF used instead
- **HTML artifacts:** `&#039;` encoding produced noise tokens — fixed by switching tokeniser
- **Class imbalance:** Original 72/27 split — augmented to 54/45 using genuine reviews

---

## Project Structure

```
├── data/
│   └── drugsComTrain.csv
├── stopwords/
│   └── custom_medical_stopwords.txt
├── models/
│   └── saved Orange .pkcls model files
├── outputs/
│   ├── Full_Text_Mining_Report.docx
│   └── TextMining_CRISPDM_Presentation.pptx
└── README.md
```

---

## References

- [Orange Data Mining](https://orangedatamining.com)
- [drugsComTrain Dataset — UCI ML Repository](https://archive.ics.uci.edu/ml/datasets/Drug+Review+Dataset+%28Drugs.com%29)
- CRISP-DM Process Framework
