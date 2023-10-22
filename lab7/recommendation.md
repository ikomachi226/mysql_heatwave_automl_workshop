# レコメンド・システムモデルを作成する

ここでは、ユーザーの好みに基づいた映画をおすすめする機械学習モデルを作成します。

レコメンド・システムモデルの作成にはユーザからのフィードバックなど`明示的なデータ(explicit)`を基に作成する場合と、クリック履歴や視聴履歴などの`暗黙的なデータ(implicit)`を基に作成する場合があります。

HeatWave AutoMLではどちらの場合もレコメンデーションモデルを作成することができます。

*暗黙的なデータを基にモデルを作成するには`8.0.34-u4-cloud`にアップグレードされている必要があります

## タスク1: データを準備する
1. CSVファイルをダウンロードして、オブジェクト・ストレージに格納します。
    [ml-100k_train.csv](./recommendation/ml-100k_train.csv)
    [ml-100k_test.csv](./recommendation/ml-100k_test.csv)   

2. MySQL ShellでMySQL HeatWaveに接続し、SQLモードでスキーマ、テーブルを作成します。
```sql
CREATE DATABASE mlcorpus;

USE mlcorpus;

SET GLOBAL local_infile=ON;
SET GLOBAL max_allowed_packet=1073741824;

DROP TABLE IF EXISTS `ml-100k_train`;
CREATE TABLE `ml-100k_train` (user_id VARCHAR(10), item_id VARCHAR(10), rating FLOAT, id MEDIUMINT NOT NULL AUTO_INCREMENT, PRIMARY KEY (id));

DROP TABLE IF EXISTS `ml-100k_test`;
CREATE TABLE `ml-100k_test` LIKE `ml-100k_train`;
```

3. MySQL ShellのJavaScriptモードに変更し、テーブルインポートユーティリティを実行します。
```js
\js
util.importTable("ml-100k_train.csv",{table: "ml-100k_train", dialect: "csv-unix", skipRows:1})
util.importTable("ml-100k_test.csv",{table: "ml-100k_test", dialect: "csv-unix", skipRows:1})
```

3. MySQL ShellのSQLモードに変更し、モデル作成の準備とデータが格納されていることをします。
```sql
\sql
CREATE TABLE `ml-100k`LIKE `ml-100k_test`;
INSERT INTO `ml-100k`
Select * from `ml-100k_test` limit 5;
select * from `ml-100k_train` limit 10;
```

## タスク2: 明示的データからモデルを作成する(explicit)
基となるアンケートで収集したユーザーのフィードバック(1〜5)に基づいて定量化された情報からおすすめする映画を抽出します。
モデルはHeatWave AutoMLの各ルーチンを実行することで作成できます。

レコメンデーションモデルを作成する場合は、パラメタに`recommendation`を指定します。

まず、名前をつけて、入力データに対してml trainを呼び出す。
ml trainを呼び出し、入力データとしてテーブルの名前を与えます。
また、ここで予測したい特徴の列の名前を指定する必要があります。
また、推薦モデルを学習させたいので、タスクは推薦であることを指定する必要があります。
そして、ユーザーとアイテムのカラムIDも指定する必要がある。

1. モデル名を指定してML_TRAINルーチンを実行します。この時使用するカラム名とモデル種類を指定します。

  - テーブル名: mlcorpus.ml-100k_train

  - カラム名: rating

  - オプション:
    - タスク: recommendation
    - ユーザーIDカラム名: user_id
    - アイテムIDカラム名: item_id

  - モデルハンドル: @model_explicit

```sql
set @model_explicit = "model_explicit";

#`ml-100k_train`テーブルを基にモデル`@model_explicit`を作成
CALL sys.ML_TRAIN('mlcorpus.`ml-100k_train`', 'rating', JSON_OBJECT('task', 'recommendation', 'users', 'user_id', 'items', 'item_id'), @model_explicit);
```

2. 作成したモデルをロードしてします。
```sql
#作成したモデル`@model_explicit`をロード
CALL sys.ML_MODEL_LOAD(@model_explicit, NULL);
```

3. 作成したモデルを使用してレコメンドを生成します。
  - 対象テーブル名: mlcorpus.ml-100k
  - モデル名: @model_explicit
  - 出力テーブル名: mlcorpus.rating_predictions

