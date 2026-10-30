# ISC353 Final Report(最終レポート)

Fatigue factors and sleep duration(睡眠時間と疲労要因の分析)

## Research question(研究課題)

How does the effect of each behavioural factor (e.g., bedtime phone use, caffeine) on next-day fatigue change with sleep duration?
(寝る前のスマホ利用やカフェインなどの各要素が翌日の疲労に与える影響は、睡眠時間によってどのように変わるか？)

Moving windows over sleep duration are the main method, following Oliver's suggestion. Linear regression is fitted in each 1.0h window moved by 0.1h, and random forest with SHAP is used as a check.
(Oliver の提案に従い、睡眠時間の移動窓を中心の方法にする。幅1.0時間の窓を0.1時間ずつ動かし、窓ごとに線形回帰を作る。確認のため RF と SHAP も使う。)

## Main results(主な結果)

- **Final model(最終モデル):** moving-window linear regression. Test MSE 0.33470, R² 0.9543, about half of one linear regression for everyone (0.64775).
  (移動窓の線形回帰。テストのMSEは0.33470、R²は0.9543で、全員で1つの線形回帰（0.64775）の約半分)
- **The effects change with sleep(効果は睡眠時間によって変わる):** 7 of 10 features changed at least once. 14 of 50 neighbor comparisons exclude 0, against about 2.5 expected by chance.
  (10特徴量のうち7つで1回以上変わった。隣どうしの比べ50回のうち14回で0を含まず、偶然の目安2.5回より多い)
- **Sleep-related factors(睡眠に関係する要因):** sleep latency, sleep hours, deep sleep and snoozes are strongest at 4-6h and weaken from 6h to 8h.
  (寝つくまでの時間、睡眠時間、深い睡眠、スヌーズは4〜6時間で一番強く、6時間から8時間にかけて弱まる)
- **Phone use, screen brightness and caffeine(スマホ、画面の明るさ、カフェイン):** about the same up to 7h, smaller only from 7h to 8h.
  (7時間まではほぼ同じで、7時間から8時間でだけ小さくなる)
- **No change shown(変わるとは言えない):** blue light filter, physical activity and REM sleep.
  (ブルーライトフィルター、運動、レム睡眠)
- **Random forest agrees(RF も同じ形):** SHAP slopes correlate with the linear regression curves at 0.89-0.99 for 6 features.
  (SHAP の傾きと線形回帰の曲線の相関は6つの特徴量で0.89〜0.99)
- **Best model by range(区間別の最良モデル):** choosing a model per sleep range (test MSE 0.33042) was not clearly better than the moving-window linear regression.
  (睡眠時間の区間ごとにモデルを選ぶ方法（テストのMSE 0.33042）は、移動窓の線形回帰よりはっきり良いとは言えなかった)

Details and limitations are in Step 11 of the notebook(詳細と限界はノートブックの Step 11).

## Files(ファイル)

- `analysis.ipynb`:Analysis notebook with outputs(実行結果つきの分析ノートブック)
- `bedtime_screentime_sleep_debt.csv`:Dataset, 8,500 rows × 18 columns(データ、8,500行×18列)
- `figures/`:Figures for the report, created by the notebook(ノートブックが作るレポート用の図)
  - `window_lr_coef.png`:Moving-window linear regression coefficients with 95% bootstrap bands(移動窓の線形回帰の係数と95%の帯)
  - `window_lr_coef_diff.png`:Coefficient change between neighboring windows(隣どうしの窓の係数の差)
  - `window_rf_shap_slope.png`:Random forest SHAP slope vs linear regression coefficient by window(窓ごとのRFのSHAPの傾きと線形回帰の係数)
  - `window_rf_shap_share.png`:Share of mean |SHAP| by window(窓ごとの平均|SHAP|の割合)
  - `range_cv.png`, `range_cv_best.png`:CV MSE and R² by sleep range(区間ごとの交差検証MSEとR²)
  - `range_test.png`:Test MSE and R² by sleep range(区間ごとのテストのMSEとR²)
  - `final_test_pred.png`:Actual vs predicted fatigue of the final model on test data(最終モデルのテストデータでの実際の疲労と予測)
  - `coef_by_group.png`:Appendix, 1-hour group coefficients on the moving-window curves(付録：1時間グループの係数と移動窓の曲線)

## Data(データ)

- Source(出典):Sleep Debt and Screen Time: Late-Night Phone Habits (Kaggle, samartalwar)
  https://www.kaggle.com/datasets/samartalwar/sleep-debt-and-screen-time-late-night-phone-habits
- License(ライセンス):CC BY-SA 4.0 https://creativecommons.org/licenses/by-sa/4.0/
- Changes(変更):None(なし). The file is redistributed as downloaded(ダウンロードしたまま再配布).
- The data is simulated, not records of real people(シミュレーションで作られたデータで、実在の人の記録ではない).

## How to run(実行方法)

Python 3.12. Libraries(ライブラリ):pandas, numpy, scikit-learn, matplotlib, shap, statsmodels, scipy, ipykernel

Run all cells from the top in order. The grid search in Step 5, the bootstrap in 7-2, the tuning in 8-1 and the CV in 9-1 take the longest.
(セルを上から順にすべて実行する。時間がかかるのは Step 5 のグリッドサーチ、7-2 のブートストラップ、8-1 の設定選び、9-1 の交差検証。)

## Steps(ステップ)

- Step 1:Data collection(データ収集)
- Step 2:Data understanding(データの理解)
- Step 3:Preprocessing(前処理). 10 behavioural features(特徴量10個)
- Step 4:Train/test split(学習とテストに分ける). 80/20, stratified by 10-minute sleep bins(10分ごとの睡眠時間で層化)
- Step 5:Baseline(ベースライン). One linear regression and one tuned random forest for everyone(全員で線形回帰と調整したRFを1つずつ)
- Step 6:Moving windows(移動窓). Window rules, and width chosen by CV (1.0h)(窓のルールと、交差検証で選んだ幅)
- Step 7:Moving-window linear regression(移動窓の線形回帰). Coefficient curves, bootstrap bands, neighbor comparisons(係数の曲線、ブートストラップの帯、隣どうしの比べ)
- Step 8:Moving-window random forest(移動窓のRF). Settings chosen by CV, SHAP slopes(交差検証で選んだ設定、SHAPの傾き)
- Step 9:Comparing models by sleep range(区間ごとのモデルの比較). 10-fold CV, best model by range, Nadeau-Bengio t-test(10分割交差検証、区間別の最良モデル、Nadeau–Bengio のt検定)
- Step 10:Test evaluation(テストデータでの評価). Used once, final model chosen by a fixed rule(1回だけ使い、決めておいたルールで最終モデルを選ぶ)
- Step 11:Conclusion and limitations(結論と限界)
- Appendix:Results of the original 1-hour groups(付録：元の計画の1時間グループの結果)
