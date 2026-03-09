このステップでは、Snowflake アカウントにアクセスできる IP アドレスとネットワークを制御するネットワークルールとポリシーを設定します。

**アカウントコンテキスト:** これらのネットワークポリシーは組織アカウント（作成済みの場合）またはプライマリアカウントに適用されます。ネットワークルールはインフラデータベースのガバナンススキーマに保存されます。

## なぜこれが重要か？

ネットワークポリシーは、ネットワークの場所に基づいて Snowflake アカウントへのアクセスを制限する重要なセキュリティコントロールです。ネットワークポリシーなしでは、有効な資格情報を持つ誰でも世界中どこからでもアカウントにアクセスできます。

ネットワークポリシーが提供するもの:
- **IP ホワイトリスト**: 信頼できるネットワークからの接続のみを許可する
- **多層防御**: 認証を超えた追加の層
- **コンプライアンス**: ネットワークアクセス制御の規制要件を満たす
- **攻撃面の削減**: 資格情報ベースの攻撃への露出を制限する
- **地理的制限**: 予期しない場所からのアクセスを防ぐ

## 外部前提条件

- 会社のネットワーク IP 範囲のリスト（オフィス、VPN など）
- アクセスが必要なクラウドサービス IP のリスト（ETL ツール、BI ツール）
- 組織のネットワークトポロジーの理解

## 主要な概念

**ネットワークルール**
IP アドレスまたは CIDR 範囲のリストを定義する Snowflake オブジェクト。ネットワークルールを、どこから誰が入れるかを定義する「ゲストリスト」と考えてください。

**ネットワークポリシー**
ネットワークルールを許可/ブロックリストに組み合わせる Snowflake オブジェクト。これはアクセスを許可する前にゲストリストに対して IP アドレスをチェックする「バウンサー」です。

**アカウントレベルポリシー**
アカウント全体に適用されるネットワークポリシー — デフォルトですべての人に適用される「玄関のセキュリティ」。

**ユーザーレベルポリシー**
特定のユーザーに適用されるネットワークポリシー。これは特定のユーザーのために標準の玄関ルールを上書きする「VIP 入口」のようなものです。

**ベストプラクティス: 多層防御**
ネットワークポリシーは防御の最前線です — 資格情報が盗まれても、攻撃者は権限のないネットワークから接続できません。

