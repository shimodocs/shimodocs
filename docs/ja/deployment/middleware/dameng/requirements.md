# Dameng V8 要件

[← ShimoDocs Suite デプロイメント文書](../../README.md)

この文書は、実装、維持、または統合を行う担当者を導くことを目的としています Dameng データベースを初めて使用して、完了するために Dameng DM8 データベース初期化, MySQL 互換モードの設定、サービスの起動、および接続の確認を段階的に行う。

この文書の例では、次の計画を使用しています：

| 項目 | 例の値 |
| --- | --- |
| データベースインストールディレクトリ | `/opt/dmdbms` |
| データベース保存ディレクトリ | `/dmdata/data` |
| DATABASE_NAME | `DMTEST` |
| インスタンス名 | `DBSERVER` |
| データベースポート | `5236` |
| 管理者アカウント | `SYSDBA` |
| 管理者 PASSWORD | `<SYSDBA_PASSWORD>` |

> 注意： `<SYSDBA_PASSWORD>` および `<SYSAUDITOR_PASSWORD>` 文書内のはプレースホルダーです。実際の操作時には、実際のパスワードに置き換えて、複雑さの要件を満たしてください。 PASSWORD 複雑さの要件。

## 1. 操作前確認

### 1. 以下を確認してください Dameng はすでにインストールされています

サーバーで実行:

```bash
ls /opt/dmdbms/bin
```

のようなファイルが見える場合 `dminit`, `dmserver`, `disql`、それは次を示しています Dameng ソフトウェアはすでにインストールされています。

バージョンも確認できます:

```bash
/opt/dmdbms/bin/dmserver
```

出力にこのような内容が表示されることがあります:

```text
dmserver V8
version: 03134284194-20240920-243574-20108
```

### 2. システムユーザーを確認する

Dameng 通常は次のユーザーでデータベースを実行します `dmdba` ユーザーが存在するか確認してください:

```bash
id dmdba
```

存在しない場合、次のユーザーで作成できます: `root` ユーザー:

```bash
groupadd dinstall
useradd -g dinstall -m -d /home/dmdba -s /bin/bash dmdba
passwd dmdba
```

### 3. データディレクトリの準備

次を使用して実行します `root` ユーザー:

```bash
mkdir -p /dmdata/data
chown -R dmdba:dinstall /dmdata
chmod -R 775 /dmdata
```

この手順の目的は、データベースファイルを保存するディレクトリを作成し、ユーザーに権限を付与することです。 `dmdba` ユーザー。

## 2. データベースの初期化

次のユーザーに切り替えます `dmdba` ユーザー:

```bash
su - dmdba
```

初期化コマンドを実行します: 

```bash
/opt/dmdbms/bin/dminit \
  PATH=/dmdata/data \
  PAGE_SIZE=32 \
  EXTENT_SIZE=32 \
  CASE_SENSITIVE=0 \
  UNICODE_FLAG=1 \
  DB_NAME=DMTEST \
  INSTANCE_NAME=DBSERVER \
  PORT_NUM=5236 \
  SYSDBA_PWD=<SYSDBA_PASSWORD> \
  SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

初期化が成功すると、次のような出力が表示されます: 

```text
create dm database success
```
初期化が成功した後、データベースディレクトリが生成されます: 

```text
/dmdata/data/DMTEST
```

その中で重要な設定ファイルは次のとおりです:

```text
/dmdata/data/DMTEST/dm.ini
```

## 3. 修正 MySQL 互換性の設定

設定ファイルを編集します `root` または `dmdba` ユーザー:

```bash
vi /dmdata/data/DMTEST/dm.ini
```

次の2つの設定を探して修正します: 

```ini
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

ファイルに既にこれらの2つの設定がある場合は、既存の行を直接修正できます。

同じ名前の設定をファイルの末尾に追加しないでください。そうしないと、重複する設定が発生し、実際の有効値が期待値と異なる場合があります。

修正が完了したら、以下のコマンドで確認できます:

```bash
grep -Ein 'COMPATIBLE_MODE|ORDER_BY_NULLS_FLAG' /dmdata/data/DMTEST/dm.ini
```

期待される表示:

```text
COMPATIBLE_MODE = 4
ORDER_BY_NULLS_FLAG = 0
```

## 4. データベースサービスの登録

に切り替えます `root` ユーザー:

```bash
exit
```

データベースサービスを登録: 

```bash
/opt/dmdbms/script/root/dm_service_installer.sh \
  -t dmserver \
  -p DBSERVER \
  -dm_ini /dmdata/data/DMTEST/dm.ini
```

登録が成功した後、サービス名は通常次の通りです:

```text
DmServiceDBSERVER.service
```

起動時に開始するように設定し、サービスを開始します: 

