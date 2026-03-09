このステップでは、MFA 設定を確認し、管理者ユーザーが多要素認証を有効にできるようガイドします。これにより、特権アカウントがパスワード以外の追加のセキュリティ層を持つことが確保されます。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## **なぜこれが重要か？**

MFA はアカウント侵害のリスクを大幅に軽減します:
- パスワードが盗まれても、攻撃者は第 2 の要素が必要
- フィッシング、クレデンシャルスタッフィング、パスワード漏洩から保護
- 多くのセキュリティフレームワーク（SOC2、HIPAA、PCI）のコンプライアンスに必要

管理者アカウントに対しては、その高い権限を考慮すると MFA は特に重要です。

## **前提条件**

- 管理者ユーザーの作成またはプロビジョニング済み
- ユーザーが認証アプリにアクセスできる（Duo、Google Authenticator、Microsoft Authenticator）
- ユーザーサポートの連絡先の特定

## **主要な概念**

**TOTP（時間ベースのワンタイムパスワード）**
最も一般的な MFA 方法。ユーザーが認証アプリをインストールし、QR コードをスキャンして設定します。アプリは 30 秒ごとに新しい 6 桁のコードを生成します。

**SSO ユーザーに対する MFA**
ユーザーが SAML SSO で認証する場合、MFA は通常 Snowflake ではなく ID プロバイダーによって処理されます。IdP が MFA を強制していることを確認してください。

**追加情報:**
* [多要素認証（MFA）](https://docs.snowflake.com/en/user-guide/security-mfa)
* [MFA ベストプラクティス](https://docs.snowflake.com/en/user-guide/security-mfa-migration-best-practices)

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認します。

#### このアカウントへの管理者アクセスを持つべき人は誰ですか？（`account_admin_users`: object-list）
このアカウントで管理者権限が必要なユーザーを定義します。

#### このアカウントで人間のユーザーに許可する認証方法は何ですか？（`human_auth_methods`: multi-select）
**オプション:**
- SAML Only（SSO 必須）
- SAML or Password with MFA
- Password with MFA Only

#### パスワード認証に必要な MFA 方法は何ですか？（`mfa_method`: multi-select）
**オプション:**
- TOTP（認証アプリ）
- Passkey（FIDO2/WebAuthn）
- Either TOTP or Passkey

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### MFA サポートの連絡先は誰ですか？（`account_mfa_support_contact`: text）
ユーザーが MFA の問題（デバイスの紛失、ロックアウト、登録の問題）についてサポートを受けられる連絡先を提供します。

**例:**
- `it-helpdesk@company.com`
- `#snowflake-support`（Slack）
- `https://helpdesk.company.com/snowflake`
