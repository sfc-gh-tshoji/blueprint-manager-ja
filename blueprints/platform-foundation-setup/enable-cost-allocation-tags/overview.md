このステップでは、前のステップで確立されたタグフレームワークを追加の**コスト配分タグ**で拡張するかどうかを決定します。タグは Snowflake リソース全体の支出を整理、追跡、分析するのに役立つ重要なキーと値のペアです。潜在的なコスト配分タグには次のものが含まれます:

* COST_CENTER - 会計コストセンターコード
* OWNER - 担当チームまたは個人
* PROJECT - 特定のプロジェクトまたはイニシアチブ
* APPLICATION - アプリケーションまたはシステム名

## なぜこれが重要か？

コスト配分タグは堅牢な財務運用（FinOps）実践を可能にします:

* **チャージバック**: ビジネスユニットに実際の使用量を請求する
* **ショーバック**: 請求せずにチームの支出を表示する
* **最適化**: 最適化のためのコストの高い領域を特定する
* **説明責任**: リソースの責任者を追跡する

## アカウントコンテキスト
このステップは組織アカウント（作成済みの場合）またはプライマリアカウントで実行してください。

## 前提条件

* Snowflake リソースの財務上の所有権と責任構造を決定する

## 主要な概念

**Snowflake のタグとは？**

[タグ](https://docs.snowflake.com/en/user-guide/object-tagging/introduction)は別の Snowflake オブジェクトに割り当てることができるスキーマレベルのオブジェクトです。タグはキーと値のペアとして保存され、タグ名がキーとなり、タグをオブジェクトに割り当てるときに文字列値を関連付けます。

**タグはどのように Snowflake のコスト追跡を可能にするか？**

コスト配分のために作成された[タグ](https://docs.snowflake.com/en/user-guide/object-tagging/introduction)は、ウェアハウス、データベース、その他のオブジェクトに適用でき、Snowflake の[ACCOUNT\_USAGE ビュー](https://docs.snowflake.com/en/sql-reference/account-usage)（例: WAREHOUSE\_METERING\_HISTORY、TAG\_REFERENCES）と結合して、タグ値別のクレジット消費を計算できます。これにより、コストセンター、チーム、またはプロジェクト別に支出を集計するコスト配分レポートを作成できます。詳細な例については[タグを使用したコストの帰属](https://docs.snowflake.com/en/user-guide/cost-attributing)を参照してください。

## ベストプラクティス

**コスト配分タグを早期に実装するかどうかを決めてください！**

環境を最初に設定するときにタグを実装するのが大幅に簡単です。既存のオブジェクトにはいつでもタグを追加できますが、Snowflake の使用ビューの過去の消費データはタグのコンテキストなしに記録されます — 新しく適用されたタグと過去のクレジット使用量を遡って関連付けることはできません。これは、コスト配分レポートがタグが適用された時点以降のタグ付き使用量のみを反映することを意味します。

## 追加情報

* [オブジェクトタグの概要](https://docs.snowflake.com/en/user-guide/object-tagging/introduction) — Snowflake のタグフレームワークの概要
* [タグを使用したコストの帰属](https://docs.snowflake.com/en/user-guide/cost-attributing) — コスト配分とレポートにタグを使用する

### 設定の質問

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**例:** PLAT\_INFRA
**追加情報:**
* [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database)
* [Object Identifiers](https://docs.snowflake.com/en/sql-reference/identifiers)

#### ガバナンススキーマに付ける名前は何ですか？（`governance_name`: text）
**推奨名:** GOVERNANCE
**追加情報:**
* [CREATE SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/create-schema)
* [マネージドアクセススキーマ](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas)

#### 追加のコスト配分タグを追加しますか？（`enable_cost_tags`: multi-select）
**何を聞いているか？** コアプラットフォームタグ以外に追加のコスト配分タグを設定するかどうかを決定します。

**なぜ重要か？** 追加のコストタグにより、より詳細なコスト追跡が可能になり、組織の会計システムと統合できます。

**すでにプラットフォーム管理用に持っているタグ:**
* domain - ビジネスユニットまたは部門
* environment - SDLC ステージ（dev、test、prod）
* dataproduct - データ製品識別子
* workload - ワークロードタイプ
* zone - データゾーン
* data\_classification - データ機密レベル

**次のステップで設定される追加タグ:**
* cost\_center - 会計コストセンターコード
* owner - 担当チームまたは個人
* project - 特定のプロジェクトまたはイニシアチブ
* application - アプリケーションまたはシステム名

**オプションの説明:**
**はい（推奨）:**
* 追加の FinOps およびコスト配分タグを作成するために次のステップに進む

**いいえ:**
* FinOps のために以前のステップのコアプラットフォームタグ（ドメイン、環境など）のみを使用する
* 必要に応じて後でタグを追加できる

**推奨事項:** はいを選択して、少なくとも cost\_center と owner タグを追加し、より良い財務説明責任を確保します。

**追加情報:**
* [タグを使用したコストの帰属](https://docs.snowflake.com/en/user-guide/cost-attributing)
**オプション:**
- Yes
- No
