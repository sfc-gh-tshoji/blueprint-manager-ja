このステップでは、各ウェアハウスの使用を制御するウェアハウスアクセスロールを作成します。最小権限の原則に従い、アクセスロールはウェアハウス自体とは分離されています。

各ウェアハウスに対して、以下を付与する `_WH_U_<name>`（ウェアハウス USAGE）ロールが作成されます:
- `USAGE` — クエリにウェアハウスを使用する機能
- `MONITOR` — ウェアハウスアクティビティを監視する機能
- `OPERATE` — ウェアハウスを起動/停止する機能

**アカウントコンテキスト:** ADMIN ロールを使用してターゲットアカウントでこの SQL を実行します。

## なぜこれが重要か？

ウェアハウスアクセスロールを分離することで以下が可能になります:
- **柔軟な割り当て**: データアクセスとは独立してウェアハウスアクセスを付与
- **コスト制御**: ウェアハウスアクセスを制限してコンピューティング支出を管理
- **監査トレイル**: どのロールがどのウェアハウスを使用できるかを明確に把握
- **最小権限**: ユーザーは必要なウェアハウスのみにアクセス可能

## 前提条件

- ウェアハウスの作成済み（ステップ 3.1）
- コアロールの作成済み（ステップ 2.1）

## 主要な概念

**アクセスロールの命名規則**

```
<prefix>_WH_U_<warehouse_name>
        └─────────┬─────────┘
           ウェアハウス USAGE
```

**権限の内訳**

| 権限 | 説明 |
|------|------|
| `USAGE` | ウェアハウスを使用してクエリを実行 |
| `MONITOR` | ウェアハウスのメトリクスとクエリ履歴を表示 |
| `OPERATE` | ウェアハウスの再開、一時停止、クエリの中止 |

**ロールの所有権**
- アクセスロールは RBAC ロールによって所有される
- RBAC ロールはこれらを他のロールに付与できる
- 委任管理を可能にする

**追加情報:**
* [ウェアハウス権限](https://docs.snowflake.com/en/user-guide/security-access-control-privileges#warehouse-privileges)
* [GRANT PRIVILEGE](https://docs.snowflake.com/en/sql-reference/sql/grant-privilege)


### 設定の質問

#### このデータ製品の名前は何ですか？（`data_product_name`: text）
データ製品の説明的な名前を提供します。

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)

#### このデータ製品はどのアカウントにデプロイされますか？（`target_account_name`: text）
Snowflake アカウントの正確な名前を入力します。

#### このデータ製品が属するドメインはどれですか？（`data_product_domain`: multi-select）
プラットフォームファウンデーションで定義されたビジネスドメインを選択します。

#### このデータ製品がデプロイされる環境はどれですか？（`data_product_environment`: multi-select）
デプロイメントの SDLC 環境を選択します。

#### データ製品のウェアハウスを定義してください。（`warehouse_configuration`: object-list）
コンピューティングウェアハウスを定義します（名前、サイズ、最小/最大クラスター、自動一時停止）。
