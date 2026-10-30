# ISC353 Final Report(最終レポート)

Fatigue factors by sleep-duration group(睡眠時間グループ別の疲労要因の分析)

## Research question(研究課題)

Does the effect of each behavioural factor on next-day fatigue differ across sleep-duration groups?
(各行動要因が翌日の疲労に与える影響は、睡眠時間のグループによって違うか？)

For each sleep group, a random forest (interpreted with SHAP) and a linear regression (coefficients in original units) are fitted separately and compared.
(睡眠時間のグループごとにランダムフォレストと線形回帰を作り、比べる。ランダムフォレストは SHAP で、線形回帰は元の単位の係数で解釈する。)

## Main results(主な結果)

- **The effects differ across sleep groups(効果は睡眠時間グループによって異なる).** The F-test of all 40 feature×group interaction terms gives F=53.81, df=(40,6745), p<0.001.
- **Sleep-related factors differ even without the 8h+ group(睡眠に関係する要因は、8h+を除いても効果が異なる):** sleep latency, sleep hours, deep sleep and snoozes. Their effects are strongest around 5.5-6.5h.
- **Phone use, caffeine, screen brightness and blue light filter differ only when 8h+ is included(スマホ、カフェイン、画面の明るさ、ブルーライトフィルターは、8h+を入れたときだけ効果が異なる).** In 8h+, 94.7% of people have fatigue 1.0, so all effects become small.
- **One linear regression per group predicts as well as a random forest(グループごとの線形回帰は、RF と同じくらい当たる):** test MSE 0.3301 vs 0.3407. It is much better than one linear regression for everyone (0.6308).
- **SHAP(SHAP):** sleep latency is the most important feature in every group (38.5-41.2% of mean |SHAP|).

Details and limitations are in Step 14 of the notebook(詳細と限界はノートブックの Step 14).

## Files(ファイル)

- `analysis.ipynb`:Analysis notebook with outputs(実行結果つきの分析ノートブック)
- `bedtime_screentime_sleep_debt.csv`:Dataset, 8,500 rows × 18 columns(データ、8,500行×18列)
- `figures/`:Figures for the report, created by the notebook(ノートブックが作るレポート用の図)
  - `coef_by_group.png`:Linear regression coefficients with 95% CI by group(グループ別の係数と95%信頼区間)
  - `shap_by_group.png`:SHAP importance by group(グループ別のSHAP重要度)
  - `shap_dependence.png`:SHAP value vs feature value(特徴量の値とSHAP値)
  - `coef_windows.png`, `shap_windows.png`:Overlapping windows(重なる区切りでの係数とSHAP)
  - `paired_cv.png`:MSE of each CV fold by model(モデルごとの分割別MSE)

## Data(データ)

- Source(出典):Sleep Debt and Screen Time: Late-Night Phone Habits (Kaggle, samartalwar)
  https://www.kaggle.com/datasets/samartalwar/sleep-debt-and-screen-time-late-night-phone-habits
- License(ライセンス):CC BY-SA 4.0 https://creativecommons.org/licenses/by-sa/4.0/
- Changes(変更):None(なし). The file is redistributed as downloaded(ダウンロードしたまま再配布).
- The data is simulated, not records of real people(シミュレーションで作られたデータで、実在の人の記録ではない).

## How to run(実行方法)

Python 3.12. Libraries(ライブラリ):pandas, numpy, scikit-learn, matplotlib, shap, statsmodels, scipy, ipykernel

Run all cells from the top in order. The grid search in Step 8 takes about 5 minutes.
(セルを上から順にすべて実行する。Step 8 のグリッドサーチは約5分かかる。)

## Steps(ステップ)

- Step 1:Data collection(データ収集)
- Step 2:Data checks and EDA(データ確認と探索的分析)
- Step 3:Preprocessing(前処理). 10 behavioural features(特徴量10個)
- Step 4:Sleep groups(睡眠時間のグループ分け). 1-hour bins:-5h, 5-6h, 6-7h, 7-8h, 8h+
- Step 5:Train/test split(学習用とテスト用に分割). 80/20, stratified by group(グループで層化)
- Step 6:Baseline(ベースライン). One random forest and one linear regression for everyone(全員で1つずつ)
- Step 7:Models per group(グループごとのモデル)
- Step 8:Grid search of random forest(RFのグリッドサーチ). max_depth × min_samples_leaf, 5-fold CV, heatmaps
- Step 9:Evaluation on test data(テストデータでの評価)
- Step 10:Interpretation of linear regression(線形回帰の解釈). Coefficients, 95% CI, correlated features, model without sleep_latency and snoozes
- Step 11:SHAP for the random forests(RFのSHAP)
- Step 12:Robustness check with overlapping windows(重なる区切りでの頑健性の確認), suggested by Oliver. 1-hour windows moved by 30 minutes
- Step 13:Statistical tests(統計的検定). Interaction F-test, paired t-test on 10-fold CV
- Step 14:Summary and limitations(まとめと限界)
