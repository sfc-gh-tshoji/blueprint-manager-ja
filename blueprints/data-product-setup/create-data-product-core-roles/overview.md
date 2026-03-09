このステップでは、データ製品のコアアカウントレベルの 5 つのロールを作成します:

1. **ADMIN**（`<prefix>_ADMIN`）— 完全な管理制御、データベース・スキーマ・ウェアハウスを所有
2. **CREATE**（`<prefix>_CREATE`）— オブジェクトを作成できる、タスク・マスキング・タグ付けのためのアカウントアクセスロールを受け取る
3. **WRITE**（`<prefix>_WRITE`）— データを変更できる（INSERT、UPDATE、DELETE）
4. **RBAC**（`<prefix>_RBAC`）— データベースアクセスロールを所有、委任アクセスガバナンスを可能にする
5. **READ**（`<prefix>_READ`）— データ製品全体の読み取り専用アクセス

ロールの所有権は SCIM 設定によって決まります:
- **SCIM あり**: ロールは `<scim_prefix>_PROVISIONER` が所有
- **SCIM なし**: ロールは `USERADMIN` が所有

**アカウントコンテキスト:** USERADMIN ロールを使用してターゲットアカウントでこの SQL を実行します。

## なぜこれが重要か？

コアロールが提供するもの:
- **職務の分離**: 異なる責任のための異なるロール
- **委任管理**: データ製品チームが自分のアクセスを管理する
- **最小権限の原則**: ユーザーは必要なものだけを取得する
- **SCIM 互換性**: ロール所有権が ID プロバイダー管理をサポートする

## 前提条件

- USERADMIN ロールでターゲットアカウントにアクセス可能
- データ製品 ID の定義済み（ステップ 1.2）
- SCIM プレフィックスの設定済み（または NONE）
- （オプション）アカウントアクセスロールが存在する（`_AR_EXEC_TASK`、`_AR_APPLY_DDM` など）

## 主要な概念

**ロール階層**

```
SYSADMIN
└── <dataproduct>_ADMIN
    ├── <dataproduct>_CREATE ← _AR_EXEC_TASK, _AR_VIEW_AUSG, _AR_APPLY_*
    └── <dataproduct>_RBAC
```

**ロールの責任**

| ロール | できること | できないこと |
|--------|----------|------------|
| ADMIN | インフラを所有、CREATE/RBAC ロールを管理 | 通常はユーザーに直接割り当てない |
| CREATE | オブジェクトを作成、タスクを実行、ポリシーを適用 | インフラを所有 |
| WRITE | データを変更 | オブジェクトを作成 |
| RBAC | アクセスロールをユーザー/ロールに付与 | データを変更 |
| READ | データをクエリ | 何かを変更 |

**SCIM ロール所有権**

ロール所有権は SCIM 設定によって異なります:
- **SCIM あり**: ロールは SCIM プロビジョナーロール（例: `OKTA_PROVISIONER`）が所有し、ID プロバイダーを通じた自動ユーザー割り当てが可能
- **SCIM なし**: ロールは `USERADMIN` が所有し、Snowflake での手動ユーザー割り当てが必要

**追加情報:**
* [CREATE ROLE](https://docs.snowflake.com/en/sql-reference/sql/create-role) — ロール作成
* [ロール階層](https://docs.snowflake.com/en/user-guide/security-access-control-overview#role-hierarchy-and-privilege-inheritance)
* [SCIM プロビジョニング](https://docs.snowflake.com/en/user-guide/scim)

### 設定の質問

#### このデータ製品の名前は何ですか？（`data_product_name`: text）
データ製品の説明的な名前を提供します。

#### このデータ製品が属するドメインはどれですか？（`data_product_domain`: multi-select）
プラットフォームファウンデーションで定義されたビジネスドメインを選択します。

#### このデータ製品がデプロイされる環境はどれですか？（`data_product_environment`: multi-select）
デプロイメントの SDLC 環境を選択します。

#### SCIM プロビジョナーロールに使用するプレフィックスは何ですか？（`scim_prefix`: text）
- `AAD`、`OKTA`、`PING` などの一般的な SCIM プレフィックス
- SCIM を使用していない場合は `NONE` と入力

#### どのアカウント戦略を実装しますか？（`account_strategy`: multi-select）
**オプション:**
- Single Account
- Multi-Account (Environment-based)
- Multi-Account (Domain-based)
- Multi-Account (Domain + Environment)

#### このデータ製品はどのアカウントにデプロイされますか？（`target_account_name`: text）
Snowflake アカウントの正確な名前を入力します。

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**例:** PLAT\_INFRA

#### ガバナンススキーマに付ける名前は何ですか？（`governance_name`: text）
**推奨名:** GOVERNANCE
