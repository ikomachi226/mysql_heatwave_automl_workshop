# MySQL HeatWaveインスタンスを作成する
ここでは、コンパートメント、仮想クラウドネットワークを作成してDBシステムを作成します。

**前提条件**
- MySQL Shellの利用

## タスク1: コンパートメントを作成する
1. OCIメニューから[アイデンティティとセキュリティ]-[コンパートメント]を選択します。
2. [コンパートメント]画面で[コンパートメントの作成]をクリックします。
3. [コンパートメントの作成]ダイアログで[名前][説明]を入力します。

  - 名前
  ```
  automl
  ```
  - 説明
  ```
  Compartment for AutoML with MySQL Database workshop
  ```
4. 親コンパートメントが自動的に表示されたら[コンパートメントを作成]をクリックします。
![compartment-create](./image/compartment-create.png)

## タスク2: 仮想ネットワーク(VCN)を作成する
1. OCIメニューから[ネットワーキング]-[仮想クラウド・ネットワーク]を選択します。
![homepage](./image/homepage.png)

![home-menu-networking-vcn](./image/home-menu-networking-vcn.png)

2. [インターネット接続性を持つVCNの作成]を選択し[VCNウィザードの起動]をクリックします。
![vcn-wizard-start](./image/vcn-wizard-start.png)

3. 基本情報を入力し、[次]をクリックします。

  - VCN名
  ```
  HEATWAVE-VCN
  ```
  - コンパートメント
  ```
  automlを選択
  ```
![vcn-wizard-compartment](./image/vcn-wizard-compartment.png)

4. [確認および作成]画面で入力情報に誤りがないことを確認したら[作成]をクリックします。
![vcn-wizard-create](./image/vcn-wizard-create.png)

5. VCNが作成されたら[VCNの表示]をクリックすると、作成したVCNの情報が確認できます。
![vcn-wizard-view](./image/vcn-wizard-view.png)

## タスク3: MySQL HeatWaveへの接続を行うためにセキュリティ・リストを設定する

1. タスク2で作成したVCNの画面で[サブネット]-[プライベート・サブネット-HEATWAVE-VCN]をクリックします。
![vcn-details](./image/vcn-details.png)

2. [セキュリティ・リスト]-[プライベート・サブネット-HEATWAVE-VCNの **セキュリティ・リスト** ]をクリックします。
![vcn-security-list](./image/vcn-security-list.png)

3. [イングレス・ルール]-[イングレス・ルールの追加]をクリックします。
![vcn-mysql-ingress](./image/vcn-mysql-ingress.png)

4. 以下の情報を指定し、[イングレス・ルールの追加]をクリックします。
  - ソースCIDR
  ```
  0.0.0.0/0
  ```
  - 宛先ポート範囲
  ```
  3306,33060
  ```
  - 説明
  ```
  MySQL Port Access
  ```
![vcn-mysql-add-ingress](./image/vcn-mysql-add-ingress.png)

5. [プライベート・サブネット-heatwave-vcnのセキュリティリスト]画面の「イングレス・ルール」一覧に追加したイングレス・ルールが表示されます。
![vcn-mysql-ingress-completed](./image/vcn-mysql-ingress-completed.png)

## タスク4: HTTP接続を行うためにセキュリティ・リストを設定する
1. OCIメニューから[ネットワーキング]-[仮想クラウド・ネットワーク]を選択します。
2. [HEATWAVE-VCN]をクリックして詳細画面を開きます。
4. [Default Security List for HEATWAVE-VCN]をクリックします。
5. [イングレス・ルール］-［イングレス・ルールの追加］をクリックします。
  - ソースCIDR
  ```
  0.0.0.0/0
  ```
  - 宛先ポート範囲
  ```
  80,443
  ```
  - 説明
  ```
  Allow HTTP connections
  ```
![vcn-ttp-add-ingress](./image/vcn-ttp-add-ingress.png)

6. [Default Security List for HeatWave-VCN]の[イングレス・ルール]一覧に追加したイングレス・ルールが表示されます。
![vcn-ttp-ingress-completed](./image/vcn-ttp-ingress-completed.png)

## タスク5: MySQL HeatWaveインスタンスを起動する
1. OCIメニューから[データベース]-[MySQL HeatWave]-[DBシステム]を選択します。

![home-menu-database-mysql](./image/home-menu-database-mysql.png)

2. [DBシステムの作成]ボタンをクリックします。

![mysql-menu](./image/mysql-menu.png)

3. DBシステムオプションで以下を選択します。

    **- 開発またはテスト**

    **- スタンドアロン**
  
    **- MySQL HeatWave**

![mysql-create-option-develpment](./image/mysql-create-option-develpment.png)

![mysql-heatwave-system-selection](./image/mysql-heatwave-system-selection.png)

5. [DBシステム情報の指定]で以下の情報を入力します。

  - コンパートメントに[automl]を選択します。

  - 名前
  ```
  HEATWAVE-DB
  ```
  - 説明
  ```
  MySQL HeatWave Database Instance
  ```
  - 管理者資格証明の作成(任意の値を設定してください)

    ***DB管理者になりますので失念するとDBにログインできません***

      **- ユーザ名**
  
      **- パスワード**
  
      **- パスワードの確認**

![mysql-create-admin](./image/mysql-create-admin.png)
  
6. [ネットワーキングの構成]で以下の値が設定されていることを確認します。

  - 仮想クラウドネットワーク: **HEATWAVE-VCN**

  - サブネット: **プライベート・サブネット-HEATWAVE-VCN(リージョナル)**

![mysql-create-network-ad](./image/mysql-create-network-ad.png)

7. [配置の構成]で[AD-1]が選択され、[フォルト・ドメインの選択]はOFFになっていることを確認します。

8. [ハードウェアの構成]で以下の値を設定します。

  - シェイプの選択: **MySQL.HeatWave.VM.Standard**

  - データ・ストレージ・サイズ(GB): **1024(デフォルト)**

![mysql-create-db-hardware](./image/mysql-create-db-hardware.png)

9. [バックアップの設定]で[自動バックアップ]が選択されていることを確認します。

![mysql-create-backup](./image/mysql-create-backup.png)

10. [拡張オプションの設定]をクリックします。
11. [ネットワーキング]タブでホスト名にDBシステム名と同じ名前を入力します。
    - ホスト名
    ```
    HEATWAVE-DB
    ```
![mysql-create-advanced](./image/mysql-create-advanced.png)

12. [DBシステムの作成]画面の入力内容を確認し、[作成]ボタンをクリックします。

![mysql-create](./image/mysql-create.png)

13. 作成したDBシステムの詳細画面に遷移します。この時のステータスは[作成中]になっています。

![mysql-create-in-progress](./image/mysql-create-in-progress.png)

14. プロビジョニングが終了すると、ステータスが[アクティブ]になります。

![mysql-detail-active](./image/mysql-detail-active.png)

15. [HEATWAVE-DB]詳細画面-[リソース]メニュー-[エンドポイント]を選択し、IPアドレスが設定されていることを確認します。
