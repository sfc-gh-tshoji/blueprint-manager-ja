このステップでは、Snowflake でユーザーをどのように管理・認証するかを選択します。この基本的な決定が、その後の設定ステップを決定します。

**アカウントコンテキスト:** このステップでは、組織アカウント（作成済みの場合）またはプライマリアカウントの ID 管理を設定します。

## なぜこれが重要か？

ID 管理アプローチは、Snowflake プラットフォーム全体のセキュリティ、管理オーバーヘッド、ユーザーエクスペリエンスに影響します。ここでの選択が次のことを決定します:
- ユーザーの作成と削除方法
- ユーザーの認証方法（SSO vs パスワード）
- 必要な手動管理の量

## 外部前提条件

- 組織の ID 管理戦略を理解する
- 組織が使用している ID プロバイダー（IdP）を把握する（ある場合）
- SAML/SSO が認証戦略の一部になるかどうかを決める

## 主要な概念

**SCIM（System for Cross-domain Identity Management）**
自動化されたユーザープロビジョニングを可能にするオープンスタンダードプロトコル。SCIM は ID プロバイダーと Snowflake 間の「同期ケーブル」と考えてください。IdP でユーザーが追加、変更、または削除されると、その変更が自動的に Snowflake に反映されます。

**ID プロバイダー（IdP）**
組織内のユーザー ID の権威あるソース（例: Okta、Azure AD、Ping Identity）。IdP は組織の従業員を把握する「唯一の信頼できる情報源」です。

**SAML/SSO（シングルサインオン）**
ユーザーが ID プロバイダーの資格情報を使用して Snowflake に認証できるようにし、別の Snowflake パスワードなしでシームレスなログインエクスペリエンスを提供します。

**手動ユーザー管理**
IdP 統合なしに Snowflake ユーザーを直接作成・管理します。ユーザーは Snowflake のユーザー名とパスワード（+ MFA）で認証します。

**追加情報:**
* [SCIM 概要](https://docs.snowflake.com/en/user-guide/scim) — Snowflake における SCIM プロビジョニングの概要
* [SAML/SSO 概要](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth) — フェデレーテッド認証オプション

### 設定の質問

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**何を聞いているか？**
組織がユーザー ID の管理に使用する ID プロバイダー（IdP）を選択します。この IdP が Snowflake へのユーザープロビジョニングの信頼できる情報源になります。

**なぜ重要か？**
IdP によって設定手順と機能が異なります。Snowflake は Okta や Azure AD などの主要 IdP に特定のドキュメントを提供し、その他の SCIM 2.0 対応プロバイダーには汎用設定を使用します。

**オプションの説明:**
- **Okta**: ネイティブ Snowflake SCIM 統合を持つエンタープライズ IdP
- **Microsoft Entra ID (Azure AD)**: Snowflake のギャラリーアプリを持つ Microsoft のクラウド ID サービス
- **その他の SCIM 2.0 対応 IdP**: SCIM 2.0 プロトコルをサポートする任意の IdP
- **なし - 手動ユーザー管理**: SCIM をスキップして手動でユーザーを管理する（非推奨）

**推奨事項:**
組織にエンタープライズ IdP がある場合、SCIM 統合の設定を強くお勧めします。初期設定の工数は、自動プロビジョニングの継続的なメリットと比べて最小限です。

**追加情報:**
* [SCIM 概要](https://docs.snowflake.com/en/user-guide/scim)
* [対応 ID プロバイダー](https://docs.snowflake.com/en/user-guide/scim#supported-identity-providers)
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### シングルサインオン用に SAML/SSO を設定しますか？（`configure_saml`: multi-select）
**何を聞いているか？**
このセットアップの一部として SAML ベースのシングルサインオン（SSO）を設定するか、後回しにするかを決定します。

**なぜ重要か？**
SAML/SSO を使用すると、ユーザーは ID プロバイダーを使用して Snowflake に認証でき、シームレスなログインエクスペリエンスと集中認証コントロールを提供します。

**オプションの説明:**

**はい - 今すぐ SAML を設定する:**
- 専用のステップが SAML 設定を案内します
- IdP の準備ができていて、初日から SSO を使いたい場合に推奨

**いいえ - 後で設定する:**
- 現在は SAML 設定をスキップ
- ユーザーはユーザー名/パスワード + MFA で認証します
- 後で SAML を設定できます（再構築不要）

**「後で設定する」を選ぶ場合:**
- IdP がまだ完全に設定されていない
- まず基本を動かしたい
- ID チームと調整が必要
- 概念実証をしている

**注記:** SAML なしでも、パスワード + MFA は強力な認証を提供します。SAML は利便性と集中管理を追加しますが、必ずしもセキュリティが高まるわけではありません。

**推奨事項:**
SCIM プロバイダーを選択して IdP の準備ができている場合、完全な SSO エクスペリエンスのために今すぐ SAML を設定します。そうでない場合は、後で設定します。

**追加情報:**
* [SAML/SSO 設定](https://docs.snowflake.com/en/user-guide/admin-security-fed-auth) — フェデレーテッド認証のセットアップ
**オプション:**
- Yes - Configure SAML now
- No - Configure later or use password authentication
