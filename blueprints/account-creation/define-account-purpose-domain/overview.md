このステップでは、新しい Snowflake アカウントが表すビジネスドメインを選択することで、アカウントの目的を定義します。ドメインベースのマルチアカウント戦略では、各アカウントはビジネスドメイン（営業、財務、HR など）に対応します。

**アカウントコンテキスト:** このステップは組織アカウントから実行します。

## なぜこれが重要か？

ドメインベースの戦略では:
- 各アカウントは異なるビジネスドメインを表す
- 環境（Dev、Test、Prod）はアカウント内のデータベースレベルで整理される
- ビジネスユニット間の強い分離が提供される
- アカウントレベルでのコスト配分はドメイン別になる

## 外部の前提条件

- ドメインベースのマルチアカウント戦略を持つプラットフォームファウンデーションワークフローの完了
- このアカウントが提供するビジネスドメインの把握

## 主要な概念

**ドメインベースのアカウント戦略**
各 Snowflake アカウントはビジネスドメインを表します。各ドメインアカウント内では、異なる環境のために別々のデータベースまたはスキーマを作成します（データ製品ワークフローで処理）。

**ドメイン**
このアカウントが提供するビジネスユニットまたは機能エリア。ドメインはプラットフォームファウンデーションタスクで定義されました（営業、財務、HR、エンジニアリングなど）。

**データベースレベルの環境**
この戦略では、環境（Dev、Test、Prod）はアカウントレベルではありません。データ製品ワークフローを実行するときに、このアカウント内の別々のデータベースまたはスキーマとして作成されます。

**追加情報:**
* [組織でのアカウント管理](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts) — アカウント管理の概要

### 設定の質問

#### このアカウントはどのドメインを表しますか？（`account_domain`: multi-select）
このアカウントが表すビジネスドメインを選択します。このアカウントは、このドメインのすべての環境（Dev、Test、Prod）を提供します。

**これが重要な理由:**
- ドメインがアカウント名の一部になる
- アカウントレベルのコスト配分がこのドメインに帰属する
- このアカウントのすべてのリソースがこのドメインに関連付けられる

#### このアカウントの目的の簡単な説明を提供してください。（`account_description`: text）
このアカウントの用途を説明する短い説明を書きます。

**例:**
- "Sales domain account - contains all environments for Sales analytics"
- "Finance domain account for financial reporting and analytics"
- "HR domain account for people analytics and workforce planning"

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)
