このステップでは、異なるタイプのユーザーが Snowflake に認証する方法を定義する認証ポリシーを設定します。

**アカウントコンテキスト:** これらの認証ポリシーは組織アカウント（作成済みの場合）またはプライマリアカウントに適用されます。

## **なぜこれが重要か？**

認証ポリシーは、ユーザーに許可される認証方法を制御します。異なるユーザータイプには異なるセキュリティ要件があります:

* **人間のユーザー**: セキュリティのために MFA を使用した SSO で認証する必要があります
* **サービスアカウント**: OAuth、キーペア、またはトークンを使用する必要があります（パスワードは不可）
* **ブレークグラスアカウント**: フォールバックとしてパスワードアクセスが必要です

適切な認証ポリシーがないと:

* サービスアカウントが安全でないパスワード認証を使用するかもしれません
* 人間のユーザーが MFA 要件をバイパスするかもしれません
* ブレークグラスアカウントが日常業務に使用されるかもしれません

## **外部前提条件**

* SAML/SSO 統合が設定済み
* ブレークグラスアカウントが作成済み
* 組織の認証要件の理解

## **主要な概念**

**認証ポリシー** 許可される認証方法、MFA 要件、クライアントタイプの制限を指定する Snowflake オブジェクト。認証ポリシーを、異なるドアの「入場要件」と考えてください — 正面玄関（人間のユーザー）には厳格な要件があり、サービス入口（サービスアカウント）には異なる要件があります。

**認証方法** ユーザーが身元を証明する方法:

* PASSWORD: ユーザー名とパスワード
* SAML: SAML アサーションによる SSO
* OAUTH: OAuth 2.0 トークン
* KEYPAIR: RSA キーペア認証
* PAT: パーソナルアクセストークン

**MFA 認証方法** 多要素認証オプション:

* TOTP: タイムベースのワンタイムパスワード（認証アプリ）
* PASSKEY: FIDO2/WebAuthn パスキー

**クライアントタイプ** 認証を使用できるクライアント:

* SNOWFLAKE\_UI: Web インターフェース
* SNOWSIGHT: Snowsight Web アプリ
* DRIVERS: JDBC、ODBC、Python など
* SNOWSQL: SnowSQL CLI

**ベストプラクティス: 多層セキュリティ** 異なるユーザータイプには異なるポリシーが必要です — 建物に VIP 入口、従業員入口、配送入口があり、それぞれに適切なセキュリティチェックがあるようなものです。

**追加情報:**

