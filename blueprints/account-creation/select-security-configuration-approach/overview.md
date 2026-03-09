このステップでは、プラットフォームファウンデーションワークフローで確立された同じセキュリティ設定を使用するか、このアカウントのカスタムセキュリティ設定を作成するかを選択します。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## なぜこれが重要か？

セキュリティ設定の一貫性により管理が簡素化され、組織全体のコンプライアンスが確保されます。ただし、一部のアカウントには異なる設定が必要な固有の要件がある場合があります。

この選択は以下に影響します:
- SCIM 統合設定
- ネットワークポリシーの IP 範囲
- 認証ポリシーの要件
- SAML/SSO 設定

## 外部の前提条件

- プラットフォームファウンデーションワークフローの完了
- このアカウントのセキュリティ要件の理解
- このアカウント固有の規制またはコンプライアンスニーズの把握

## 主要な概念

**組織設定を使用する場合でも、このアカウントには独自の以下が必要です:**
- SCIM セキュリティ統合（該当する場合）
- ネットワークルールとポリシー
- 認証ポリシー
- 管理者ユーザー
- ブレークグラスアクセス

**追加情報:**
* [セキュリティの概要](https://docs.snowflake.com/en/user-guide/admin-security) — Snowflake のセキュリティ機能

### 設定の質問

#### このアカウントのセキュリティをどのように設定しますか？（`security_config_approach`: multi-select）
プラットフォームファウンデーションのセキュリティ設定を複製するか、このアカウントのカスタム設定を作成するかを選択します。

**オプション:**
- Use Organization Configuration（組織設定を使用）— ほとんどのアカウントに推奨
- Configure Custom Settings（カスタム設定を作成）— 固有の規制要件があるアカウント向け

#### このアカウントに SAML/SSO を設定しますか？（`account_configure_saml`: multi-select）
**オプション:**
- Yes - Configure SAML for this account（このアカウントに SAML を設定する）
- No - Use password authentication（パスワード認証を使用する）

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認またはカスタマイズします。

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### SAML/SSO によるシングルサインオンを設定しますか？（`configure_saml`: multi-select）
**オプション:**
- Yes - Configure SAML now（今すぐ SAML を設定する）
- No - Configure later or use password authentication（後で設定するかパスワード認証を使用する）
