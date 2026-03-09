このステップでは、このワークフローの残りと将来のワークフローで使用される基盤オブジェクトを作成します:

1. **インフラデータベース** — プラットフォーム全体のガバナンスと管理オブジェクトの「ホーム」として機能する集中型データベース
2. **ガバナンススキーマ** — タグ、ネットワークルール、ポリシーが保存されるデータベース内のスキーマ（マネージドアクセスが有効）

後続のステップで作成されるオブジェクト（データベースオブジェクト名を標準化しコスト配分を可能にするタグ、ネットワークルールとポリシーなど）は、このデータベースとスキーマに保存されます。

## なぜこれが重要か？

シングルアカウント戦略とマルチアカウント戦略のどちらを実装する場合でも、これらのオブジェクトを単一の専用インフラデータベースに格納することで、組織全体でガバナンスの一貫性が確保されます。マルチアカウント環境では、このデータベースを組織アカウントから子アカウントに共有し、すべてのビジネスユニットが同じ標準化されたメタデータとセキュリティ境界を使用することを確保できます。

## アカウントコンテキスト

このステップは、[組織アカウント](https://docs.snowflake.com/en/user-guide/organization-accounts)（作成された場合）またはプライマリアカウントで実行してください。

## 主要な概念

このステップではインフラデータベースとガバナンススキーマを作成します。以下は、Snowflake オブジェクト階層におけるこれらのオブジェクトの位置を確認するための内容です。

![Snowflake Object Hierarchy](../../images/account_strategy_images/snowflake_object_hierarchy.png)

* **組織** — 組織は、ビジネスエンティティが所有するアカウントをリンクする Snowflake のファーストクラスオブジェクトです。
* **Snowflake アカウント** — 顧客は 1 つ以上のアカウントを作成できます。
* **アカウントにはデータベースが含まれる** — 各データベースは単一の Snowflake アカウントに属します。データベースは複数のアカウントにまたがることはできませんが、他のアカウントにレプリケートまたは共有できます。
* **データベースにはスキーマが含まれる** — 各スキーマは単一の Snowflake データベースに属します。
* **スキーマにはオブジェクトが含まれる** — オブジェクトにはテーブル、タグ、ルール、ビュー、ファイルフォーマット、シーケンス、UDF、プロシージャなどが含まれます。

後続のステップで作成してこのインフラデータベースとスキーマに保存するオブジェクト（タグ、ネットワークルール、ネットワークポリシーなど）については、後続のステップで詳しく説明します。

## ベストプラクティス

* **早期にインフラの命名規則を標準化する:** 中央の Snowflake プラットフォームチームは通常、特定のアカウントレベルのオブジェクトを所有するため、Snowflake プラットフォームチームが中央プラットフォームチームを識別し、他のビジネスドメインのオブジェクトと区別するための指定された頭字語（例: plat、snow、cdp）を選択することを強くお勧めします。この名前は、何十から何百ものオブジェクトとポリシーが参照するようになってからは変更が困難になるため、この識別子を早期に選択して実装することをお勧めします。

* **[マネージドアクセススキーマ](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas)を使用する:** マネージドアクセススキーマは、個々のオブジェクト作成者ではなくスキーマオーナーがすべてのオブジェクト権限を制御するセキュリティモデルです。プラットフォームチーム（SECURITYADMIN または SYSADMIN ロール経由）のみが権限を付与でき、「シャドウ」セキュリティ設定を防ぐために、スキーマにはマネージドアクセスを使用することをお勧めします。

## テスト方法

このステップを実行したら、以下のコマンドで正常に動作したことを確認できます:

* データベースの存在を確認: SHOW DATABASES LIKE '{{ platform\_database\_name }}';
* マネージドアクセスの状態を確認: SHOW SCHEMAS IN DATABASE {{ platform\_database\_name }}; （ガバナンススキーマが is\_managed\_access で TRUE を示しているか確認）

## 追加情報

* [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database) — SQL コマンドリファレンス
* [CREATE SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/create-schema) — SQL コマンドリファレンス
* [Managed Access Schemas](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas) — 集中型権限管理
* [Object Tagging](https://docs.snowflake.com/en/user-guide/object-tagging) — ガバナンスのためのタグの使用
* [Data Sharing](https://docs.snowflake.com/en/user-guide/data-sharing-intro) — アカウント間でのオブジェクト共有

### 設定の質問

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**プラットフォーム/インフラデータベースとは何か？**
  インフラデータベースは、タグ、ネットワークルール、ガバナンスポリシー、共有プロシージャなどのプラットフォーム全体のオブジェクトを格納する集中型「ハブ」データベースです。中央プラットフォームチームが所有し、マルチアカウントデプロイメントのすべてのアカウントに共有されます。
  **推奨命名アプローチ:**
  プラットフォーム所有のインフラストラクチャを重視するデータベースとして明確に識別する名前を使用します。フォーマットは: &lt;domain&gt;\_&lt;dataproduct&gt;
  * **ドメイン:** plat（「platform」の略）またはプラットフォームチームの頭字語を使用する（例: cdp、snow、data）
  * **データプロダクト:** infra またはインフラストラクチャの目的を示す別の用語を使用する
* **例:** PLAT\_INFRA — プラットフォームチームのオーナーシップとインフラストラクチャの目的を明確に示す
  **代替例:**
  * CDP\_INFRA — クラウドデータプラットフォームインフラストラクチャ
  * SNOW\_ADMIN — Snowflake 管理
  * DATA\_PLATFORM — データプラットフォームデータベース
* **重要:** 慎重に選択してください！この名前は最終的に何十から何百ものオブジェクト、ポリシー、プロシージャによって参照されます。後で変更すると複雑でリスクが高くなります。
  **追加情報:**
  * [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database)
  * [Object Identifiers](https://docs.snowflake.com/en/sql-reference/identifiers)

#### ガバナンススキーマに付ける名前は何ですか？（`governance_name`: text）
**ガバナンススキーマとは何か？**
  ガバナンススキーマはインフラデータベース内に作成され、セキュリティ、コンプライアンス、プラットフォームガバナンスに関連するオブジェクトを含みます。これには、プラットフォームと FinOps タグ、ネットワークルール、監査ビュー、管理プロシージャが含まれます。

  **推奨名:** GOVERNANCE

  これはスキーマの目的を明確に伝える、わかりやすい自己説明的な名前です。代替オプションには以下が含まれます:
  * ADMIN — 管理
  * SECURITY — セキュリティ重視のオブジェクト
  * PLATFORM — プラットフォームレベルのオブジェクト

**スキーマ設定:**
  このスキーマは**マネージドアクセス**を有効にして作成されます。これは以下を意味します:
  * スキーマオーナー（通常は [SYSADMIN](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system) — システム管理者）のみがオブジェクトの権限を付与できます
  * オブジェクト作成者が独自のアクセスを付与する「シャドウ」セキュリティ設定を防ぎます
  * ガバナンスオブジェクトにアクセスできる人を集中管理します

**ベストプラクティス:** 機能目的を表すシンプルな単一語の名前を使用してください。

**追加情報:**
  * [CREATE SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/create-schema)
  * [Managed Access Schemas](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas)
  * [System Roles](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system)