* [認証ポリシー](https://docs.snowflake.com/en/user-guide/authentication-policies) — 認証ポリシーオプションの概要
* [CREATE AUTHENTICATION POLICY](https://docs.snowflake.com/en/sql-reference/sql/create-authentication-policy) — SQL コマンドリファレンス
* [Snowflake の MFA](https://docs.snowflake.com/en/user-guide/security-mfa) — 多要素認証のセットアップ

### 設定の質問

#### このアカウントの人間のユーザーに許可される認証方法は何ですか？（`human_auth_methods`: multi-select）
**何を聞いているか？**
人間のユーザー（インタラクティブユーザー）がこのアカウントに認証する方法を選択します。プラットフォームファウンデーションの値が事前入力されています — 一貫性のためにそのまま受け入れるか、このアカウントに異なる要件がある場合は変更してください。

**なぜ重要か？**
認証ポリシーはデータへのゲートウェイです。セキュリティと使いやすさの適切なバランスにより以下が確保されます:
- 不正アクセスからの保護
- セキュリティ要件へのコンプライアンス
- 正当なユーザーの良いエクスペリエンス
- 組織のセキュリティ姿勢との整合性

**継承値:**
この回答はプラットフォームファウンデーション設定から事前入力されています。ほとんどの組織はアカウント間で認証を一貫させますが、次の場合は異なる設定が必要なことがあります:
- 開発アカウント（俊敏性のためにより緩やか）
- 本番アカウント（より厳格な要件）
- 外部向けアカウント（より厳格または異なる IdP）

**オプションの説明:**

**SAML のみ（SSO 必須）:** *（SAML が設定されている場合のみ表示）*
- ユーザーは ID プロバイダーを通じて認証する必要があります
- 最も強力な集中管理を提供します
- 本番アカウントに推奨
- **注記:** 緊急アクセス用のブレークグラスアカウントが必要です

**SAML またはパスワード（MFA 付き）:** *（SAML が設定されている場合のみ表示）*
- ユーザーは SSO またはパスワード + MFA を使用できます
- セキュリティを維持しながら柔軟性を提供します
- SSO が常に利用可能とは限らないアカウントに適しています

**パスワード（MFA 付き）のみ:**
- ユーザーは Snowflake パスワードと MFA で認証します
- SSO 依存なしの強力なセキュリティ
- このアカウントが IdP と統合していない場合に使用

**推奨事項:**
このアカウントがプラットフォームファウンデーションとは異なる特定の要件がない限り、プラットフォームファウンデーションの値を受け入れます。

**追加情報:**
* [認証ポリシー](https://docs.snowflake.com/en/user-guide/authentication-policies) — ポリシー設定ガイド
**オプション:**
- SAML Only (SSO required)
- SAML or Password with MFA
- Password with MFA Only

#### SAML 統合に付ける名前は何ですか？（`saml_integration_name`: text）
**例:** `OKTA_SSO`、`AZURE_AD_SAML`、`PING_SSO`
**追加情報:**
* [CREATE SECURITY INTEGRATION (SAML2)](https://docs.snowflake.com/en/sql-reference/sql/create-security-integration-saml2)

#### パスワード認証にはどの MFA 方法が必要ですか？（`mfa_method`: multi-select）
**何を聞いているか？**
ユーザーがパスワードで認証するときに要求する多要素認証方法を選択します。

**なぜ重要か？**
MFA はパスワード盗難によるアカウント侵害のリスクを大幅に軽減します。

**オプションの説明:**
- **TOTP（認証アプリ）**: Google Authenticator、Microsoft Authenticator、Duo などのアプリからのタイムベースコード。広くサポートされています。
- **パスキー（FIDO2/WebAuthn）**: ハードウェアキーまたは生体認証。最も安全ですが互換デバイスが必要です。
- **TOTP またはパスキー**: ユーザーが選択できます。柔軟性のために推奨。

**推奨事項:**
**TOTP またはパスキー**はセキュリティとユーザーの柔軟性の最適なバランスを提供します。

**追加情報:**
* [Snowflake の MFA](https://docs.snowflake.com/en/user-guide/security-mfa)
**オプション:**
- TOTP (Authenticator Apps)
- Passkey (FIDO2/WebAuthn)
- Either TOTP or Passkey

#### サービスアカウントに許可される認証方法は何ですか？（`service_auth_methods`: multi-select）
**何を聞いているか？**
サービスアカウント（自動化されたプロセス、アプリケーション）の認証方法を定義します。

**なぜ重要か？**
サービスアカウントはパスワード認証を使用すべきではありません。パスワードはセキュリティが低く、ローテーションが難しいです。

**オプションの説明:**
- **OAuth のみ**: サービスは OAuth トークンを使用する必要があります。OAuth をサポートするクラウドアプリケーションに最適。
- **キーペアのみ**: サービスは RSA キーペアを使用する必要があります。オンプレミスまたはカスタムアプリケーションに最適。
- **OAuth またはキーペア**: どちらの方法も許可。柔軟性のために推奨。
- **OAuth、キーペア、または PAT**: パーソナルアクセストークンを追加。PAT は管理が簡単ですが安全性が低いです。

**推奨事項:**
**OAuth またはキーペア**は、異なる統合パターンに対応しながらセキュリティを提供します。

**追加情報:**
* [キーペア認証](https://docs.snowflake.com/en/user-guide/key-pair-auth)
* [OAuth](https://docs.snowflake.com/en/user-guide/oauth)
**オプション:**
- OAuth Only
- Key Pair Only
- OAuth or Key Pair
- OAuth, Key Pair, or PAT

#### 認証ポリシーをアカウントレベルで適用しますか？（`apply_auth_policies_account_level`: multi-select）
**何を聞いているか？**
デフォルトですべてのユーザーに人間ユーザー認証ポリシーを強制するかどうかを決定します。

**なぜ重要か？**
- **はい**: すべてのユーザーは、特定のオーバーライドがない限り（ブレークグラスなど）ポリシーに準拠する必要があります
- **いいえ**: ポリシーは明示的に割り当てたユーザーにのみ適用されます

**推奨事項:**
- 初期展開とテスト中は**いいえ**から始める
- ポリシーが正常に動作することを確認したら**はい**に移行する
- まずブレークグラスアカウントに独自のポリシーがあることを確認する

**追加情報:**
* [認証ポリシーの有効化](https://docs.snowflake.com/en/user-guide/authentication-policies#activating-an-authentication-policy)
**オプション:**
- Yes - Apply default policy to all users
- No - Apply only to specific users

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### 管理者として設定するのは誰ですか？（`manual_admin_users`: object-list）
管理者ロール（admin_role フィールド）オプション: ACCOUNTADMIN, SECURITYADMIN, SYSADMIN, USERADMIN

#### 管理者ロールを付与するのは誰ですか？（`scim_admin_users`: object-list）
管理者ロール（admin_role フィールド）オプション: ACCOUNTADMIN, SECURITYADMIN, SYSADMIN, USERADMIN
