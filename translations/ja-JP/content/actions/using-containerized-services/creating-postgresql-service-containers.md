---
title: PostgreSQLサービスコンテナの作成
shortTitle: PostgreSQL サービス コンテナ
intro: ワークフローで利用するPostgreSQLサービスコンテナを作成できます。 このガイドでは、コンテナで実行されるジョブか、ランナーマシン上で直接実行されるジョブのためのPostgreSQLサービスの作成例を紹介します。
redirect_from:
  - /actions/automating-your-workflow-with-github-actions/creating-postgresql-service-containers
  - /actions/configuring-and-managing-workflows/creating-postgresql-service-containers
  - /actions/guides/creating-postgresql-service-containers
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
type: tutorial
topics:
  - Containers
  - Docker
---

{% data reusables.actions.enterprise-beta %}
{% data reusables.actions.enterprise-github-hosted-runners %}

## はじめに

このガイドでは、Docker Hubの`postgres`イメージを使ってサービスコンテナを設定するワークフローの例を紹介します。 ワークフローの実行スクリプトは、PostgreSQL サービスに接続し、テーブルを作成してから、データを入力します。 ワークフローが PostgreSQL テーブルを作成してデータを入力することをテストするために、スクリプトはテーブルからコンソールにデータを出力します。

{% data reusables.github-actions.docker-container-os-support %}

## 必要な環境

{% data reusables.github-actions.service-container-prereqs %}

YAML、{% data variables.product.prodname_actions %}の構文、PosgreSQLの基本な理解があれば役立つかも知れません。 詳しい情報については、以下を参照してください。

