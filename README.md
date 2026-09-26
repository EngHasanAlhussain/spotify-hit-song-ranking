# Ranking Models for Top Hit Song Prediction

Learning-to-rank models for predicting daily Spotify Top-200 chart positions, and an analysis of what actually drives a song onto the chart.

Group project for Applied Machine Learning (University of Edinburgh, Sem 1 2025).

**Authors:** Rida Pan, Alfin Pradana, Nico R, Hasan Alhussain

## Summary

We treat "will this song chart, and how high" as a learning-to-rank problem rather than a classification problem. Songs are bucketed into five relevance tiers (Top 10 / 20 / 50 / 100 / 200) per day, and six models are compared: Multinomial Logistic Regression, KNN, Random Forest, LightGBM, MLP, and CatBoost (trained listwise with the YetiRank loss).

**Headline result:** CatBoost's listwise ranking objective gave the strongest validation performance, with **NDCG@10 = 0.6240** and **NDCG@20 = 0.6104**. Performance drops substantially on the test period (8 months later), and feature importance shows *year* and *season* are the most influential signals — audio features (danceability, energy, etc.) contribute comparatively little.

## Data

The dataset (~650k daily Top-200 chart entries with audio, artist, and temporal features, 2017-2023) is **not included** in this repo — it's a 157MB CSV, over GitHub's file size limit, and its exact redistribution rights aren't confirmed. To reproduce:

1. Obtain a Spotify daily Top-200 chart dataset with audio features (columns expected: `Title`, `Artists`, `Rank`, `Date`, `Points (Total)`, and audio features `Danceability`, `Energy`, `Loudness`, `Acousticness`, `Instrumentalness`, `Speechiness`, `Valence`).
2. Place it at `data/Spotify_Dataset_V3.csv`.
3. Run the notebook top to bottom.

See `data/README.md` for the full expected schema.

## Method

- **Cleaning:** deduplicated multi-artist entries, capped physically-invalid loudness values (>0 dB) to 0, chronological train/validation/test split (2017-2021 / Jan-Aug 2022 / Sep 2022-May 2023) to avoid temporal leakage.
- **Feature engineering:** cyclical encoding for season/day-of-week, multi-hot encoding for artist nationality/continent, Word2Vec embeddings (50-dim) for artist and title text.
- **Models:** see `notebooks/31_AML_CODE.ipynb` for the full pipeline; parameters for every model are listed in the report appendix.
- **Evaluation:** NDCG@{10,20,50}, since it weights errors near the top of the chart more heavily than errors further down — the right metric when getting the Top 10 right matters more than the Top 150.

## Results

| Model | Valid NDCG@10 | Valid NDCG@20 | Valid NDCG@50 | Test NDCG@10 | Test NDCG@20 | Test NDCG@50 |
|---|---|---|---|---|---|---|
| **CatBoost** | **0.6240** | **0.6104** | 0.6372 | 0.3358 | 0.3699 | 0.4834 |
| LightGBM | 0.5564 | 0.6021 | **0.6385** | 0.3149 | 0.3499 | 0.4688 |
| Random Forest | 0.5838 | 0.5857 | 0.6171 | 0.3441 | 0.3666 | 0.4927 |
| Logistic Regression | 0.6020 | 0.5698 | 0.6201 | 0.3718 | 0.3734 | 0.4570 |
| KNN | 0.4920 | 0.5341 | 0.6210 | 0.3659 | 0.4051 | **0.5037** |
| MLP | 0.3824 | 0.4116 | 0.4905 | **0.3800** | **0.4093** | 0.4806 |

All models degrade on the test set, tree-based models most sharply — consistent with the feature-importance finding that *year* is the dominant signal, so a model trained on 2017-2021 loses relevance as it gets further from its training window.

## Repo structure

```
.
├── notebooks/
│   └── 31_AML_CODE.ipynb      # full pipeline: cleaning, EDA, feature engineering, modeling, evaluation
├── report/
│   └── report.pdf              # full written report (methodology, EDA, discussion, references)
├── data/
│   └── README.md               # expected schema (data itself not included, see above)
├── requirements.txt
└── README.md
```

## Setup

```bash
pip install -r requirements.txt
```

Then place your dataset at `data/Spotify_Dataset_V3.csv` and run `notebooks/31_AML_CODE.ipynb` top to bottom (Kernel → Restart & Run All).

## License

MIT — see [LICENSE](LICENSE).

## Generative AI use

ChatGPT was used for grammar/spelling proofreading of the written report. All modeling, analysis, and code are the authors' own work.
