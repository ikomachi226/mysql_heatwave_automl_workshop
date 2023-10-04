# HeatWave AutoMLを利用する

## はじめに

機械学習の対象データをデータベースにロードするには、以下のステップを実行して必要なスキーマとテーブルをまず作成します。

Python3はすでにコンピュートインスタンスにロードされており、MySQL Shell は前のセクションでインストール済みです。

このステップを実行すると、IrisデータはMySQL HeatWaveに以下のスキーマとテーブルとして格納されます：

  - ml_dataスキーマ： ml_data schema: トレーニングデータセットとテストデータセットのテーブルを含むスキーマ。

  - iris_trainテーブル： トレーニングデータセット（ラベル付き）。特徴カラム（sepal length, sepal width, petal length, petal width）と、基底真理値（ground truth values）を含むクラスターゲット（class target）カラムを含む。

  - iris_testテーブル： テストデータセット（ラベルなし）。特徴列（sepal length, sepal width, petal length, petal width）を含むが、ターゲット列は含まない。

  - iris_validateテーブル： 検証データセット（ラベル付き）。特徴カラム（sepal length, sepal width, petal length, petal width）と、グランドトゥルース値（ground truth value）を含む、入力クラスのターゲットカラムを含む。

このセクションでは、IrisデータをHeatWaveにロードし、HeatWave AutoMLを利用した機械学習モデルの作成〜評価の一連の操作を行います。

##タスク1: HeatWave AutoMLを利用する準備を行います
1. もしCloud Shellを終了している場合はCloud Shellを使ってコンピュートインスタンスにSSH接続します。
```
ssh -i id_rsa opc@<コンピュートインスタンスのパブリックIPアドレス>
```

2. MySQL Shellクライアントツールを使って、以下のコマンドでMySQLに接続します。
```
mysqlsh -uadmin -p -h <MySQL HeatWaveのプライベートIPアドレス> -P3306 --sql
```

**参考**　

このワークショップではMySQL HeatWaveの管理者アカウントを利用していますが、別のMySQLユーザーを使用する場合、

HeatWave AutoMLを使用するためには以下の権限付与が必要になりますので注意してください。
  - 機械学習データセットを含むスキーマの SELECT および ALTER 権限
  ```
  GRANT SELECT, ALTER ON schema_name.* TO 'user_name'@'%'；
  ```

  - HeatWave AutoML ルーチンが存在する MySQL sys スキーマの SELECT および EXECUTE 権限
  ```
  GRANT SELECT, EXECUTE ON sys.* TO 'user_name'@'%';
  ```

##タスク2: トレーニング用データとテスト用データをロードします
1. MySQL HeatWave DBシステムで機械学習スキーマとテーブルを作成するには、以下のコマンドでサンプルデータベースをダウンロードします：

  - この[リンク](./iris-ml-data.txt)をクリックして、iris-ml-data.txt ファイルをローカルマシンにダウンロードします。
  - ローカルマシンから iris-ml-data.txt をメモ帳等のエディタで開きます。
    
2. MySQL Shellにiris-ml-data.txtの内容をコピー＆ペーストし、最後にEnterを押す。（最後の文の確認を忘れないでください）
3. 機械学習スキーマ(ml_data)およびスキーマ配下のテーブルを確認する。
   ```
   use ml_data;
   show tables;
   ```

##タスク3:　機械学習モデルのトレーニングを行う(分類)
1. ML_TRAIN()を実行してモデルのトレーニングを行います。
   ```
   CALL sys.ML_TRAIN('ml_data.iris_train', 'class',JSON_OBJECT('task', 'classification'), @iris_model);
   ```
2. トレーニングが終了すると、モデルハンドルが@iris_modelに出力され、モデルがモデルカタログに格納されます。

   モデルカタログのエントリは以下のクエリで確認することができます。
   ```
   SELECT model_id, model_handle, train_table_name FROM ML_SCHEMA_admin.MODEL_CATALOG;
   ```
3. ML_MODEL_LOAD()を使用してモデルをロードします。
   ```
   CALL sys.ML_MODEL_LOAD(@iris_model, NULL);
   ```
   **モデルを使用する前に、モデルをロードする必要があります。**
   
   **モデルはアンロードするか、HeatWaveクラスタを再起動するまでロードされた状態になります。**

   出力は次のようになります。
   

##タスク4:　 単一データに対する予測と説明


##タスク5:

##タスク6:

**補足資料**
