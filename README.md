# ISC353 Final Report(最終レポート)

Fatigue factors by sleep-duration group(睡眠時間グループ別の疲労要因の分析)

## Research question(研究課題)

Does the effect of each behavioural factor on next-day fatigue differ across sleep-duration groups?
(各行動要因が翌日の疲労に与える影響は、睡眠時間のグループによって違うか？)

For each sleep group, a random forest (interpreted with SHAP) and a linear regression (coefficients in original units) are fitted separately and compared.
(睡眠時間のグループごとにランダムフォレストと線形回帰を作り、比べる。ランダムフォレストは SHAP で、線形回帰は元の単位の係数で解釈する。)

## Files(ファイル)

- `analysis.ipynb`:Analysis notebook(分析ノートブック)
- `data/bedtime_screentime_sleep_debt.csv`:Dataset, 8,500 rows × 18 columns(データ、8,500行×18列)
- `data/README.md`:Data source and license(データの出典とライセンス、CC BY-SA 4.0)

## How to run(実行方法)

Python 3.12. Libraries(ライブラリ):pandas, numpy, scikit-learn, matplotlib, shap, statsmodels, scipy, ipykernel

Run all cells from the top in order. The grid search in Step 8 takes about 5 minutes.
(セルを上から順にすべて実行する。Step 8 のグリッドサーチは約5分かかる。)

## Progress(進捗)

### Done(完了)

- Step 1:Data collection(データ収集)
- Step 2:Data checks and EDA(データ確認と探索的分析). Types, missing values, duplicates, descriptive statistics, boxplots, histograms, correlations, values stuck at bounds, attribute columns.
- Step 3:Feature selection(特徴量の選択). 10 behavioural features.
- Step 4:Sleep groups(睡眠時間のグループ分け). 1-hour bins:-5h, 5-6h, 6-7h, 7-8h, 8h+
- Step 5:Train/test split(学習用とテスト用に分割). 80/20, stratified by group(グループで層化).
- Step 6:Baseline(ベースライン). Random forest and linear regression on all people(全員).
- Step 7:Models per group(グループごとのモデル)
- Step 8:Grid search(グリッドサーチ). max_depth × min_samples_leaf, 5-fold CV, shown as heatmaps.

Decisions(決めたこと):outliers and values stuck at bounds are left as is(外れ値と上限・下限への張り付きはそのまま). All 10 features are used in the linear regression(線形回帰は10個の特徴量をすべて使う).

### Not done yet(未完了)

- Step 9:Test-set evaluation(テストデータでの評価). MSE and R² per group, against the baseline. Compare the default and the tuned random forest.
- Step 10:Interpretation(解釈)
  - SHAP for each group's random forest(グループごとのランダムフォレストの SHAP)
  - Linear-regression coefficients per group in original units(グループごとの線形回帰の係数、元の単位)
  - Revisit the collinearity between phone use and sleep latency(スマホ使用と入眠時間の相関、r=0.86 を再検討)
  - Decide whether to add a model without sleep_latency and snoozes(sleep_latency と snoozes を除いたモデルを追加するか決める)
  - Robustness check with other bins(別の区切りでの頑健性の確認):30-minute bins, cuts close to sleep_debt_category
- Step 11:Statistical tests(統計的検定). Interaction terms and a paired CV comparison of models. Check first whether these methods were covered in class(授業で扱ったか先に確認する).
- Step 12:Summary(まとめ)
