このステップでは、Snowflake アカウントの管理ユーザーに対して多要素認証（MFA）を有効にするためのガイダンスと確認を提供します。

**アカウントコンテキスト:** MFA は組織アカウント（作成済みの場合）またはプライマリアカウントのすべての管理ユーザーに対して有効にする必要があります。

## なぜこれが重要か？

多要素認証（MFA）は、ユーザーがアクセスを取得するために 2 つ以上の確認要素を提供することを要求する重要なセキュリティコントロールです。パスワードが侵害されても、攻撃者は第 2 の要素なしにアカウントにアクセスできません。

Snowflake アカウントにとって:
- **MFA は高権限アカウントを保護する**: ACCOUNTADMIN やその他の管理者ロールは主要なターゲットです
- **コンプライアンス要件**: 多くの規制が特権アクセスに MFA を要求します
- **侵害の影響を軽減**: 資格情報が侵害されただけでは不十分です
- **監査トレイル**: MFA 登録が追跡され、監査可能です

## 外部前提条件

- ユーザーは認証アプリ（Duo、Google Authenticator、Microsoft Authenticator）または FIDO2 セキュリティキーにアクセスできる必要があります
- ユーザーは MFA への登録を求められることを認識しておく必要があります

## 主要な概念

**タイムベースのワンタイムパスワード（TOTP）**
認証アプリによって生成される 30 秒ごとに変わる一時コード。ほとんどの認証アプリでサポートされている標準アプローチ。

**パスキー（FIDO2/WebAuthn）**
ハードウェアセキュリティキーまたはプラットフォーム認証器（指紋、Face ID）を使用するフィッシング耐性の認証方法。最も安全なオプション。

**MFA 登録**
Snowflake に第 2 要素を登録するプロセス。MFA が必要な場合、ユーザーはログイン時に登録を求められます。

**MFA 猶予期間**
Snowflake はポリシーが適用された後の MFA 登録に猶予期間を許可します。ユーザーはこの期間中 MFA なしでログインできます。

**追加情報:**
* [Snowflake の MFA](https://docs.snowflake.com/en/user-guide/security-mfa)
* [MFA への登録](https://docs.snowflake.com/en/user-guide/security-mfa-enroll) — MFA のユーザー登録ガイド
* [FIDO2 パスキー](https://docs.snowflake.com/en/user-guide/security-mfa-passkey) — ハードウェアセキュリティキーの設定

### 設定の質問

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**何を聞いているか？**
組織がユーザー ID の管理に使用する ID プロバイダー（IdP）を選択します。

**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

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

#### 管理者ロールを付与するのは誰ですか？（`scim_admin_users`: object-list）
**何を聞いているか？**
どの SCIM プロビジョニングされたユーザーが管理者ロールを受け取るべきかを定義します。

**推奨事項:**
- ロックアウトシナリオを防ぐために少なくとも **2 名の ACCOUNTADMIN ユーザー**を作成する
- 共有/汎用アカウントではなく個人アカウントを使用する
- ACCOUNTADMIN ユーザーにはロール階層のため SECURITYADMIN と SYSADMIN も付与されます

**追加情報:**
* [ACCOUNTADMIN ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-accountadmin-role)
* [システム定義ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles)

#### ブレークグラス緊急アクセスアカウントを設定する（`breakglass_accounts`: object-list）
**何を聞いているか？**
1 つ以上のブレークグラス緊急アクセスアカウントを定義します。各アカウントは ID プロバイダーが利用できない場合に SSO をバイパスしてパスワードで認証できます。

**アカウント数:**
- **最低 1**: 少なくとも 1 つのブレークグラスアカウントが必要
- **推奨 2**: 1 つが侵害された場合の冗長性を提供
- **最大 3**: 3 つ以上は攻撃面を増やします

**ユーザー名:**
- 説明的な名前を使用: `BREAKGLASS_ADMIN`、`EMERGENCY_ACCESS`、`SOS_ADMIN`
- これは共有緊急アカウントなので個人名は避ける
- 複数のアカウントの場合はサフィックスを使用: `BREAKGLASS_ADMIN_01`、`BREAKGLASS_ADMIN_02`
- **重要:** ユーザー名としてメールアドレスを使用しないでください。ブレークグラスアカウントは SSO が利用できない場合に動作する必要があるため、`@` や `.` 文字のないシンプルな識別子を使用してください。

**メール:**
- 個人メールではなくチームの配布リストを使用する
- 例: `snowflake-security@company.com`、`platform-team@company.com`
- 重要なワークロードがある場合は、メールが 24 時間 365 日監視されていることを確認する

**許可される IP:**
- カンマ区切りの IP アドレスまたは CIDR 範囲を入力する
- 例: `10.0.0.1, 192.168.1.0/24, 172.16.0.0/16`
- VPN 出口 IP、オフィス IP、バックアップロケーションを含める
- 任意の IP からのアクセスを許可するには `NONE` を入力する（推奨しない）

**入力例:**

| username | email | allowed_ips |
|----------|-------|-------------|
| `BREAKGLASS_ADMIN_01` | `platform-team@company.com` | `10.0.0.1, 192.168.1.0/24` |
| `BREAKGLASS_ADMIN_02` | `security-team@company.com` | `10.0.0.2, 192.168.1.0/24` |

**セキュリティノート:**
- すべてのアカウントは `MUST_CHANGE_PASSWORD = TRUE` で作成されます
- 初期パスワードは初回ログイン時に変更する必要があります
- セキュアなボルトに保存されたワンタイムパスワード（OTP）を使用する
- Web UI のみに制限する（ドライバー/CLI アクセスなし）

**追加情報:**
* [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user)
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies)

#### 管理ユーザーに対して MFA はいつ適用されますか？（`mfa_enforcement_timeline`: multi-select）
**何を聞いているか？**
パスワードでログインするユーザーに MFA が必要なタイミングを選択します。

**なぜ重要か？**
ユーザーは認証アプリをダウンロードして MFA に登録する時間が必要です。タイムラインが短すぎるとユーザーがロックアウトされる可能性があり、長すぎるとセキュリティが低下します。

**オプションの説明:**
- **即時**: ユーザーは次のログイン時に MFA に登録する必要があります。セキュリティに最適ですが、アクセスの問題が生じる可能性があります。
- **7 日間**: 1 週間の猶予期間。ほとんどの組織に推奨。
- **14 日間**: 2 週間の猶予期間。分散チームに適しています。
- **30 日間**: 1 ヶ月の猶予期間。複雑なユーザーコミュニケーション要件がある場合のみ使用。

**推奨事項:**
**7 日間**はセキュリティとユーザーエクスペリエンスの良いバランスを提供します。このステップを実行した直後にユーザーにコミュニケーションを送信してください。

**追加情報:**
* [MFA 登録](https://docs.snowflake.com/en/user-guide/security-mfa-enroll)
**オプション:**
- Immediately - Require MFA now
- 7 Days - Allow one week for enrollment
- 14 Days - Allow two weeks for enrollment
- 30 Days - Allow one month for enrollment
