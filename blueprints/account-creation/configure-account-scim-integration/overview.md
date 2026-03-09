このステップでは、ID プロバイダーからこの Snowflake アカウントへの自動ユーザープロビジョニングを有効にする SCIM（System for Cross-domain Identity Management）統合を設定します。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## なぜこれが重要か？

SCIM 統合はユーザーのライフサイクルを自動化します:
- **オンボーディング**: IdP に追加されるとユーザーが自動的に作成される
- **オフボーディング**: IdP から削除されるとユーザーが自動的に無効化される
- **属性同期**: ユーザー属性（名前、メール）が同期されたままになる

SCIM がないと、各 Snowflake アカウントでユーザーを手動で作成・削除する必要があります。

## 外部の前提条件

- ID プロバイダーの設定済み（Okta、Azure AD、またはその他の SCIM 2.0 互換）
- IdP から Snowflake へのネットワークアクセス（SCIM API コール用）
- IdP で新しい SCIM アプリケーション/統合を設定できる能力

## 主要な概念

**SCIM セキュリティ統合**
SCIM プロビジョニングを有効にする Snowflake オブジェクト。各アカウントには独自の統合が必要で、アカウント間で共有することはできません。

**SCIM トークン**
SCIM 統合を作成するときに生成されるシークレットトークン。このトークンは SCIM API リクエストを認証するために IdP に提供されます。

**追加情報:**
* [SCIM の概要](https://docs.snowflake.com/en/user-guide/scim)
* [Okta SCIM 設定](https://docs.snowflake.com/en/user-guide/scim-okta)
* [Azure AD SCIM 設定](https://docs.snowflake.com/en/user-guide/scim-azure)

### 設定の質問

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### このアカウントの SCIM 統合に使用する名前は何ですか？（`account_scim_integration_name`: text）
このアカウントの SCIM セキュリティ統合オブジェクトの名前を提供します。

**例:**
- `OKTA_SCIM_INTEGRATION`
- `AAD_SCIM_INTEGRATION`

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認します。

#### SCIM プロビジョニングに許可する IP アドレスは何ですか？（`account_scim_allowed_ips`: list）
ID プロバイダーが Snowflake への SCIM API コールを行うことができる IP アドレスを入力します。IdP の IP 範囲に制限することで、不正なプロビジョニング試行を防ぎます。

#### Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
URL の最初の部分から組織名を確認します。
