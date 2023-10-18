# MySQL HeatWaveへの接続

## はじめに
MySQL HeatWaveは、お客様のデータを潜在的な攻撃や脆弱性から保護するために、プライベートネットワークからのみアクセス可能なサービスです。

接続するためには踏み台サーバーやBastionサービスなどの利用が必要になります。

このワークショップでは、コンピュートインスタンス(VM)を使用して、ブラウザ(Cloud Shell)からMySQL HeatWaveに接続します。

そのためにこのセクションでは、SSHキー、コンピュートインスタンスを起動して、MySQL Shell(クライアント)をインストールします。

## タスク1: Cloud ShellからSSHキーを作成する

Cloud ShellはBashシェルを実行する仮想マシンで、OCIコンソールからアクセスします。

1. Cloud Sheを起動し、Bastion セッションで使用する SSH キーを生成します。

Oracle Cloud シェルを起動するには、Cloud コンソールに移動し、ページの右上にある[開発者ツール]アイコン-[Cloud Shell]をクリックします。

**TeraTermやPuTTY等のSSH接続ツールも利用できます。接続方法については[ドキュメント](https://docs.oracle.com/ja-jp/iaas/Content/GSG/Tasks/testingconnection.htm)を参照してください*

![cloud-shell](./image/cloud-shell.png)

![cloud-shell-setup](./image/cloud-shell-setup.png)

![cloud-shell-open](./image/cloud-shell-open.png)

これでブラウザでCloud Shellが起動されますが、初回は生成に時間がかかる場合があります。

***Cloud Shell ウィンドウの右上隅にあるアイコンを使用して、Cloud Shell セッションを最小化、最大化、再起動、および終了することができます。***

2. Cloud Shellが起動したら、以下のコマンドでSSHキーを作成します。

  ```
  ssh-keygen -t rsa
  ```

  各質問に対してEnterキーを入力します。以下のようになります。

  ![ssh-key-show](./image/ssh-key-show.png)
  
3. SSHの公開鍵と秘密鍵は**~/.ssh/id_rsa.pub**に格納されます。

4. 作成された2つのファイルを確認します。
  ```
  cd .ssh
  ```
  ```
  ls
  ```

  ![shh-key-list](./image/shh-key-list.png)

  ***出力されるファイルには秘密鍵：id_rsaと公開鍵：id_rsa.pubの2つがあることに注意してください。***
  
  ***秘密鍵は安全に保管し、その内容を共有しないようにしてください。公開鍵は様々な操作に必要であり、特定のシステムにアップロードしたり、クラウドでの安全な通信を促進するために利用してください。***

## タスク2: コンピュートインスタンスを作成する

lab1で起動したMySQL HeatWaveインスタンスに接続するためにコンピュートインスタンスを作成します。

1. コンピュートインスタンスを作成する前にメモ帳等のエディタを起動し、以下の手順で公開SSHキーをエディタにコピーします。
  - Cloud Shellウィンドウを開きます。

    ![cloud-shell-open-large](./image/cloud-shell-open-large.png)

  - 以下のコマンドを実行します。
    ```
    cat ~/.ssh/id_rsa.pub
    ```

    ![ssh-key-display](./image/ssh-key-display.png)

  - 以下のように***id_rsa.pub***の内容をエディタにコピーします。

  ![notepad-rsa-key](./image/notepad-rsa-key.png)

2. Cloud Shellウィンドウを最小化します。

  ![ssh-key-display-minimize](./image/ssh-key-display-minimize.png)

3. OCIメニューから[コンピュート]-[インスタンス]を選択します。

  ![navigation-compute](./image/navigation-compute.png)  

4. **automl**コンパートメントが選択されていることを確認し、[インスタンスの作成]をクリックします。

  ![compute-menu-create-instance](./image/compute-menu-create-instance.png)
  
  - 名前
    ```
    HEATWAVE-Client
    ```
  - コンパートメントに作成: **automl**
  - 配置: **可用性ドメイン**
  - セキュリティ: **保護インスタンス:無効**

  ![compute-create-security](./image/compute-create-security.png)

  - イメージ: **Oracle Linux8**
  - Shape: **VM.Standard.E4**

  ![compute-create-image](./image/compute-create-image.png)

  - OCPU,メモリー量を以下のように変更します。
    
    ```
    OCPU: 2
    メモリー量: 32
    ```

    ![compute-create-change-shape](./image/compute-create-change-shape.png)
    
    ![compute-create-select-shape](./image/compute-create-select-shape.png)
   
  - プライマリVNIC情報
    - **既存の仮想クラウド・ネットワークを選択: HEATWAVE-VCN**
    - **既存のサブネットを選択: パブリック・サブネット-HEATWAVE-VCN**
    - **パブリックIPv4アドレス: パブリックIPv4アドレスの自動割当て**

    ![compute-create-networking](./image/compute-create-networking.png)
     
    ![compute-create-networking-select](./image/compute-create-networking-select.png)

  - SSHキーの追加: **公開キーの貼付け**
    - 手順1でエディタに貼り付けたSSHキーをコピー＆ペーストします。    

    ![compute-create-add-ssh-key](./image/compute-create-add-ssh-key.png)
    
  その他の設定はデフォルトのままにしておきます。 
  
5. [作成]ボタンをクリックします。

  ![compute-provisioning](./image/compute-provisioning.png)

6. コンピュートの詳細画面のステータスが[作成中]から[実行中]に変わると利用できるようになります。(これには数分かかります)

  ![compute-active](./image/compute-active.png)

## タスク3: SSHを使用してコンピュートインスタンスに接続します。
1. 以下をエディタにコピーしておきます。
   - タスク2で起動したコンピュートインスタンスのパブリックIPアドレス
   - MySQL HeatWaveインスタンスのエンドポイントIPアドレス

  ![notepad-rsa-key-compute-db](./image/notepad-rsa-key-compute-db.png)

2. Cloud Shellウィンドウから、以下のコマンドを実行してコンピュートインスタンスに接続します。
    ```
    ssh -i ~/.ssh/id_rsa opc@<コンピュートインスタンスのパブリックIPアドレス>
    ```
    - **Are you sure you want to continue connecting (yes/no)?**　と聞かれたら **yes**を入力します

    ![connect-first-signin](./image/connect-first-signin.png)

## タスク4: MySQL Shellをインストールし、MySQL HeatWaveに接続する
1. Cloud Shellウィンドウから、以下のコマンドを実行してMySQL Shellをインストールします。

    (RHEL8系のOSにディストリビューションされているMySQLモジュールを無効化してインストールします) 
   ```
   sudo yum install https://dev.mysql.com/get/mysql80-community-release-el8-4.noarch.rpm
   ```
   ```
   sudo yum module disable mysql
   ```
   ```
   sudo yum install mysql-shell
   ```

    ![mysql-install-shell](./image/mysql-install-shell.png)

3. Cloud Shellウィンドウから、以下のコマンドを実行してコンピュートインスタンス経由でMySQL HeatWaveに接続します。
   ```
   mysqlsh -uadmin -p -h <MySQL HeatWaveインスタンスのエンドポイントIPアドレス>
   ```

    ![mysql-endpoint-private-ip](./image/mysql-endpoint-private-ip.png)

    ![mysql-shell-first-connect](./image/mysql-shell-first-connect.png)

4. 接続できたらスキーマ一覧を確認します。
   ```
   \sql
   show databases;
   ```
  ![list-schemas-before](./image/list-schemas-before.png)

**補足資料**

[Cloud Shellについて](https://www.oracle.com/jp/devops/cloud-shell/?source=:so:ch:or:awr::::Sc)


***[次のセクションへ](../lab4/readme.md)***
