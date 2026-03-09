このステップでは、タスク 1 で定義した各ゾーンのデータベースを作成します。各データベースには以下が含まれます:

1. **データベース** — ゾーンごとに 1 つ（RAW、CURATED、PUB など）
2. **データベースロール** — アクセス制御のためのデータベースごとに 3 つのロール:
   - `DB_R` — すべてのスキーマへの読み取りアクセス
   - `DB_W` — すべてのスキーマへの書き込みアクセス
   - `DB_C` — すべてのスキーマへの作成アクセス

データベースは SYSADMIN によって作成され、委任管理のためにデータ製品の ADMIN ロールに所有権が移転されます。

**アカウントコンテキスト:** SYSADMIN ロールを使用してターゲットアカウントでこの SQL を実行します。

## なぜこれが重要か？

ゾーンデータベースが提供するもの:
- **データの組織化**: 生データ、キュレート済み、公開済みデータの分離
- **アクセス制御**: 異なるゾーンが異なるアクセスポリシーを持てる
- **パフォーマンス**: 独立したスケーリングとリソース管理
- **コスト帰属**: ゾーン別のストレージコストを追跡

## 前提条件

- コアロールが作成済み（ステップ 2.1）
- SYSADMIN ロールでターゲットアカウントにアクセス可能
- ゾーン構造の定義済み（ステップ 1.3）

## 主要な概念

**データベースロール**

データベースロールはデータベース内に存在するポータブルなアクセスパターンです:

| ロール | 目的 | 付与内容 |
|--------|------|---------|
| `DB_R` | すべてのデータを読み取る | すべての SC_R_* スキーマロールを継承 |
| `DB_W` | すべてのデータに書き込む | すべての SC_W_* スキーマロールを継承 |
| `DB_C` | すべてのオブジェクトを作成する | すべての SC_C_* スキーマロールを継承 |

**注記:** ロール継承（C ← W ← R）はスキーマレベルで確立され、データベースレベルではありません。データベースロールはスキーマロールを集約しますが、独自の継承チェーンはありません。

**アカウントロールのマッピング:**
```
<dataproduct>_READ   ← DB_R（すべてのデータベース）
<dataproduct>_WRITE  ← DB_W（すべてのデータベース）
<dataproduct>_CREATE ← DB_C（すべてのデータベース）
```

**タイムトラベル**
データベースはタイムトラベルサポートのために設定可能な DATA_RETENTION_TIME_IN_DAYS で作成されます。

**追加情報:**
* [CREATE DATABASE](https://docs.snowflake.com/en/sql-reference/sql/create-database)
* [データベースロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#database-roles)
* [タイムトラベル](https://docs.snowflake.com/en/user-guide/data-time-travel)

### 設定の質問

#### このデータ製品の名前は何ですか？（`data_product_name`: text）
データ製品の説明的な名前を提供します。

#### データ製品のデータゾーンを定義してください。（`data_zones`: object-list）
各ゾーン（RAW、CURATED、CONSUMPTION など）と対応するタイムトラベル日数を定義します。

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
デプロイメントの SDLC 環境を選択します（dev、test、stg、prod など）。

#### プラットフォームデータベースに付ける名前は何ですか？（`platform_database_name`: text）
**例:** PLAT\_INFRA

#### ガバナンススキーマに付ける名前は何ですか？（`governance_name`: text）
**推奨名:** GOVERNANCE
