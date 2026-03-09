このステップでは、このアカウントの管理者ユーザーを確立します。ID 管理アプローチに応じて、SCIM でプロビジョニングされたユーザーに管理者ロールを割り当てるか、管理者権限を持つローカルユーザーを作成します。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## なぜこれが重要か？

管理者ユーザーはアカウントガバナンスのために重要です:
- **ACCOUNTADMIN**: アカウントの完全制御（控えめに使用）
- **SECURITYADMIN**: ユーザー、ロール、セキュリティポリシーを管理
- **SYSADMIN**: データベース、ウェアハウス、その他のリソースを管理
- **USERADMIN**: ユーザーを作成・管理（多くの場合 SCIM に委任）

複数の管理者がいることで、1 人が不在の場合でも継続性が確保されます。

## 外部の前提条件

- SCIM 統合の設定済み（SCIM を使用する場合）
- このアカウントへの管理者アクセスが必要なユーザーのリスト
- 各ユーザーに必要な管理者ロールの理解

## 主要な概念

**最小権限の原則**
必要最小限のロールを付与します:
- ほとんどのユーザーはリソース管理のために SYSADMIN のみが必要
- アクセス制御を管理する人には SECURITYADMIN
- ACCOUNTADMIN は 2〜3 人のシニア管理者に限定する

**ロール階層**
ACCOUNTADMIN は SECURITYADMIN と SYSADMIN から継承します。ACCOUNTADMIN を持つユーザーはすべての管理機能を実行できます。

**追加情報:**
* [アクセス制御の概要](https://docs.snowflake.com/en/user-guide/security-access-control-overview) — ロール階層と権限
* [システムロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles) — 組み込みロールの説明

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認またはカスタマイズします。

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management

#### このアカウントへの管理者アクセスを持つべき人は誰ですか？（`account_admin_users`: object-list）
このアカウントで管理者権限が必要なユーザーを定義します。

**SSO 対応の推奨事項: ログイン名としてメールアドレスを使用する**
SSO を現在使用していない場合でも、`login_name` としてユーザーの**メールアドレス**を使用することを強くお勧めします。

**管理者ロールオプション:**
| ロール | 責任 | 推奨対象 |
|--------|------|---------|
| `ACCOUNTADMIN` | アカウントの完全制御 | 2〜3 人のシニアプラットフォーム/セキュリティリード |
| `SECURITYADMIN` | ユーザーとロール管理 | セキュリティチームメンバー |
| `SYSADMIN` | リソース管理 | プラットフォームエンジニア、DBA |
| `USERADMIN` | ユーザーの作成/変更 | HR 統合（SCIM を使用しない場合） |

**ベストプラクティス:**
- ACCOUNTADMIN を 2〜3 人の信頼できる個人に限定する
- 各人が自分のアカウントを使用する（共有アカウントは不可）
- 冗長性のために少なくとも 2 人が ACCOUNTADMIN を持つようにする
