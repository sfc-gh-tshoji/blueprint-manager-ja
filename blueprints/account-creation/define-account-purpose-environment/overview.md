このステップでは、新しい Snowflake アカウントが表す環境を選択することで、アカウントの目的を定義します。環境ベースのマルチアカウント戦略では、各アカウントは SDLC 環境（Dev、Test、Prod など）に対応します。

**アカウントコンテキスト:** このステップは組織アカウントから実行します。

## なぜこれが重要か？

環境ベースの戦略では:
- 各アカウントは異なる SDLC 環境を表す
- ドメイン（営業、財務、HR）はアカウント内のデータベースレベルで整理される
- 環境間（特に本番環境）の強い分離が提供される
- アカウントレベルでのコスト配分は環境別になる

## 外部の前提条件

- 環境ベースのマルチアカウント戦略を持つプラットフォームファウンデーションワークフローの完了
- このアカウントが提供する環境の把握

## 主要な概念

**環境ベースのアカウント戦略**
各 Snowflake アカウントは SDLC 環境を表します。各環境アカウント内では、異なるビジネスドメインのために別々のデータベースを作成します（データ製品ワークフローで処理）。

**環境**
このアカウントが表す SDLC ステージ。環境はプラットフォームファウンデーションタスクで定義されました（DEV、TEST、PROD など）。

**データベースレベルのドメイン**
この戦略では、ドメイン（営業、財務、HR）はアカウントレベルではありません。データ製品ワークフローを実行するときに、このアカウント内の別々のデータベースとして作成されます。

**追加情報:**
* [組織でのアカウント管理](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts) — アカウント管理の概要

### 設定の質問

#### このアカウントはどの環境を表しますか？（`account_environment`: multi-select）
このアカウントが表す SDLC 環境を選択します。このアカウントは、この環境向けにすべてのドメイン（営業、財務、HR など）を提供します。

**環境の考慮事項:**
- **DEV/DEVELOPMENT**: セキュリティが低め、実験が許可される
- **TEST/QA**: 中程度のセキュリティ、制御された変更
- **PROD/PRODUCTION**: 最高のセキュリティ、厳格な変更管理

#### このアカウントの目的の簡単な説明を提供してください。（`account_description`: text）
このアカウントの用途を説明する短い説明を書きます。

**例:**
- "Dev environment account - for development and testing across all domains"
- "Production environment account - live systems for all business domains"

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)