- 「[{% data variables.product.prodname_actions %} を学ぶ](/actions/learn-github-actions)」
- PostgreSQLのドキュメンテーション中の[PostgreSQLチュートリアル](https://www.postgresqltutorial.com/)

## コンテナ内でのジョブの実行

{% data reusables.github-actions.container-jobs-intro %}

{% data reusables.github-actions.copy-workflow-file %}

{% raw %}
```yaml{:copy}
name: PostgreSQL service example
on: push

jobs:
  # コンテナジョブのラベル
  container-job:
    # コンテナは Linux ベースのオペレーティングシステムで実行しなければならない
    runs-on: ubuntu-latest
    # `container-job` が実行される Docker Hub イメージ
    container: node:10.18-jessie

    # `container-job` で実行するサービスコンテナ
    services:
      # サービスコンテナへのアクセスに使用されるラベル
      postgres:
        # Docker Hub のイメージ
        image: postgres
        # postgres のパスワードを入力する
        env:
          POSTGRES_PASSWORD: postgres
        # postgres が起動するまで待機するようにヘルスチェックを設定する
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      # CI テストを実行する前に、リポジトリにコードのコピーをダウンロードする
      - name: Check out repository code
        uses: actions/checkout@v2

      # `package.json` ファイル内のすべての依存関係のクリーンインストールを実行する
      # 詳しい情報については https://docs.npmjs.com/cli/ci.html を参照
      - name: Install dependencies
        run: npm ci

      - name: Connect to PostgreSQL
        # PostgreSQLテーブルを作成し、テーブルにデータを入力してから
        # データを取得するスクリプトを実行する。
        run: node client.js
        # `client.js` スクリプトが新しいPostgreSQLクライアントの作成に使う環境変数。
        env:
          # PostgreSQLサービスコンテナとの通信に使われるホスト名
          POSTGRES_HOST: postgres
          # デフォルトのPostgreSQLポート
          POSTGRES_PORT: 5432
```
{% endraw %}

### ランナージョブの設定

{% data reusables.github-actions.service-container-host %}

{% data reusables.github-actions.postgres-label-description %}

```yaml{:copy}
jobs:
  # コンテナジョブのラベル
  container-job:
    # コンテナはLinuxベースのオペレーティングシステム内で実行しなければならない
    runs-on: ubuntu-latest
    # `container-job`が実行されるDocker Hubのイメージ
    container: node:10.18-jessie

    # `container-job`と実行されるサービスコンテナ
    services:
      # サービスコンテナへのアクセスに使われるラベル
      postgres:
        # Docker Hubのイメージ
        image: postgres
        # postgresのパスワードを提供
        env:
          POSTGRES_PASSWORD: postgres
        # postgresが起動するまで待つヘルスチェックの設定
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
```

### ステップの設定

{% data reusables.github-actions.service-template-steps %}

```yaml{:copy}
steps:
  # CI テストを実行する前に、リポジトリにコードのコピーをダウンロードする
  - name: Check out repository code
    uses: actions/checkout@v2

  # `package.json` ファイル内のすべての依存関係のクリーンインストールを実行する
  # 詳しい情報については https://docs.npmjs.com/cli/ci.html を参照する
  - name: Install dependencies
    run: npm ci

  - name: Connect to PostgreSQL
    # PostgreSQL テーブルを作成し、テーブルにデータを入力してから
    # データを取得するスクリプトを実行する。
    run: node client.js
    # 新しい PostgreSQL クライアントを作成するために
    # `client.js` スクリプトによって使用される環境変数。
    env:
      # PostgreSQLサービスコンテナとの通信に使われるホスト名
      POSTGRES_HOST: postgres
      # デフォルトのPostgreSQLポート
      POSTGRES_PORT: 5432
```

{% data reusables.github-actions.postgres-environment-variables %}

PostgreSQLサービスのホスト名は、ワークフロー中で設定されたラベルで、ここでは`postgres`です。 同じユーザー定義ブリッジネットワーク上のDockerコンテナは、デフォルトですべてのポートをオープンするので、サービスコンテナにはデフォルトのPostgreSQLのポートである5432でアクセスできます。

## ランナーマシン上で直接のジョブの実行

ランナーマシン上で直接ジョブを実行する場合、サービスコンテナ上のポートをDockerホスト上のポートにマップしなければなりません。 Dockerホストからサービスコンテナへは、`localhost`とDockerホストのポート番号を使ってアクセスできます。

{% data reusables.github-actions.copy-workflow-file %}

{% raw %}
```yaml{:copy}
name: PostgreSQL Service Example
on: push

jobs:
  # ランナージョブのラベル
  runner-job:
    # サービスコンテナまたはコンテナジョブを使用する場合は Linux 環境を使用する必要がある
    runs-on: ubuntu-latest

    # `runner-job` で実行されるサービスコンテナ
    services:
      # サービスコンテナへのアクセスに使用されるラベル
      postgres:
        # Docker Hub イメージ
        image: postgres
        # postgres のパスワードを入力する
        env:
          POSTGRES_PASSWORD: postgres
        # postgres が起動するまで待機するようにヘルスチェックを設定する
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          # サービスコンテナの tcp ポート 5432 をホストにマップする
          - 5432:5432

    steps:
      # CI テストを実行する前に、リポジトリにコードのコピーをダウンロードする
      - name: Check out repository code
        uses: actions/checkout@v2

      # `package.json` ファイル内のすべての依存関係のクリーンインストールを実行する
      # 詳しい情報については https://docs.npmjs.com/cli/ci.html を参照する
      - name: Install dependencies
        run: npm ci

      - name: Connect to PostgreSQL
        # PostgreSQLテーブルを作成し、テーブルにデータを入力してから
        # データを取得するスクリプトを実行する
        run: node client.js
        # `client.js` スクリプトが新しいPostgreSQLクライアントの
        # 作成に使う環境変数
        env:
          # PostgreSQLサービスコンテナとの通信に使われるホスト名
          POSTGRES_HOST: localhost
          # デフォルトのPostgreSQLポート
          POSTGRES_PORT: 5432
```
{% endraw %}

### ランナージョブの設定

{% data reusables.github-actions.service-container-host-runner %}

{% data reusables.github-actions.postgres-label-description %}

このワークフローはPostgreSQLサービスコンテナ上のポート5432をDockerホストにマップします。 `ports`キーワードに関する詳しい情報については「[サービスコンテナについて](/actions/automating-your-workflow-with-github-actions/about-service-containers#mapping-docker-host-and-service-container-ports)」を参照してください。

```yaml{:copy}
jobs:
  # ランナージョブのラベル
  runner-job:
    # サービスコンテナもしくはコンテナジョブを使う場合にはLinux環境を使わなければならない
    runs-on: ubuntu-latest

    # `runner-job`と実行されるサービスコンテナ
    services:
      # サービスコンテナへのアクセスに使われるラベル
      postgres:
        # Docker Hubのイメージ
        image: postgres
        # postgresにパスワードを提供
        env:
          POSTGRES_PASSWORD: postgres
        # postgresが起動するまで待つヘルスチェックの設定
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          # サービスコンテナ上のTCPポート5432をホストにマップ
          - 5432:5432
```

### ステップの設定

{% data reusables.github-actions.service-template-steps %}

```yaml{:copy}
steps:
  # CI テストを実行する前に、リポジトリにコードのコピーをダウンロードする
  - name: Check out repository code
    uses: actions/checkout@v2

  # `package.json` ファイル内のすべての依存関係のクリーンインストールを実行する
  # 詳しい情報については https://docs.npmjs.com/cli/ci.html を参照する
  - name: Install dependencies
    run: npm ci

  - name: Connect to PostgreSQL
    # PostgreSQL テーブルを作成し、テーブルにデータを入力してから
    # データを取得するスクリプトを実行する
    run: node client.js
    # `client.js` スクリプトが新しいPostgreSQLクライアントの
    # 作成に使う環境変数
    env:
      # PostgreSQLサービスコンテナとの通信に使われるホスト名
      POSTGRES_HOST: localhost
      # デフォルトのPostgreSQLポート
      POSTGRES_PORT: 5432
```

{% data reusables.github-actions.postgres-environment-variables %}

{% data reusables.github-actions.service-container-localhost %}

## PostgreSQLサービスコンテナのテスト

次のスクリプトを使用してワークフローをテストできます。このスクリプトは、PostgreSQL サービスに接続し、プレースホルダーデータを含む新しいテーブルを追加します。 そしてそのスクリプトは PostgreSQL テーブルに保存されている値をターミナルに出力します。 スクリプトには好きな言語を使えますが、この例ではNode.jsとnpmモジュールの`pg`を使っています。 詳しい情報については[npm pgモジュール](https://www.npmjs.com/package/pg)を参照してください。

*client.js*を修正して、ワークフローで必要なPostgreSQLの操作を含めることができます。 この例では、スクリプトは PostgreSQL サービスに接続し、`postgres` データベースにテーブルを追加し、プレースホルダーデータを挿入してから、データを取得します。

{% data reusables.github-actions.service-container-add-script %}

```javascript{:copy}
const { Client } = require('pg');

const pgclient = new Client({
    host: process.env.POSTGRES_HOST,
    port: process.env.POSTGRES_PORT,
    user: 'postgres',
    password: 'postgres',
    database: 'postgres'
});

pgclient.connect();

const table = 'CREATE TABLE student(id SERIAL PRIMARY KEY, firstName VARCHAR(40) NOT NULL, lastName VARCHAR(40) NOT NULL, age INT, address VARCHAR(80), email VARCHAR(40))'
const text = 'INSERT INTO student(firstname, lastname, age, address, email) VALUES($1, $2, $3, $4, $5) RETURNING *'
const values = ['Mona the', 'Octocat', 9, '88 Colin P Kelly Jr St, San Francisco, CA 94107, United States', 'octocat@github.com']

pgclient.query(table, (err, res) => {
    if (err) throw err
});

pgclient.query(text, values, (err, res) => {
    if (err) throw err
});

pgclient.query('SELECT * FROM student', (err, res) => {
    if (err) throw err
    console.log(err, res.rows) // Print the data in student table
    pgclient.end()
});
```

このスクリプトは、PostgreSQL サービスへの新しい接続を作成し、`POSTGRES_HOST` および `POSTGRES_PORT` 環境変数を使用して PostgreSQL サービスの IP アドレスとポートを指定します。 `host`と`port`が定義されていない場合、デフォルトのホストは`localhost`で、デフォルトのポートは5432になります。

スクリプトはテーブルを作成し、そのテーブルにプレースホルダーデータを展開します。 `postgres` データベースにデータが含まれていることをテストするために、スクリプトはテーブルの内容をコンソールログに出力します。

このワークフローを実行すると、「PostgreSQL への接続」ステップに次の出力が表示されます。これにより、PostgreSQL テーブルが正常に作成されてデータが追加されたことを確認できます。

```
null [ { id: 1,
    firstname: 'Mona the',
    lastname: 'Octocat',
    age: 9,
    address:
     '88 Colin P Kelly Jr St, San Francisco, CA 94107, United States',
    email: 'octocat@github.com' } ]
```
