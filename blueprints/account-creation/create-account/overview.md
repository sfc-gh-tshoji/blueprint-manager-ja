このステップでは、組織内に新しい Snowflake アカウントを作成する SQL コマンドを実行します。このコマンドは GLOBALORGADMIN ロールを使用して組織アカウントから実行する必要があります。

**アカウントコンテキスト:** このステップは GLOBALORGADMIN ロールを使用して組織アカウントから実行します。

## **なぜこれが重要か？**

これは新しいアカウントが実際に Snowflake に作成される瞬間です。CREATE ACCOUNT コマンドは:
- 新しい独立した Snowflake アカウントをプロビジョニングする
- ACCOUNTADMIN 権限を持つ初期管理者ユーザーを作成する
- 組織のアカウントリストにアカウントを登録する
- 一意の URL からアカウントにアクセスできるようにする

## **前提条件**

- アカウントの目的とパラメーターの設定済み（前のステップ）
- 組織アカウントでの GLOBALORGADMIN ロールアクセス
- Snowflake へのネットワーク接続

## **主要な概念**

**CREATE ACCOUNT コマンド**
Snowflake 組織内に新しいアカウントを作成するために使用される SQL コマンド。ORGADMIN ロールが必要で、組織対応アカウントからのみ実行できます。

**ORGADMIN ロール**
組織内でアカウント管理操作を可能にするシステムロール。このロールは ACCOUNTADMIN とは異なります — アカウント内のリソースではなく、アカウント自体を管理します。

**アカウントのアクティベーション**
作成後、初期管理者はアクティベーション手順を含むメールを受け取ります。アカウントはその URL ですぐにアクセス可能になります。

**追加情報:**
* [CREATE ACCOUNT](https://docs.snowflake.com/en/sql-reference/sql/create-account) — SQL コマンドリファレンス
* [アカウントの管理](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts) — アカウントライフサイクル管理
* [ORGADMIN ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-orgadmin-role) — ロールの権限

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
前のステップでプラットフォームファウンデーションの命名規則に基づいた推奨名が生成されました。確認またはカスタマイズします。

**命名要件:**
- 組織内で一意である必要がある
- 文字、数字、アンダースコアを含めることができる
- 数字で始めることはできない
- 最大 255 文字
- 内部的に大文字に変換される

#### このアカウントはどの Snowflake エディションを使用しますか？（`new_account_edition`: multi-select）
**オプション:**
- Standard
- Enterprise
- Business Critical

#### このアカウントはどのクラウドリージョンでホストされますか？（`new_account_region`: text）
アカウントが作成される Snowflake リージョン ID を指定します。空白のままにすると、組織アカウントと同じリージョンにアカウントが作成されます。

**一般的なリージョン ID:**

| クラウド | リージョン | ID |
|---------|----------|-----|
| AWS | US West 2（オレゴン） | `AWS_US_WEST_2` |
| AWS | US East 1（バージニア北部） | `AWS_US_EAST_1` |
| AWS | EU West 1（アイルランド） | `AWS_EU_WEST_1` |
| Azure | East US 2 | `AZURE_EASTUS2` |
| Azure | West Europe | `AZURE_WESTEUROPE` |
| GCP | US Central 1 | `GCP_US_CENTRAL1` |

#### 初期管理者のユーザー名は何にしますか？（`new_account_admin_name`: text）
このアカウントの最初の ACCOUNTADMIN ユーザーのユーザー名（ログイン名）を指定します。

#### 初期管理者のメールアドレスは何ですか？（`new_account_admin_email`: text）
初期 ACCOUNTADMIN ユーザーのメールアドレスを提供します。このメールでアカウントのアクティベーション通知とパスワードリセットリンクを受け取ります。

#### このアカウントの目的の簡単な説明を提供してください。（`account_description`: text）
このアカウントの用途を説明する短い説明を書きます。

#### インフラデータベースレプリケーショングループの名前は何にしますか？（`infrastructure_replication_group`: text）
インフラデータベースを他のアカウントに同期するレプリケーショングループオブジェクトの名前を選択します。
**デフォルトの推奨:** infrastructure_replication_group

#### Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
Snowflake 組織名は URL の最初の部分です。例: `https://ACME-prod.snowflakecomputing.com` → 組織名は ACME。

#### 組織アカウントの名前は何にしますか？（`org_account_name`: text）
**推奨名:** ORG。1 つの組織に組織アカウントは 1 つだけなので、この特別な目的を明確に示す名前にします。

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)
