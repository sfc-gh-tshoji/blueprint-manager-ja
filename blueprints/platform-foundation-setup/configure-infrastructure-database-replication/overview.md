このステップでは、インフラデータベースを組織内のすべてのアカウントにレプリケートできるようにする Snowflake REPLICATION GROUP を作成します。このステップで生成されるもの:

1. **レプリケーショングループ**（`infrastructure_replication_group`）— インフラデータベースをターゲットアカウントに同期する名前付きレプリケーショングループ
2. **自動更新スケジュール** — レプリケートされたデータベースを同期させるための設定可能なスケジュール

レプリケーショングループは今作成されるため、**アカウント作成**ワークフローを実行するときに、新しいアカウントがインフラデータベースのレプリカを作成し、**ローカルオブジェクトにガバナンスタグを適用**できます。データ共有（共有オブジェクトが読み取り専用）とは異なり、レプリケーションは完全に使用できるローカルコピーを作成します。

**なぜ共有ではなくレプリケーションなのか？**

* **タグを適用できる**: レプリケートされたタグはローカルオブジェクトになり、ウェアハウス、データベース、その他のリソースに SET できます
* **ネットワークルールがローカルで機能する**: レプリケートされたネットワークルールはローカルネットワークポリシーで参照できます
* **完全な機能**: すべてのガバナンスオブジェクトがローカルで作成されたかのように動作します

**仕組み:**

* このステップ（組織アカウント内）は*プライマリ*レプリケーショングループを作成します
* アカウント作成ワークフロー（各新しいアカウント内）は*セカンダリ*レプリケーショングループとレプリカデータベースを作成します
* すべてのアカウントには、自動的に同期されるガバナンスオブジェクトの独自のローカルコピーができます

**アカウントコンテキスト:** このステップは組織アカウントで実行してください。

## なぜこれが重要か？

マルチアカウント戦略では、各アカウントが組織アカウントで確立されたガバナンスフレームワークにアクセスする必要があります。Snowflake のデータベースレプリケーションが提供するもの:

* **書き込み可能なガバナンスオブジェクト**: タグとネットワークルールをローカルリソースに適用できます
* **自動同期**: ソースの変更がスケジュールに従ってレプリケートされます
* **ポイントインタイムの一貫性**: レプリケーショングループ内のすべてのオブジェクトが一貫しています
* **集中管理**: 一度定義してどこにでもレプリケートします

## 前提条件

* インフラデータベースが作成されている（これはこのワークフローの*インフラデータベースの作成*ステップで作成されます）
* マルチアカウント戦略が選択されている（これはこのワークフローの前の*アカウント戦略の決定*ステップで選択されます）
* レプリケーション用に有効にされた組織（同じ組織内のすべてのアカウントがレプリケートできます）

## 主要な概念

* [**データベースレプリケーション:**](https://docs.snowflake.com/en/user-guide/account-replication-intro) アカウント間でデータベースとアカウントオブジェクトをレプリケートする Snowflake の機能。共有とは異なり、レプリケートされたオブジェクトは完全に使用できるローカルコピーになります。

* [**レプリケーショングループ:**](https://docs.snowflake.com/en/sql-reference/sql/create-replication-group) ユニットとしてレプリケートされるオブジェクトの定義されたコレクション。プライマリレプリケーショングループはソースアカウントに存在し、セカンダリレプリケーショングループはターゲットアカウントに存在します。

* [**プライマリ vs セカンダリ:**](https://docs.snowflake.com/en/user-guide/account-replication-intro#replication-groups-and-failover-groups)
  * **プライマリ**: ソースレプリケーショングループ（組織アカウント内）
  * **セカンダリ**: レプリカレプリケーショングループ（各ターゲットアカウント内）

* **タグとレプリケーション**: レプリケートされたデータベースに保存されたタグは、各ターゲットアカウントのローカルオブジェクトになります。これにより、レプリケートされたタグを使用して `ALTER WAREHOUSE SET TAG` を実行できます — 共有タグでは不可能なことです。

* **更新スケジュール**: レプリケーショングループはスケジュールに従って自動的に更新されるように設定（例: 10 分ごと）または手動で更新されるように設定できます。

## 追加情報

* [Introduction to Replication and Failover](https://docs.snowflake.com/en/user-guide/account-replication-intro) — レプリケーション機能の概要
* [CREATE REPLICATION GROUP](https://docs.snowflake.com/en/sql-reference/sql/create-replication-group) — SQL コマンドリファレンス
* [Replication Considerations](https://docs.snowflake.com/en/user-guide/account-replication-considerations) — レプリケートされたオブジェクトの重要な考慮事項
* [Replication and Tags](https://docs.snowflake.com/en/user-guide/object-tagging/interaction#replication) — タグとレプリケーションの連携方法

### 設定の質問

#### インフラデータベースのレプリケーショングループに付ける名前は何ですか？（`infrastructure_replication_group`: text）
**何を聞いているか？** インフラデータベースを他のアカウントに同期する REPLICATION GROUP オブジェクトの名前を選択します。
**なぜ重要か？** この名前はターゲットアカウントでセカンダリレプリケーショングループを作成するときに使用されます。説明的で命名規則に沿った名前を選択してください。
**推奨事項:**
* 小文字とアンダースコアを使用する
* infrastructure または governance などの明確な識別子を含める
* 簡潔だが説明的にする
* **例:**
* infrastructure_replication_group
* governance_replication_group
* platform_replication_group
* **デフォルトの推奨:** infrastructure_replication_group

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**例:** PLAT\_INFRA
**追加情報:**
* [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database)
* [Object Identifiers](https://docs.snowflake.com/en/sql-reference/identifiers)

#### どのアカウント戦略を実装したいですか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)

#### あなたの Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
**追加情報:**
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### 組織アカウントに付ける名前は何ですか？（`org_account_name`: text）
**推奨名:** ORG
**追加情報:**
* [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts)
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### インフラデータベースをターゲットアカウントにレプリケートする頻度はどれくらいですか？（`replication_schedule`: multi-select）
**何を聞いているか？** Snowflake がインフラデータベースをすべてのターゲットアカウントに自動的に同期する頻度を選択します。
**なぜ重要か？** より頻繁なレプリケーションは変更（新しいタグ、更新されたビュー）がターゲットアカウントにより早く利用可能になることを意味しますが、わずかなコストの影響があるかもしれません。
**推奨事項:**
* **10 分ごと** — アクティブな開発に最適、変更が素早く伝播します
* **30 分ごと** — ほとんどの組織にとって良いバランス
* **1 時間ごと** — 変更が少ない安定した環境に適しています
* **手動のみ** — 変更が伝播するタイミングを明示的に制御したい環境用

**注記:** インフラデータベースの変更（新しいタグ、ビュー）は通常まれなため、1 時間ごとの更新でも通常は十分です。
**オプション:**
- Every 10 minutes
- Every 30 minutes
- Every hour
- Manual only
