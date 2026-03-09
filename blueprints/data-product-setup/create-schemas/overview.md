このステップでは、タスク 1 で計画したスキーマ組織に基づいて、各データベース内にスキーマを作成します。各スキーマには以下が含まれます:

1. **スキーマ** — 集中付与制御のための MANAGED ACCESS で作成
2. **スキーマデータベースロール** — スキーマごとに 3 つのロール:
   - `SC_R_<schema>` — スキーマへの読み取りアクセス
   - `SC_W_<schema>` — スキーマへの書き込みアクセス
   - `SC_C_<schema>` — スキーマへの作成アクセス

スキーマロールには現在および将来のオブジェクトへの包括的な付与が含まれており、新しいオブジェクトが作成されたときの自動権限管理を可能にします。

**アカウントコンテキスト:** ADMIN ロールを使用してターゲットアカウントでこの SQL を実行します。

## なぜこれが重要か？

スキーマの組織化が提供するもの:
- **きめ細かいアクセス**: 異なるスキーマが異なるアクセスポリシーを持てる
- **マネージドアクセス**: スキーマ所有者を通じた集中付与制御
- **フューチャーグラント**: 新しいオブジェクトが自動的に適切な権限を継承する
- **ロールのポータビリティ**: データベースロールは任意のアカウントロールに付与できる

## 前提条件

- データベースが作成済み（ステップ 2.2）
- スキーマ設定の定義済み（ステップ 1.4）
- データ製品の ADMIN ロールが利用可能

## 主要な概念

**マネージドアクセススキーマ**
すべてのスキーマは `WITH MANAGED ACCESS` で作成されます:
- スキーマ所有者のみが付与を管理できる
- オブジェクト作成者は自分のオブジェクトへのアクセスを付与できない
- ADMIN ロールでアクセス制御を集中管理する

**スキーマロールの権限**

| ロール | オブジェクトタイプ | 権限 |
|--------|---------------|------|
| SC_R | テーブル、ビュー、外部テーブル、動的テーブル、マテリアライズドビュー | SELECT |
| SC_R | 関数 | USAGE |
| SC_W | テーブル | INSERT, UPDATE, DELETE, TRUNCATE |
| SC_W | ストリーム、プロシージャ、シーケンス、タスク、ファイルフォーマット、ステージ、動的テーブル、アラート | 適切な USAGE/OPERATE |
| SC_C | スキーマ | CREATE TABLE, VIEW, STREAM, FUNCTION, PROCEDURE など |

**ロール階層:**
```
データベースレベル:   DB_C           DB_W            DB_R
                       ↑               ↑               ↑
スキーマレベル:  SC_C_<schema> ← SC_W_<schema> ← SC_R_<schema>
```

**追加情報:**
* [CREATE SCHEMA](https://docs.snowflake.com/en/sql-reference/sql/create-schema)
* [マネージドアクセススキーマ](https://docs.snowflake.com/en/user-guide/security-access-control-considerations#label-managed-access-schemas)
* [フューチャーグラント](https://docs.snowflake.com/en/sql-reference/sql/grant-privilege#future-grants-on-database-or-schema-objects)

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

#### データ製品の各ゾーンのスキーマを定義してください。（`schema_configuration`: object-list）
各ゾーン内のスキーマを定義します（例: RAW ゾーンでは SALESFORCE、SAP など）。
