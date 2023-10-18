# (参考)その他のHeatWave AutoML利用例

### データファイル(.csv)の利用方法
1. GitHubから該当のCSVファイルをダウンロードする
2. OCIコンソールのメニューから[ストレージ]-[バケット]を選択する
   ![bucket_menu](./image/bucket_menu.png)
   
3. [バケットの作成]を選択し、任意のバケット名を指定し作成する
    ***バケット名、ネームスペースを控えておきます***
   ![bucket_name](./image/bucket_name.png)

4. [アップロード]ボタンをクリックし、CSVファイルをアップロードする
5. [PARリクエストの作成]を選択する
6. コンピュートインスタンスにCloud Shellでログインし、バケットに格納したファイルを取得する
  ```
  $ wget https://objectstorage.ap-tokyo-1.oraclecloud.com/n/idazzjlcjqzj/b/xxxx.csv
  ```
7. MySQL ShellでMySQL HeatWaveに接続する
  ```
  mysqlsh -u<> -p -h<> --sql
  ```
8. 新規にテーブルを作成する
9. 
  ```
  util.loadDump("/home/opc/tpch_100g", {dryRun: true, resetProgress:true, ignoreVersion:true, schema: "tpch_100g"})
  ```

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
