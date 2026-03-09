このステップでは、Snowflake 環境を管理する初期アカウント管理者を作成します。手動でユーザーを管理する（SCIM なし）ことを選択したため、これらのユーザーは Snowflake に直接作成されます。

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

- 指定された管理者のメールアドレス
- 管理者は会社のメールアドレスを持っている必要があります（個人メール不可）

## 主要な概念

**ACCOUNTADMIN ロール**
Snowflake で最も権限が高いシステム定義ロール。2〜3 名の信頼できる個人に限定すべきです。

**SECURITYADMIN ロール**
アカウント内のすべてのオブジェクトへの付与を管理できます。ACCOUNTADMIN の代わりに日常のセキュリティ管理に使用します。

**SYSADMIN ロール**
データベース、ウェアハウス、その他のオブジェクトを作成できます。日常のインフラ管理に使用します。

**USERADMIN ロール**
ユーザーとロールを作成・管理できます。運用上のユーザー管理に委任されることがよくあります。

**ユーザータイプ**
- `PERSON`: インタラクティブにログインする人間のユーザー
- `SERVICE`: 自動化されたプロセスと統合
- `LEGACY_SERVICE`: 下位互換性のあるサービスアカウント

## 追加情報

* [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user) — ユーザー作成の SQL コマンドリファレンス
* [システム定義ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles) — 組み込み管理者ロールの概要
* [アクセスコントロール権限](https://docs.snowflake.com/en/user-guide/security-access-control-privileges) — 詳細な権限リファレンス

### 設定の質問

#### 管理者として設定するのは誰ですか？（`manual_admin_users`: object-list）
**何を聞いているか？**
Snowflake アカウントを管理する管理者を定義します。各管理者に対して、詳細を提供し管理者ロールを指定します。

**SSO 対応の推奨事項: メールをユーザー名として使用する**
現在 SSO を使用していない場合でも、ユーザーの**メールアドレス**を `username` として使用することを強くお勧めします。メリット:
- **SSO 対応:** ほとんどの ID プロバイダー（Okta、Azure AD など）はデフォルト識別子としてメールを使用します。今メールを使用することで、後のシームレスな SSO 統合が確保されます。
- **一意性:** メールアドレスはグローバルに一意で、命名の競合を防ぎます。
- **一貫性:** ユーザーはすべてのシステムで同じ識別子でログインします。

**管理者ロール（admin_role フィールド）**

次のいずれかの値を正確に入力してください:

| 入力する値 | 目的 | 推奨人数 |
|------------|------|----------|
| `ACCOUNTADMIN` | 完全なアカウントコントロール - 最も権限の高いロール | 2〜3 名のみ |
| `SECURITYADMIN` | セキュリティ、付与、アクセスコントロールの管理 | 2〜5 名 |
| `SYSADMIN` | データベース、ウェアハウス、インフラの管理 | 3〜10 名 |
| `USERADMIN` | ユーザーとカスタムロールの管理 | 2〜5 名 |

**重要:** `admin_role` フィールドは上記通りに正確に入力する必要があります（大文字小文字は区別しませんが、一貫性のために大文字を使用してください）。

**入力例（SSO 対応）:**

| username | email | first_name | last_name | admin_role |
|----------|-------|------------|-----------|------------|
| `john.smith@company.com` | `john.smith@company.com` | `John` | `Smith` | `ACCOUNTADMIN` |
| `jane.doe@company.com` | `jane.doe@company.com` | `Jane` | `Doe` | `ACCOUNTADMIN` |
| `bob.wilson@company.com` | `bob.wilson@company.com` | `Bob` | `Wilson` | `SYSADMIN` |

**推奨事項:**
- ロックアウトシナリオを防ぐために少なくとも **2 名の ACCOUNTADMIN ユーザー**を作成する
- 共有/汎用アカウントではなく個人アカウントを使用する
- SSO 対応のためにメールアドレスをユーザー名として使用する
- 個人メールではなく会社メールアドレスを使用する

**セキュリティノート:**
- すべてのユーザーは `MUST_CHANGE_PASSWORD = TRUE` で作成されます
- ユーザーは初回ログイン時に変更する必要がある初期パスワードを受け取ります
- 初期設定後に MFA の有効化を検討する（多要素認証の有効化ステップ）

**追加情報:**
* [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user)
* [ACCOUNTADMIN ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-accountadmin-role)

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**何を聞いているか？**
組織がユーザー ID の管理に使用する ID プロバイダー（IdP）を選択します。

**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management
