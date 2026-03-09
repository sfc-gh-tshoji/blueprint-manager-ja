このステップでは、シングルサインオン（SSO）のための SAML（Security Assertion Markup Language）を設定し、ID プロバイダーから Snowflake へのフェデレーテッド認証を有効にします。

**アカウントコンテキスト:** このステップでは、組織アカウント（作成済みの場合）またはプライマリアカウントの SSO を設定します。

## なぜこれが重要か？

SAML SSO は ID プロバイダーを通じた集中認証を提供し、以下を実現します:
- **シングルサインオン**: ユーザーは会社の IdP で一度認証するだけです
- **集中セキュリティ**: 認証ポリシーが 1 か所で管理されます
- **ユーザーエクスペリエンスの向上**: 別の Snowflake パスワードを管理する必要がありません
- **セキュリティの強化**: 既存の MFA、条件付きアクセス、セキュリティポリシーを活用します
- **コンプライアンス**: 集中認証ログで監査要件を満たします

## 外部前提条件

- SAML 2.0 をサポートする ID プロバイダー（IdP）
- SAML アプリケーションを設定するための IdP への管理者アクセス
- Snowflake の ACCOUNTADMIN または SECURITYADMIN ロール
- IdP の SAML メタデータ（証明書、SSO URL、発行者）

## 主要な概念

**SAML（Security Assertion Markup Language）**
ID プロバイダーとサービスプロバイダー間で認証データを交換するオープンスタンダード。SAML を「デジタルパスポート」と考えてください — IdP がパスポートにスタンプを押し（アサーションを作成し）、Snowflake が国境で受け入れます（検証してアクセスを許可）。

**ID プロバイダー（IdP）**
ユーザーを認証し SAML アサーションを発行するシステム（例: Okta、Azure AD、Ping）。IdP はあなたの身元を確認する「パスポートオフィス」です。

**サービスプロバイダー（SP）**
SAML アサーションを受け入れるアプリケーション（Snowflake）。Snowflake は IdP が発行したパスポートを信頼する「目的地」です。

**SSO URL**
ユーザーが IdP で認証するためにリダイレクトされる URL。これはユーザーが「パスポートにスタンプを押してもらう」場所です。

**X.509 証明書**
IdP からの SAML アサーションを検証するために使用される証明書。これは Snowflake がパスポートが本物で偽造されていないことを確認する方法です。

**追加情報:**
* [フェデレーテッド認証](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth) — Snowflake における SSO とフェデレーテッド認証の概要
* [SAML 概要](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-overview) — SAML の概念とフローの理解
* [Snowflake を SP として設定する](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-configure-snowflake) — サービスプロバイダーのセットアップガイド

### 設定の質問

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### SAML 統合に付ける名前は何ですか？（`saml_integration_name`: text）
**何を聞いているか？**
Snowflake に作成される SAML セキュリティ統合の名前を提供します。

**なぜ重要か？**
統合名は SAML 設定を参照するために使用され、IdP 起点の SSO のログイン URL に表示されます。

**形式:**
- 大文字とアンダースコアを使用する
- 明確にするために IdP 名を含める
- 例: `OKTA_SSO`、`AZURE_AD_SAML`、`PING_SSO`

**推奨事項:**
`<IdP>_SSO` または `<IdP>_SAML` の形式を使用します（`<IdP>` は ID プロバイダー名）。

**追加情報:**
* [CREATE SECURITY INTEGRATION (SAML2)](https://docs.snowflake.com/en/sql-reference/sql/create-security-integration-saml2)

#### ID プロバイダーの発行者/エンティティ ID は何ですか？（`saml_issuer`: text）
**何を聞いているか？**
ID プロバイダーからの発行者（エンティティ ID とも呼ばれる）を提供します。これにより IdP が一意に識別されます。

**なぜ重要か？**
発行者は SAML アサーションが期待された ID プロバイダーから来ていることを確認するために使用されます。

**見つけ方:**
- **Okta**: SAML アプリケーション設定の「Identity Provider Issuer」に表示
- **Azure AD**: SAML 設定の「Azure AD Identifier」として表示
- **Ping**: アプリケーション接続設定の「Entity ID」に表示

**形式:**
通常、次のような URL: `http://www.okta.com/exk1234567890` または URN。

**追加情報:**
* [SAML 設定](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-configure-snowflake)

#### ID プロバイダーの SSO URL は何ですか？（`saml_sso_url`: text）
**何を聞いているか？**
ID プロバイダーからの SSO URL を提供します。これは Snowflake がユーザーを認証のためにリダイレクトする場所です。

**なぜ重要か？**
この URL は SP 起点の SSO に必要です。ユーザーが Snowflake から始めて IdP にリダイレクトされます。

**見つけ方:**
- **Okta**: SAML アプリケーション設定の「Identity Provider Single Sign-On URL」に表示
- **Azure AD**: エンタープライズアプリケーションの SAML 設定の「Login URL」に表示
- **Ping**: アプリケーション接続設定に表示

**形式:**
完全な URL、通常: `https://your-idp.com/app/snowflake/sso/saml`

**追加情報:**
* [SAML 設定](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-configure-snowflake)

#### ID プロバイダーの X.509 署名証明書は何ですか？（`saml_certificate`: text）
**何を聞いているか？**
ID プロバイダーからの X.509 証明書を提供します。この証明書は SAML アサーションを検証するために使用されます。

**なぜ重要か？**
Snowflake はこの証明書を使用して、SAML アサーションが実際に IdP から来ており、改ざんされていないことを確認します。

**見つけ方:**
- IdP の SAML 設定から証明書をダウンロードする
- テキストエディタで証明書ファイルを開く
- `-----BEGIN CERTIFICATE-----` と `-----END CERTIFICATE-----` を含む全内容をコピーする

**形式:**
BEGIN と END マーカーを含む PEM 形式の完全な証明書。

**セキュリティノート:**
証明書は公開鍵であり、設定に含めることは安全です。秘密鍵と混同しないでください。

**追加情報:**
* [SAML 証明書要件](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-configure-snowflake#label-fed-auth-configure-cert)

#### あなたの Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
**追加情報:**
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### すべてのアカウント名に追加するプレフィックスは何ですか？（`account_name_prefix`: text）
**プレフィックスを使用しない場合は `NONE` と入力してください。**
**追加情報:**
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### 組織アカウントに付ける名前は何ですか？（`org_account_name`: text）
**推奨名:** ORG
**追加情報:**
* [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts)
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### Snowflake ログインページに SSO でログインするボタンを表示しますか？（`saml_sso_login_page`: multi-select）
**何を聞いているか？**
Snowflake ログインページに「SSO でログイン」ボタンを追加するかどうかを決定します。

**なぜ重要か？**
- **はい**: ユーザーはログインページで IdP を通じて認証するボタンを表示します（推奨）
- **いいえ**: ユーザーは IdP 起点の SSO または直接 SSO URL を使用する必要があります

**推奨事項:**
ユーザーにログインページで簡単な SSO オプションを提供するために**はい**を選択します。

**追加情報:**
* [ログインページオプション](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth-configure-snowflake#label-fed-auth-configure-login-page)
**オプション:**
- Yes
- No

#### シングルサインオン用に SAML/SSO を設定しますか？（`configure_saml`: multi-select）
**オプション:**
- Yes - Configure SAML now
- No - Configure later or use password authentication
