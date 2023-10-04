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

##タスク3:

##タスク4:

##タスク5:

##タスク6:

**補足資料**
