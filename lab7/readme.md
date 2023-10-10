# (参考)その他のHeatWave AutoML利用例

### csvファイルの利用方法

### 分類
#### データ
  - XXXX.csv
  - XXXX.csv
#### モデルの生成
  ```
  CALL sys.ML_TRAIN('XXXX', 'xxxx', JSON_OBJECT('task', 'xxxx'), @model_xxxx);
  ```

### 回帰
#### ダイアモンドの価格予測
#### データ
  - diamond-train.csv
  - diamond-test.csv
#### モデルの生成
  ```
  CALL sys.ML_TRAIN('heatwaveml_bench.diamonds_train', 'price', JSON_OBJECT('task', 'regression'), @model_diamonds);
  ```

### 時系列予測
#### データ
  - XXXX.csv
  - XXXX.csv
#### モデルの生成
  ```
  CALL sys.ML_TRAIN('XXXX', 'xxxx', JSON_OBJECT('task', 'xxxx'), @model_xxxx);
  ```

### 異常検知
#### データ
  - XXXX.csv
  - XXXX.csv
#### モデルの生成
  ```
  CALL sys.ML_TRAIN('XXXX', 'xxxx', JSON_OBJECT('task', 'xxxx'), @model_xxxx);
  ```
### レコメンド
#### データ
  - XXXX.csv
  - XXXX.csv
#### モデルの生成
  ```
  CALL sys.ML_TRAIN('XXXX', 'xxxx', JSON_OBJECT('task', 'xxxx'), @model_xxxx);
  ```