```sql
#作成したモデルを基にレコメンドを生成し、出力結果を5件確認
CALL sys.ML_PREDICT_TABLE('mlcorpus.`ml-100k`', @model_explicit, 'mlcorpus.`rating_predictions`', NULL);
select * from rating_predictions limit 5;
```

4. レコメンド・システムのオプションを指定するとより詳細なレコメンドを生成することができます。次にユーザーが好むアイテム上位3件のレコメンドを生成してみます。
  - 対象テーブル名: mlcorpus.ml-100k
  - モデル名: @model_explicit
  - 出力テーブル名: mlcorpus.item_recommendations
  - レコメンドオプション: "items", "topk", 3

```sql
#ユーザーに対するtopKレコメンドを生成し、出力結果を5件確認
CALL sys.ML_PREDICT_TABLE('mlcorpus.`ml-100k`', @model_explicit, 'mlcorpus.`item_recommendations`',  JSON_OBJECT("recommend", "items", "topk", 3));
select * from item_recommendations limit 5;
```

5. 次にアイテムを好むユーザー上位3件のレコメンドを生成してみます。
  - 対象テーブル名: mlcorpus.ml-100k
  - モデル名: @model_explicit
  - 出力テーブル名: mlcorpus.item_recommendations
  - レコメンドオプション: "users", "topk", 3

```sql
#アイテムに対するtopKレコメンドを生成し、出力結果を5件確認
CALL sys.ML_PREDICT_TABLE('mlcorpus.`ml-100k`', @model_explicit, 'mlcorpus.`user_recommendations`',  JSON_OBJECT("recommend", "users", "topk", 3));
select * from user_recommendations limit 5;
```

このモデルを使うには、ml model loadを使ってHeatWaveクラスタにロードする必要がある。
予測は出力テーブルに保存されます。
テーブルの出力を見てみると、各ユーザのアイテムのペアに対して、モデルによって出力されたレーティングがあることがわかります。
しかし、我々はレコメンダー・システムを使用しているので、我々がしたいことは、与えられたユーザーにアイテムを推薦することです。
ですから、同じ予測テーブルでそれを行うことができます。
しかし、アイテムを推薦するオプションを指定する必要があります。
アイテムは3つ欲しい。
結果テーブルを見てみましょう。
各ユーザーについて、モデルによって推薦された上位3つのアイテムと、それに対応するレーティングがあります。

映画についても同じことができ、各映画について、モデルがその映画を好きだと思う上位3人のユーザーを推薦することができる。
そして、各項目について上位3人のユーザーとそれに対応するレーティングを持つ同じ表が得られる。
このような明示的なフィードバックデータの問題点は、非常に稀であるということです。

## タスク3: 暗黙的データからモデルを作成する(implicit)
***この手順を実行するには8.0.34-u4-cloudにアップグレードされている必要があります***

ユーザーから直接フィードバックを得るのは容易ではない。
一方、ユーザーの行動はより簡単にモニターできる。
どのリンクをクリックしたのか、どのビデオを見たのか、特定のページにどれだけの時間を費やしたのかを保存することができる。
これは暗黙のフィードバックと呼ばれるもので、より豊富である。
これはユーザーとサービスとのインタラクションから間接的に収集され、ユーザーの好みの代理として機能する。
そのため、このタイプの暗黙のフィードバックを使ってレコメンダーシステムを構築することができ、クリックやインタラクションごとにリアルタイムでレコメンデーションを調整することができる。
ムービーレンズのデータセットは明示的フィードバックです。
しかし、レーティングを2値化することで暗黙的フィードバックに変換することができ、直接次のように変換することができる。
インタラクションは、ユーザーが映画とインタラクションしたことを表します。

評価ではなく、暗黙のフィードバックによってデータがどのようになるかを見てみましょう。
相互作用があります。
したがって、このようなデータでモデルを構築するために、暗黙のフィードバックでモデルを構築することもできます。
必要なことは、新しいテーブル名を指定し、ML trainの呼び出しで指定するだけです。
また、feedbackをimplicitと指定することで、入力データがimplicit feedbackのカテゴリであることを指定する必要があります。
これにより、暗黙的フィードバックモデルをトレーニングすることができ、トレーニングが終了したら、モデルをHeatwaveクラスタにロードすることができます。
以前と同じように。
そして、この新しい暗黙的フィードバックモデルを使って予測や推薦を行うことができる。
出力表を見ると、前のものとよく似ている。
しかし、各ユーザーに上位3つのアイテムを推薦するために、相互作用の列を使用しています。


