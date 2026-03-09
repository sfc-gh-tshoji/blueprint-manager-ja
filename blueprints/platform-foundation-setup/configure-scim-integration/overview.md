このステップでは、{{ identity_provider }} から Snowflake への自動ユーザープロビジョニングを有効にする SCIM 統合を設定します。

**アカウントコンテキスト:** このステップでは、組織アカウント（作成済みの場合）またはプライマリアカウントの SCIM を設定します。

## なぜこれが重要か？

SCIM は ID プロバイダーと Snowflake 間のユーザーアカウントのプロビジョニングとデプロビジョニングを自動化します。この統合により:
- **手動作業を削減**: ユーザーを 1 人ずつ作成する必要がなくなります
- **セキュリティを向上**: ユーザーが組織を離れると自動的に無効化されます
- **一貫性を確保**: ユーザー属性がシステム間で同期されたままになります

## 外部前提条件

- ID プロバイダー（{{ identity_provider }}）への管理者アクセス
- IdP の SCIM プロビジョニングサーバーの IP アドレス
- Snowflake の ACCOUNTADMIN ロール

## 主要な概念

**SCIM セキュリティ統合**
IdP と Snowflake 間の接続を確立する Snowflake オブジェクト。どの IdP がユーザーをプロビジョニングできるか、およびどのロールがプロビジョニングされたユーザーを所有するかを定義します。

**SCIM プロビジョナーロール**
SCIM 統合とそれを通じてプロビジョニングされたすべてのユーザーを所有する専用の Snowflake ロール。専用ロールを使用すること（ACCOUNTADMIN を直接使用するのではなく）により、より良いセキュリティの分離が提供されます。

**SCIM トークン**
IdP がユーザー変更をプッシュするときに認証するために使用する、Snowflake が生成するベアラートークン。このトークンは機密情報であり、IdP 設定に安全に保存する必要があります。

**SCIM ネットワークポリシー**
SCIM API アクセスを IdP の IP アドレスのみに制限するネットワークポリシー。これにより、権限のないシステムがユーザーを作成または変更することを防ぎます。

**追加情報:**
* [SCIM 概要](https://docs.snowflake.com/en/user-guide/scim) — Snowflake における SCIM プロビジョニングの概要
* [Okta での SCIM](https://docs.snowflake.com/en/user-guide/scim-okta) — Okta 固有の設定ガイド
* [Azure AD での SCIM](https://docs.snowflake.com/en/user-guide/scim-azure) — Microsoft Entra ID 設定ガイド
* [汎用 IdP での SCIM](https://docs.snowflake.com/en/user-guide/scim-generic) — 他の SCIM 2.0 プロバイダーの設定

### 設定の質問

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### SCIM 統合に付ける名前は何ですか？（`scim_integration_name`: text）
**何を聞いているか？**
Snowflake に作成される SCIM セキュリティ統合の名前を提供します。

**なぜ重要か？**
統合名は監査ログおよび統合の管理時に表示されます。説明的な名前は統合がどの IdP に接続しているかを識別するのに役立ちます。

**形式:**
- 大文字とアンダースコアを使用する
- 明確にするために IdP 名を含める

**例:**
- `OKTA_SCIM_INTEGRATION`
- `AZURE_AD_SCIM`
- `PING_SCIM_INTEGRATION`

**推奨事項:**
`<IdP>_SCIM_INTEGRATION` の形式を使用します（`<IdP>` は ID プロバイダー名）。

**追加情報:**
* [CREATE SECURITY INTEGRATION (SCIM)](https://docs.snowflake.com/en/sql-reference/sql/create-security-integration-scim) — SQL コマンドリファレンス

#### SCIM 統合とプロビジョニングされたユーザーを所有するのはどのロールですか？（`scim_provisioner_role`: multi-select）
**何を聞いているか？**
SCIM 統合を所有し、SCIM を通じてプロビジョニングされたユーザーの所有権が付与されるロールを選択します。

**なぜ重要か？**
Snowflake は SCIM プロビジョニングに ACCOUNTADMIN を直接使用するのではなく、専用ロールを使用することを推奨します。これにより、より良いセキュリティの分離と明確な監査証跡が提供されます。

**オプションの説明:**

| ロール | 使用する場合 |
|--------|-------------|
| **OKTA_PROVISIONER** | Okta を IdP として使用する場合（Snowflake ドキュメントに準拠） |
| **AAD_PROVISIONER** | Microsoft Entra ID / Azure AD を使用する場合（Snowflake ドキュメントに準拠） |
| **GENERIC_SCIM_PROVISIONER** | 他の SCIM 2.0 対応 IdP を使用する場合 |

**推奨事項:**
利用可能な場合は IdP 固有のロール名を使用します。これは Snowflake のドキュメントパターンに従い、設定を明確にします。

**追加情報:**
* [SCIM ロール要件](https://docs.snowflake.com/en/user-guide/scim#step-1-create-a-scim-role-in-snowflake) — 必要なロールのセットアップ
**オプション:**
- OKTA_PROVISIONER
- AAD_PROVISIONER
- GENERIC_SCIM_PROVISIONER

#### SCIM API にアクセスを許可する IP アドレスは何ですか？（`scim_allowed_ips`: list）
**何を聞いているか？**
ID プロバイダーが SCIM プロビジョニングリクエストに使用する IP アドレスまたは CIDR ブロックを提供します。

**なぜ重要か？**
ネットワークポリシーにより SCIM API アクセスが IdP のサーバーのみに制限されます。これにより、権限のないシステムが Snowflake でユーザーを作成または変更することを防ぎます。

**IdP の IP アドレスの見つけ方:**

**Okta:**
- [Okta IP 範囲](https://help.okta.com/en/prod/Content/Topics/Security/ip-address-allow-listing.htm) を参照
- Okta セルの範囲を使用する（セルは `Settings > Account` で確認）

**Microsoft Entra ID (Azure AD):**
- [Azure IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=56519) を参照
- "AzureActiveDirectory" サービスタグを検索
- リージョンでフィルタリング

**その他の IdP:**
- IdP のドキュメントで送信 IP 範囲を確認する
- ドキュメントにない場合は IdP サポートに連絡する

**形式:**
- 個別の IP: `192.168.1.1`
- または CIDR ブロック: `192.168.1.0/24`
- 1 行に 1 エントリを入力

**追加情報:**
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies) — IP ベースのアクセス制御の概要

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