```bash
systemctl daemon-reload
systemctl enable DmServiceDBSERVER.service
systemctl start DmServiceDBSERVER.service
```

サービスの状態を確認: 

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

次の表示が見えた場合: 

```text
Active: active (running)
```

データベースサービスが起動したことを示します。 

## 5. データベースの利用可能性の確認 

### 1. ポートの確認 

以下を実行: 

```bash
ss -lntp | grep ':5236'
```

次の表示が見えた場合 `dmserver` listening on `5236`データベースのポートが正常であることを示します。

### 2. ローカルログインテスト

次のユーザーに切り替えます `dmdba` ユーザー:

```bash
su - dmdba
```

データベースにログイン: 

```bash
/opt/dmdbms/bin/disql SYSDBA/<SYSDBA_PASSWORD>@127.0.0.1:5236
```

ログインが成功した後に実行:

```sql
select 1 as OK;
```

返却があった場合: 

```text
OK
-----------
1
```

データベース接続が正常であることを示します。 

### 3. が MySQL 互換モードか確認

で実行 `disql`: 

```sql
select para_name, para_value
from v$dm_ini
where para_name in (
  'COMPATIBLE_MODE',
  'ORDER_BY_NULLS_FLAG',
  'INSTANCE_NAME',
  'PORT_NUM'
);
```

期待される結果: 

