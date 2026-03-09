このステップでは、Snowflake 環境を管理する初期アカウント管理者を定義します。これらのユーザーは ID プロバイダーから SCIM を通じてプロビジョニングされ、適切な管理者ロールが付与されます。

**アカウントコンテキスト:** これらの管理者は、組織アカウント（作成済みの場合）またはプライマリアカウント向けに設定されています。マルチアカウント戦略がある場合、追加のアカウントには別途管理者を設定する必要があります。

## なぜこれが重要か？

アカウント管理者（ACCOUNTADMIN ロール）は Snowflake アカウントで最高レベルの権限を持ちます。以下が可能です:
- すべてのオブジェクトの作成と管理
- 任意のロールへの権限付与
- アカウントレベルの設定管理
- すべてのデータへのアクセス

初期管理者を適切に設定することで次のことが確保されます:
- **冗長性**: 複数の管理者がロックアウトシナリオを防ぎます
- **説明責任**: 具体的な個人が管理アクションに責任を持ちます
- **セキュリティ**: 管理者数を制限することでリスクを軽減します

## 外部前提条件

- 前のステップで SCIM 統合を設定済み
- ID プロバイダーの Snowflake アプリケーションにユーザーが割り当て済み
- SCIM 経由で Snowflake にユーザーがプロビジョニング済み

## 主要な概念

**ACCOUNTADMIN ロール**
Snowflake で最も権限が高いシステム定義ロール。2〜3 名の信頼できる個人に限定すべきです。

**SECURITYADMIN ロール**
アカウント内のすべてのオブジェクトへの付与を管理できます。ACCOUNTADMIN の代わりに日常のセキュリティ管理に使用します。

**SYSADMIN ロール**
データベース、ウェアハウス、その他のオブジェクトを作成できます。日常のインフラ管理に使用します。

**USERADMIN ロール**
ユーザーとロールを作成・管理できます。運用上のユーザー管理に委任されることがよくあります。

## 追加情報

* [システム定義ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles) — 組み込み管理者ロールの概要
* [アクセスコントロール権限](https://docs.snowflake.com/en/user-guide/security-access-control-privileges) — 詳細な権限リファレンス

### 設定の質問

#### 管理者ロールを付与するのは誰ですか？（`scim_admin_users`: object-list）
**何を聞いているか？**
どの SCIM プロビジョニングされたユーザーが管理者ロールを受け取るべきかを定義します。各管理者に対して、ログイン名と付与するロールを提供します。

**ログイン名の形式**

ログイン名は ID プロバイダーから SCIM 経由でユーザーがプロビジョニングされた方法と完全に一致する必要があります:
- **最も一般的:** メールアドレス（例: `john.smith@company.com`）- Okta、Azure AD のデフォルト
- **代替:** ユーザー名形式（例: `john.smith`）- IdP が異なる設定の場合

**ヒント:** Snowflake で `SHOW USERS;` を実行して、IdP が使用する正確な `LOGIN_NAME` 形式を確認してください。

**管理者ロール（admin_role フィールド）**

次のいずれかの値を正確に入力してください:

| 入力する値 | 目的 | 推奨人数 |
|------------|------|----------|
| `ACCOUNTADMIN` | 完全なアカウントコントロール - 最も権限の高いロール | 2〜3 名のみ |
| `SECURITYADMIN` | セキュリティ、付与、アクセスコントロールの管理 | 2〜5 名 |
| `SYSADMIN` | データベース、ウェアハウス、インフラの管理 | 3〜10 名 |
| `USERADMIN` | ユーザーとカスタムロールの管理 | 2〜5 名 |

**⚠️ 重要:** `admin_role` フィールドは上記通りに正確に入力する必要があります（大文字小文字は区別しませんが、一貫性のために大文字を使用してください）。

**入力例:**

| login_name | admin_role |
|------------|------------|
| `john.smith@company.com` | `ACCOUNTADMIN` |
| `jane.doe@company.com` | `ACCOUNTADMIN` |
| `bob.wilson@company.com` | `SYSADMIN` |
| `alice.chen@company.com` | `SECURITYADMIN` |

**推奨事項:**
- ロックアウトシナリオを防ぐために少なくとも **2 名の ACCOUNTADMIN ユーザー**を作成する
- 共有/汎用アカウントではなく個人アカウントを使用する
- ACCOUNTADMIN ユーザーにはロール階層のため SECURITYADMIN と SYSADMIN も付与されます

**SCIM プロビジョニングの注意:**
ロールを付与する前に、ユーザーが SCIM 経由でプロビジョニングされている必要があります。`SHOW USERS;` にユーザーが表示された**後**に SQL を実行してください。

**追加情報:**
* [ACCOUNTADMIN ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-accountadmin-role)
* [システム定義ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles)

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
組織にエンタープライズ IdP がある場合、SCIM 統合の設定を強くお勧めします。

**追加情報:**
* [SCIM 概要](https://docs.snowflake.com/en/user-guide/scim)
* [対応 ID プロバイダー](https://docs.snowflake.com/en/user-guide/scim#supported-identity-providers)
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management