**追加情報:**
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies) — IP ベースのアクセス制御の概要
* [ネットワークルール](https://docs.snowflake.com/en/user-guide/network-rules) — ネットワークルールの作成と管理
* [CREATE NETWORK RULE](https://docs.snowflake.com/en/sql-reference/sql/create-network-rule) — SQL コマンドリファレンス

### 設定の質問

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**例:** PLAT\_INFRA
**追加情報:**
* [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database)
* [Object Identifiers](https://docs.snowflake.com/en/sql-reference/identifiers)

#### ガバナンススキーマに付ける名前は何ですか？（`governance_name`: text）
**推奨名:** GOVERNANCE
**追加情報:**
* [CREATE SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/create-schema)
* [マネージドアクセススキーマ](https://docs.snowflake.com/en/user-guide/security-access-control-overview#managed-access-schemas)

#### 許可された IP アドレスのネットワークルールを定義する（`allowed_network_rules`: object-list）
**何を聞いているか？**
1 つ以上のネットワークルールを作成します。各ルールには説明的な名前と、Snowflake への接続を許可する IP アドレスまたは CIDR 範囲のリストがあります。

**なぜ重要か？**
ネットワークルールは Snowflake アカウントにアクセスできる IP アドレスを定義します。関連する IP を名前付きルールにグループ化することで管理と監査が簡単になります。

**フィールド:**
- **rule_name**: ルールの説明的な名前（例: `corporate_vpn`、`fivetran`、`tableau_cloud`）
- **cidr_blocks**: カンマ区切りの IP アドレスまたは CIDR 範囲（例: `192.168.1.0/24, 10.0.0.1`）

**一般的なルールの例:**
| ルール名 | CIDR ブロック | 目的 |
|----------|--------------|------|
| `corporate_vpn` | `203.0.113.0/24, 198.51.100.50` | 会社の VPN とオフィス IP |
| `fivetran` | `52.0.2.4, 34.75.100.0/24` | Fivetran ETL サービス |
| `tableau_cloud` | `44.192.0.0/16` | Tableau Cloud BI |
| `aws_lambda` | `3.5.0.0/16` | AWS Lambda 関数 |

**IP 範囲の見つけ方:**
- 会社のネットワーク: IT/ネットワークチームに確認するか `curl ifconfig.me` を使用
- クラウドサービス: 公開 IP 範囲のプロバイダーのドキュメントを確認
- 多くのサービスが追加できる静的 IP リストを公開しています

**命名規則:**
- 小文字とアンダースコアを使用: `corporate_vpn`、`dbt_cloud`
- 説明的にする: `office` の代わりに `ny_office`
- サードパーティツールにはサービス名を含める

**追加情報:**
* [ネットワークルール](https://docs.snowflake.com/en/user-guide/network-rules)
* [CREATE NETWORK RULE](https://docs.snowflake.com/en/sql-reference/sql/create-network-rule)

#### ブロックされた IP アドレスのネットワークルールを定義する（`blocked_network_rules`: object-list）
**何を聞いているか？**
オプションで、Snowflake へのアクセスを明示的にブロックする IP アドレスのネットワークルールを作成します。

**なぜ重要か？**
ブロックルールは許可ルールより優先されます。これは、許可された範囲内の特定の IP をブロックするのに便利です。

**フィールド:**
- **rule_name**: ブロックルールの説明的な名前（例: `blocked_regions`、`former_vendor`）
- **cidr_blocks**: ブロックする IP アドレスまたは CIDR 範囲のカンマ区切りリスト

**ブロックルールの例:**
| ルール名 | CIDR ブロック | 目的 |
|----------|--------------|------|
| `blocked_countries` | `185.0.0.0/8, 91.0.0.0/8` | 特定のリージョンからのトラフィックをブロック |
| `former_vendor` | `203.0.113.100, 203.0.113.101` | 元ベンダーの静的 IP をブロック |
| `known_malicious` | `192.0.2.0/24` | 既知の悪意ある IP 範囲 |
| `excluded_subnet` | `10.0.5.0/24` | 広い許可から特定のサブネットを除外 |

**使用ケース:**
- 既知の悪意ある IP 範囲のブロック
- 許可された CIDR 範囲内の特定のホストのブロック
- 元のベンダーやパートナーの IP のブロック
- 地理的制限（国全体の IP 範囲のブロック）

**明示的に IP をブロックする必要がない場合は空のままにします。**ほとんどの組織は許可ルールのみを使用します。

**追加情報:**
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies)

#### ネットワークポリシーをアカウントレベルで適用しますか？（`enable_account_network_policy`: multi-select）
**何を聞いているか？**
デフォルトでアカウント内のすべてのユーザーにネットワークポリシーを適用するか、特定のユーザーのみに適用するかを決定します。

**なぜ重要か？**
- **はい（アカウントレベル）**: すべてのユーザーは許可されたネットワークから接続する必要があります。より安全ですが、事前に完全な IP リストが必要です。
- **いいえ（ユーザーレベルのみ）**: ネットワークポリシーは明示的に割り当てたユーザーにのみ適用されます。展開時により柔軟です。

**推奨事項:**
- 初期設定とテスト中は**いいえ**から始める
- 必要なすべての IP が含まれていることを確認したら**はい**に移行する
- ブレークグラスアカウントがポリシーをバイパスできることを常に確認する

**注意:**
必要なすべての IP を含めずにアカウントレベルのポリシーを有効にすると、自分自身をロックアウトする可能性があります！

**追加情報:**
* [ネットワークポリシーの有効化](https://docs.snowflake.com/en/user-guide/network-policies#activating-a-network-policy)
**オプション:**
- Yes - Apply to all users by default
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
