このステップでは、このアカウントの異なるタイプのユーザーが Snowflake に認証する方法を定義する認証ポリシーを設定します。プラットフォームファウンデーションの値がデフォルトとして事前入力されています — それらを確認し、このアカウントが異なる認証設定を必要とする場合は調整してください。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## **なぜこれが重要か？**

認証ポリシーは、異なるユーザータイプに適切なセキュリティを確保します:
- **人間のユーザー**: インタラクティブなログインに SSO や MFA が必要な場合がある
- **サービスアカウント**: プログラムによるアクセスにキーペア認証を使用する場合がある
- **ブレークグラスアカウント**: 緊急シナリオのためにパスワードのみが必要

## **前提条件**

- SAML/SSO の設定済み（SSO を使用する場合）
- このアカウントが組織アカウントとは異なる認証要件が必要かどうかの理解

## **主要な概念**

**認証方法**
- **PASSWORD**: ユーザー名とパスワード
- **SAML**: ID プロバイダー経由のシングルサインオン
- **OAUTH**: OAuth ベースの認証
- **KEYPAIR**: プログラムアクセス用の RSA キーペア
- **PAT**: パーソナルアクセストークン

**追加情報:**
* [認証ポリシー](https://docs.snowflake.com/en/user-guide/authentication-policies)
* [CREATE AUTHENTICATION POLICY](https://docs.snowflake.com/en/sql-reference/sql/create-authentication-policy)

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認します。

#### このアカウントで人間のユーザーに許可する認証方法は何ですか？（`human_auth_methods`: multi-select）
**オプション:**
- SAML Only（SSO 必須）
- SAML or Password with MFA
- Password with MFA Only

#### パスワード認証に必要な MFA 方法は何ですか？（`mfa_method`: multi-select）
**オプション:**
- TOTP（認証アプリ）
- Passkey（FIDO2/WebAuthn）
- Either TOTP or Passkey（推奨）

#### サービスアカウントに許可する認証方法は何ですか？（`service_auth_methods`: multi-select）
**オプション:**
- OAuth Only
- Key Pair Only
- OAuth or Key Pair（推奨）
- OAuth, Key Pair, or PAT

#### 認証ポリシーをアカウントレベルで適用しますか？（`apply_auth_policies_account_level`: multi-select）
**オプション:**
- Yes - Apply default policy to all users（すべてのユーザーにデフォルトポリシーを適用する）
- No - Apply only to specific users（特定のユーザーのみに適用する）

**推奨:** 初期のロールアウトとテスト中は「No」から始め、ポリシーが正しく機能することを検証したら「Yes」に移行します。

#### プラットフォームデータベースの名前は何にしますか？（`platform_database_name`: text）
インフラデータベースの名前（例: PLAT\_INFRA）。

#### ローカルインフラデータベースの名前は何にしますか？（`local_infra_database`: text）
書き込み可能なローカルインフラデータベースの名前（例: PLAT\_LOCAL）。

#### セキュリティポリシーに使用するスキーマ名は何にしますか？（`local_policies_schema`: text）
**推奨名:** POLICIES

#### このアカウントの SAML 統合に使用する名前は何ですか？（`account_saml_integration_name`: text）
SAML セキュリティ統合オブジェクトの名前（例: OKTA\_SAML\_INTEGRATION）。
