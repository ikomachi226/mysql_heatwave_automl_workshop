# (参考)その他のHeatWave AutoML利用例

### データファイル(.csv)の利用方法
1. GitHubから該当のCSVファイルをダウンロードします。
2. OCIコンソールのメニューから[ストレージ]-[バケット]を選択します。
   ![bucket_menu](./image/bucket_menu.png)
   
3. [バケットの作成]を選択し、任意のバケット名を指定し作成します。
    ***バケット名、ネームスペースを控えておきます***
   ![bucket_name](./image/bucket_name.png)

4. [アップロード]ボタンをクリックし、CSVファイルをアップロードします。

5. [事前認証済リクエストの作成]を選択して作成されたURLをコピーします。
   ![bucket_par_request](./image/bucket_par_request.png)
   ![bucket_create_parrequest](./image/bucket_create_parrequest.png)
   ![create_par_request](./image/create_par_request.png)
   
7. コンピュートインスタンスにCloud Shellでログインし、前の手順で作成したURLを指定してバケットに格納したファイルを取得します。
  ```
  $ wget https://objectstorage.ap-tokyo-1.oraclecloud.com/n/<ネームスペース>/b/<アップロードしたcsvファイル名>
  ```
7. MySQL ShellでMySQL HeatWaveに接続します。
  ```
  mysqlsh -u<ユーザ名> -p -h<HeatWaveインスタンスのエンドポイント> --sql
  ```
8. 新規にテーブルを作成します。（DBは必要に応じて作成してください）

9. MySQLにデータを格納します。
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