```text
INSTANCE_NAME        DBSERVER
PORT_NUM             5236
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

そのうち:

```text
COMPATIBLE_MODE = 4
```

現在のデータベース実行状態で互換モードが有効になっていることを示します。 MySQL 互換モード。 



## 付録1、設定項目の詳細説明 

### 1. `PATH` 

例: 

```text
PATH=/dmdata/data
```

意味: 

`PATH` はデータベースファイルのルートディレクトリです。初期化時に Dameng このディレクトリの下にデータベースディレクトリを作成します。

もし `DB_NAME=DMTEST`、最終ディレクトリは通常次の通りです: 

```text
/dmdata/data/DMTEST
```

このディレクトリはデータファイル、ログファイル、制御ファイル、および `dm.ini` 設定ファイルを格納します。

推奨事項：

- 本番環境では、十分な容量と安定した性能を持つデータディスクに配置することを推奨します。
- 一時ディレクトリには配置しないことを推奨します。例えば `/tmp`.
- 初期化後にディレクトリを安易に移動しないでください。

### 2. `DB_NAME`

例：

```text
DB_NAME=DMTEST
```

意味: 

`DB_NAME` はその名前です。 DATABASE_NAME. これはデータベースのディレクトリ名、ログファイル名などに影響します。 

例えば、次の場合 `DB_NAME=DMTEST`、通常は次のように生成されます: 

```text
/dmdata/data/DMTEST
/dmdata/data/DMTEST/DMTEST01.log
/dmdata/data/DMTEST/DMTEST02.log
```

推奨事項：

- プロジェクト全体で単一の明確なものを使用してください DATABASE_NAME 。
- 初期化後に変更することは推奨されません。

### 3. `INSTANCE_NAME`

例：

```text
INSTANCE_NAME=DBSERVER
```

意味: 

`INSTANCE_NAME` はデータベースインスタンス名です。サービス登録時にサービス名を生成する際に通常使用されます。

例えば: 

```text
INSTANCE_NAME=DBSERVER
```

登録後のサービス名は通常以下の通りです：

```text
DmServiceDBSERVER.service
```

推奨： 

- 単一インスタンスの単一マシンの場合、次を使用できます `DBSERVER`.
- 1台のマシンに複数のインスタンスを展開する場合、各インスタンス名は異なる必要があります。

### 4. `PORT_NUM`

例: 

```text
PORT_NUM=5236
```

意味: 

`PORT_NUM` はデータベースのリスニングポートです。アプリケーションはデータベースに接続する際にこのポートにアクセスする必要があります。 

プログラムページに入力されたポートは、これと一致している必要があります： 

```text
HOST:172.17.9.84
PORT:5236
```

推奨： 

- のデフォルトポートは Dameng 通常以下の通りです `5236`. 
- 複数の Dameng 同じマシン上のインスタンスがある場合、ポートは重複できません。 
- ポートを変更した後、データベースサービスを再起動する必要があります。 

### 5. `PAGE_SIZE` 

例: 

```text
PAGE_SIZE=32
```

意味: 

`PAGE_SIZE` はデータベースのページサイズで、KB単位です。データベースがデータを読み書きする際、ページ単位でデータを整理します。 

`PAGE_SIZE=32` は各データページが32KBであることを意味します。 

影響： 

- データの保存、インデックス作成、IO動作に影響します。 
- 初期化後の変更は推奨されません。 
- 調整が必要な場合、通常はデータベースを再初期化し、データを移行する必要があります。 

推奨： 

- もし…がある場合 SOP シナリオに応じて…に従って設定してください。 SOP. 
- 特別な要件がない場合は、任意に変更しないでください。 

### 6. `EXTENT_SIZE` 

例: 

```text
EXTENT_SIZE=32
```

意味: 

`EXTENT_SIZE` はクラスタサイズで、ページ単位で測定されます。データベースが一度に使用する空間割り当ての基本単位と理解できます。

もし： 

```text
PAGE_SIZE=32
EXTENT_SIZE=32
```

その場合、クラスタは約： 

```text
32KB * 32 = 1024KB
```

およそ1MBです。 

影響： 

- データファイルの空間割り当ての粒度に影響します。 
- 初期化後の変更は推奨されません。 

### 7. `CASE_SENSITIVE` 

例: 

```text
CASE_SENSITIVE=0
```

意味: 

`CASE_SENSITIVE` はデータベースオブジェクト名が大文字小文字を区別するかどうかを示します。

一般的な値： 

```text
0:CASE_INSENSITIVE
1:CASE_SENSITIVE
```

例えば、大文字小文字を区別しない場合、以下の2つのテーブル名は同じオブジェクトと見なすことができます：

```text
user
USER
```

影響： 

- テーブル名、フィールド名、およびオブジェクト名の認識に影響します。 
- 以下のために MySQL 移行または MySQL-互換のシナリオでは、通常…として設定することが推奨されます。 `0`. 
- 初期化後の変更は推奨されません。 

### 8. `UNICODE_FLAG` 

例: 

```text
UNICODE_FLAG=1
```

意味: 

`UNICODE_FLAG` は文字セットの設定です。

一般的な値： 

```text
0:GB18030
1:UTF-8
2:EUC-KR
```

`UNICODE_FLAG=1` データベースが…-8文字セットを使用することを示します。 UTF-8文字セットです。

推奨：

- 次を計算基準として使用することを推奨します UTF新しいシステムには-8を使用してください。
- 中国語、英語、多言語文字との互換性が向上します。
- 初期化後の変更は推奨されません。

### 9. `SYSDBA_PWD`

例：

```text
SYSDBA_PWD=<SYSDBA_PASSWORD>
```

意味: 

`SYSDBA_PWD` は…です PASSWORD のために `SYSDBA` 管理者アカウント。

`SYSDBA` データベースのスーパー管理者に似ており、高レベルの権限を持っています。

推奨： 

- 強力な…を使用してください。 PASSWORD.
- …のような簡単なパスワードは使用しないでください。 `SYSDBA`, `123456`, `password`.
- PASSWORD 長さは少なくとも8文字、文字と数字を含むことが推奨されます。
- 実際の…を書かないでください。 PASSWORD 外部文書に…を記載しないでください。

### 10. `SYSAUDITOR_PWD`

例: 

```text
SYSAUDITOR_PWD=<SYSAUDITOR_PASSWORD>
```

意味: 

`SYSAUDITOR_PWD` は…です PASSWORD の `SYSAUDITOR` 監査管理者アカウント。

`SYSAUDITOR` 主に監査関連の管理機能に使用されます。

推奨：

- 使用する PASSWORD と異なる `SYSDBA`.
- 強力な…を使用してください。 PASSWORD 複雑さの要件を満たすもの。

### 11. `COMPATIBLE_MODE`

例：

```text
COMPATIBLE_MODE = 4
```

意味: 

`COMPATIBLE_MODE` はデータベースの互換モード設定であり、 Dameng データベースが構文、関数、および特定の動作に関してどのタイプのデータベースに合わせるかを制御するために使用されます。 SQL 

一般的な値の意味: 

```text
0:DEFAULT_MODE
1:SQL92
2:Oracle
3:MS SQL Server
4:MySQL
5:DM6
6:Teradata
7:PostgreSQL
8:DB2
```

このテキストは次のように設定されています： 

```text
COMPATIBLE_MODE = 4
```

有効にすることを示します MySQL 互換モード。 

機能： 

- 互換性を向上させます MySQL SQL の構文で Dameng. 
- からの移行時の変換コストを削減します MySQL またはに適応する際に Dameng. 

注意： 

- この設定は、が Dameng プロトコルをサポートすることを意味するものではありません MySQL 。 
- プログラムはまだ使用する必要があります Dameng ドライバーは内部的に設定されています；ページにドライバー設定オプションがない場合、ユーザーは別途それを入力する必要はありません。 
- 変更後、データベースサービスの再起動が必要です。 
- 最終的に効果があるかどうかは、次に基づくべきです `v$dm_ini` クエリ結果。 

### 12. `ORDER_BY_NULLS_FLAG` 

例: 

```text
ORDER_BY_NULLS_FLAG = 0
```

意味: 

`ORDER_BY_NULLS_FLAG` は、次のときに値が表示されるかどうかを制御するために使用されます NULL 並べ替え時に値が先頭または末尾に表示されるか `ORDER BY`. 

重要な理由: 

データベースによって、NULLの並べ替えに関するデフォルトの動作が異なる場合があります。アプリケーションを移行する場合 MySQL から Damengまで、NULL の位置に応じてソート結果が変わる場合、このパラメータはクエリ結果の順序に影響を与えることがあります。 

この記事は次のように設定されています: 

```text
ORDER_BY_NULLS_FLAG = 0
```

目的は、ソートの動作をより近づけることです MySQL 使用習慣。

注意:

- 変更後、データベースサービスの再起動が必要です。
- もしビジネスが SQL すでに明示的に指定している場合、 `NULLS FIRST` または `NULLS LAST`、指定された動作は SQL で優先されるべきです。

## 付録2、よくある質問

### 1. なぜ接続できないのですか MySQL クライアント MySQL 互換モード？

なぜなら MySQL 互換モードは…にのみ影響する SQL の構文といくつかのデータベースの動作は変更するが、 Damengのネットワークプロトコルは変更しない。

アプリケーションやツールが接続する場合、 Dameng、 Dameng ドライバーは引き続き使用する必要があります:

```text
dm.jdbc.driver.DmDriver
jdbc:dm://<host>:5236
```

は使用できません: 

```text
com.mysql.cj.jdbc.Driver
jdbc:mysql://<host>:5236
```

### 2. 設定が本当に反映されたかどうかを確認する方法は？

ただ見ているだけではダメ `dm.ini` ファイル; 実行時の状態を確認するにはデータベースにログインすることを推奨します：

```sql
select para_name, para_value
from v$dm_ini
where para_name in ('COMPATIBLE_MODE', 'ORDER_BY_NULLS_FLAG');
```

実行状態は、以下の結果が確認できた場合にのみ有効です： 

```text
COMPATIBLE_MODE      4
ORDER_BY_NULLS_FLAG  0
```

### 3. 修正後に有効にならないのはなぜですか `dm.ini`?

一般的な理由:

- データベースサービスは変更後に再起動されませんでした。
- ファイルに重複する設定項目があります。
- 変更されたファイルは～ではありません `dm.ini` 現在インスタンスによって使用されています。

現在のインスタンスがどの設定ファイルを使用しているかは、サービス起動コマンドで確認できます:

```bash
systemctl status DmServiceDBSERVER.service --no-pager
```

出力には通常、次のようなものが表示されます:

```text
dmserver path=/dmdata/data/DMTEST/dm.ini -noconsole
```

### 4. もし私が〜したらどうすればいいですか PASSWORD 複雑さエラーが発生しますか？

は、 PASSWORD があまりにも簡単であることを示しています。より複雑なものに変更してください。 PASSWORDを含まない配布パッケージを選択してください。例えば:

```text
AT_LEAST 8 POSITION
CONTAINS_LETTERS_AND_NUMBERS
AVOID_USING_THE_ACCOUNT_NAME_ITSELF
```

### 5. これらのパラメータは後で変更できますか？ 

いいえ。 

初期化パラメータは通常、後で変更することは推奨されません。例えば： 
- 'PAGE_SIZE'
- 'EXTENT_SIZE'
- 'CASE_SENSITIVE'
- 'UNICODE_FLAG'
- 'DB_NAME'
- 'INSTANCE_NAME'

これらのパラメータが誤って設定されている場合、通常はデータベースを再初期化し、データを再度移行することが推奨されます。 

'dm.ini' パラメータは後で調整することができます。例えば： 

- 'COMPATIBLE_MODE'
- 'ORDER_BY_NULLS_FLAG'
- 'PORT_NUM'

しかし、変更後、データベースサービスは通常再起動する必要があります。 

## 付録3：最終チェックリスト 


- '/dmdata/data' データディレクトリが作成されました。 
- データディレクトリのホストは 'dmdba:dinstall' です。 
- データベースは正常に初期化されました。 
- '/dmdata/data/DMTEST/dm.ini' が存在します。 
- `COMPATIBLE_MODE = 4`. 
- `ORDER_BY_NULLS_FLAG = 0`. 
- データベースサービス `DmServiceDBSERVER.service` は `active`. 
- ポート `5236` がリッスンされています。 
- `SYSDBA` はデータベースにログインできます。 
- 中に `v$dm_ini`の実行時値 `COMPATIBLE_MODE` は `4`.
