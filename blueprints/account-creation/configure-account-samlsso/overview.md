このステップでは、このアカウントに SAML ベースのシングルサインオン（SSO）を設定します。これにより、ユーザーは別の Snowflake パスワードではなく、ID プロバイダーの認証情報を使用して認証できるようになります。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## なぜこれが重要か？

SAML/SSO が提供するもの:
- **シームレスな認証**: ユーザーは会社の認証情報でログインする
- **集中制御**: IdP で認証ポリシーを管理
- **パスワード疲れの軽減**: 追加のパスワードを記憶する必要がない
- **セキュリティの向上**: IdP の MFA と条件付きアクセスポリシーを活用

## 外部の前提条件

- Snowflake アプリケーションで設定済みの ID プロバイダー
- IdP メタデータ（証明書、SSO URL、発行者）
- IdP でアプリケーション設定を構成できる能力

## 主要な概念

**IdP メタデータ**
SAML を設定するために必要な ID プロバイダーの情報:
- **SSO URL**: Snowflake がユーザーを認証のためにリダイレクトする場所
- **証明書**: IdP の署名を検証するための公開鍵
- **発行者/エンティティ ID**: IdP の一意識別子

**追加情報:**
* [SAML SSO 設定](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth)
* [Okta SAML セットアップ](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-okta)
* [Azure AD SAML セットアップ](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-azure)

### 設定の質問

#### このアカウントの SAML 統合に使用する名前は何ですか？（`account_saml_integration_name`: text）
SAML セキュリティ統合オブジェクトの名前を提供します。

**例:** `OKTA_SAML_INTEGRATION`、`AAD_SAML_INTEGRATION`、`CORPORATE_SSO`

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認します。

#### ID プロバイダーの発行者（エンティティ ID）は何ですか？（`account_saml_issuer`: text）
SAML アサーションにおける ID プロバイダーの一意識別子。

**確認方法:**
- **Okta**: アプリケーション > サインオンタブ > Identity Provider Issuer
- **Azure AD**: エンタープライズアプリ > シングルサインオン > Azure AD Identifier

#### ID プロバイダーの SSO URL は何ですか？（`account_saml_sso_url`: text）
Snowflake が SAML 認証のためにユーザーをリダイレクトする URL。

#### ID プロバイダーの X.509 証明書は何ですか？（`account_saml_certificate`: text）
IdP からの SAML アサーションを検証するために使用される公開証明書。BEGIN/END 行を含む証明書全体を貼り付けます。

#### Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
URL の最初の部分から組織名を確認します。
