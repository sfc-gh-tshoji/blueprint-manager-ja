このステップでは、Snowflake エディション、クラウドリージョン、初期管理者認証情報、ローカルインフラ設定など、新しいアカウントの技術的パラメーターを設定します。

**アカウントコンテキスト:** このステップは組織アカウントから実行します。

## **なぜこれが重要か？**

これらのパラメーターは新しいアカウントの機能、場所、初期アクセスを定義します:
- **エディション**: 利用可能な機能とコンプライアンス認定を決定
- **リージョン**: データレジデンシー、レイテンシー、DR オプションに影響
- **初期管理者**: アカウントにアクセスして設定できる最初のユーザー
- **ローカルインフラ**: アカウント固有のオブジェクトのための書き込み可能なデータベースを提供（レプリケートされたインフラデータベースは読み取り専用）

## **主要な概念**

**Snowflake エディション**
- **Standard**: コア機能、基本的なセキュリティ
- **Enterprise**: マルチクラスターウェアハウス、高度なセキュリティ、90 日間のタイムトラベル
- **Business Critical**: HIPAA/PCI コンプライアンス、顧客管理の暗号化キー、プライベート接続

**追加情報:**
* [CREATE ACCOUNT](https://docs.snowflake.com/en/sql-reference/sql/create-account)
* [Snowflake エディション](https://docs.snowflake.com/en/user-guide/intro-editions)
* [サポートされているクラウドリージョン](https://docs.snowflake.com/en/user-guide/intro-regions)

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認またはカスタマイズします。

#### このアカウントはどの Snowflake エディションを使用しますか？（`new_account_edition`: multi-select）
**オプション:**
- Standard
- Enterprise
- Business Critical

#### このアカウントはどのクラウドリージョンでホストされますか？（`new_account_region`: text）
アカウントが作成される Snowflake リージョン ID を指定します。空白のままにすると、組織アカウントと同じリージョンになります。

#### 初期管理者のユーザー名は何にしますか？（`new_account_admin_name`: text）
このアカウントの最初の ACCOUNTADMIN ユーザーのログイン名を指定します。

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)

#### このアカウントはどのドメインを表しますか？（`account_domain`: multi-select）
ドメインベース戦略: このアカウントが表すビジネスドメインを選択します。

#### このアカウントはどの環境を表しますか？（`account_environment`: multi-select）
環境ベース戦略: このアカウントが表す SDLC 環境を選択します。

#### このアカウントの目的の簡単な説明を提供してください。（`account_description`: text）
このアカウントの用途を説明する短い説明を書きます。

#### 初期管理者のメールアドレスは何ですか？（`new_account_admin_email`: text）
初期 ACCOUNTADMIN ユーザーのメールアドレスを提供します。

#### ローカルインフラデータベースの名前は何にしますか？（`local_infra_database`: text）
書き込み可能なローカルインフラデータベースの名前（例: PLAT\_LOCAL）。レプリケートされたインフラデータベースとは別です。

#### セキュリティポリシーに使用するスキーマ名は何にしますか？（`local_policies_schema`: text）
**推奨名:** POLICIES

#### Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
URL の最初の部分から組織名を確認します。
