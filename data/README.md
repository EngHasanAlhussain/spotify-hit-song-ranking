# Data

The raw dataset used in this project (`Spotify_Dataset_V3.csv`, ~157MB, ~650k rows) is **not included** in this repository:

- It exceeds GitHub's 100MB per-file limit.
- It was originally provided as part of the Applied Machine Learning coursework, and its exact redistribution rights aren't confirmed.

To reproduce the results, obtain a Spotify daily Top-200 chart dataset covering 2017-2023 with the schema below, and place it at `data/Spotify_Dataset_V3.csv`.

## Expected schema

| Column | Type | Description |
|---|---|---|
| `Title` | string | Track title |
| `Artists` | string | Artist name(s), comma-separated for collaborations |
| `Date` | date | Chart date |
| `Rank` | int | Daily Top-200 chart position (1-200) |
| `Points (Total)` | int | Chart points for that day |
| `Danceability` | float | Audio feature, 0-1 |
| `Energy` | float | Audio feature, 0-1 |
| `Loudness` | float | Audio feature, dB (values > 0 are treated as invalid and capped to 0 during cleaning) |
| `Acousticness` | float | Audio feature, 0-1 |
| `Instrumentalness` | float | Audio feature, 0-1 |
| `Speechiness` | float | Audio feature, 0-1 |
| `Valence` | float | Audio feature, 0-1 |

Additional columns present in the original dataset (tempo, key, artist genres/nationality, etc.) are used for feature engineering — see the notebook for the full list.

## Splits used

- **Train:** 2017-2021
- **Validation:** Jan-Aug 2022
- **Test:** Sep 2022-May 2023

The split is chronological (not random) to avoid temporal leakage.
