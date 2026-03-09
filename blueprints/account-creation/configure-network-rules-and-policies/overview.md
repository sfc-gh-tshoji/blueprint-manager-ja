このステップでは、Snowflake アカウントにアクセスできる IP アドレスとネットワークを制御するネットワークルールとポリシーを設定します。

**アカウントコンテキスト:** これらのネットワークポリシーは組織アカウント（作成済みの場合）またはプライマリアカウントに適用されます。ネットワークルールはインフラデータベースのガバナンススキーマに保存されます。

## なぜこれが重要か？

ネットワークポリシーは、ネットワークの場所に基づいて Snowflake アカウントへのアクセスを制限する重要なセキュリティコントロールです。ネットワークポリシーなしでは、有効な認証情報を持つ誰でも世界中どこからでもアカウントにアクセスできます。

## 外部の前提条件

- 企業ネットワーク IP 範囲のリスト（オフィス、VPN など）
- アクセスが必要なクラウドサービス IP のリスト（ETL ツール、BI ツール）
- 組織のネットワークトポロジーの理解

## 主要な概念

**ネットワークルール**
IP アドレスまたは CIDR 範囲のリストを定義する Snowflake オブジェクト。

**ネットワークポリシー**
ネットワークルールをホワイトリスト/ブラックリストに組み合わせる Snowflake オブジェクト。

**追加情報:**
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies)
* [ネットワークルール](https://docs.snowflake.com/en/user-guide/network-rules)

### 設定の質問

#### プラットフォームデータベースの名前は何にしますか？（`platform_database_name`: text）
インフラデータベースの名前（例: PLAT\_INFRA）。

#### ガバナンススキーマの名前は何にしますか？（`governance_name`: text）
**推奨名:** GOVERNANCE

#### 許可 IP アドレスのネットワークルールを定義してください。（`allowed_network_rules`: object-list）
Snowflake への接続を許可される IP アドレスを含む 1 つ以上のネットワークルールを作成します。

**一般的なルールの例:**
| ルール名 | CIDR ブロック | 目的 |
|---------|------------|------|
| `corporate_vpn` | `203.0.113.0/24` | 企業 VPN とオフィス IP |
| `fivetran` | `52.0.2.4` | Fivetran ETL サービス |

#### ブロックされた IP アドレスのネットワークルールを定義してください。（`blocked_network_rules`: object-list）
（オプション）Snowflake へのアクセスを明示的にブロックする IP アドレスのネットワークルールを作成します。

#### ネットワークポリシーをアカウントレベルで適用しますか？（`enable_account_network_policy`: multi-select）
**オプション:**
- Yes - Apply to all users by default（すべてのユーザーにデフォルトで適用する）
- No - Apply only to specific users（特定のユーザーのみに適用する）

**推奨:** 初期設定とテスト中は「No」から始め、必要なすべての IP が含まれていることを検証したら「Yes」に移行します。

#### SCIM 統合にどの ID プロバイダーを使用しますか？（`identity_provider`: multi-select）
**オプション:**
- Okta
- Microsoft Entra ID (Azure ID)
- Other SCIM 2.0 Compatible IdP
- None - Manual User Management
