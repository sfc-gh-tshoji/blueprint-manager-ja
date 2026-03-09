このステップでは、このアカウントのカスタムネットワークルールとポリシーを設定します。これにより、組織アカウントの標準設定とは異なるネットワークアクセス制御を定義できます。

**アカウントコンテキスト:** このステップは新しく作成したアカウントから実行します。

## なぜこれが重要か？

ネットワークポリシーは Snowflake アカウントの「門番」として機能します:
- **信頼できる IP のみを許可**: 企業ネットワーク、VPN、既知のデータセンター
- **疑わしいソースをブロック**: 不明な場所からのアクセスを防ぐ
- **多層防御**: 認証情報が侵害されても、不正な IP は接続できない

## 外部の前提条件

- このアカウントにアクセスする IP アドレス/範囲のリスト
- 企業ネットワーク、VPN、データセンター IP の把握
- アクセスが必要なクラウドサービス IP（ETL ツール、BI プラットフォーム）の理解

## 主要な概念

**許可 vs ブロック**
- **許可ネットワークルール**: これらの IP のみが Snowflake にアクセスできる
- **ブロックネットワークルール**: これらの IP は許可ルールに一致していても明示的に拒否される

**追加情報:**
* [ネットワークポリシー](https://docs.snowflake.com/en/user-guide/network-policies)
* [ネットワークルール](https://docs.snowflake.com/en/sql-reference/sql/create-network-rule)

### 設定の質問

#### このアカウントに使用する名前は何ですか？（`new_account_name`: text）
アカウント名を確認します。

#### このアカウントの許可ネットワークルールを定義してください。（`account_allowed_network_rules`: object-list）
この Snowflake アカウントへのアクセスを許可される IP アドレスを定義します。

**形式:**
- **rule_name**: 説明的な名前（例: `corporate_network`、`vpn_endpoints`）
- **ip_addresses**: IP またはCIDR ブロックのカンマ区切りリスト

#### このアカウントのブロックネットワークルールを定義してください。（`account_blocked_network_rules`: object-list）
許可される範囲内であっても、明示的にブロックされる IP アドレスを定義します。

#### このアカウントのセキュリティをどのように設定しますか？（`security_config_approach`: multi-select）
**オプション:**
- Use Organization Configuration（組織設定を使用）
- Configure Custom Settings（カスタム設定を作成）
