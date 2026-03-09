これはコアインフラストラクチャセットアップの最終ステップです。ここでは:

1. **ロール階層の接続** — READ → WRITE → CREATE → ADMIN を接続
2. **データベースロールの接続** — データベースロールをアカウントロールにリンク
3. **アカウントアクセスロールの作成** — アカウントレベルの権限のための特化ロール
4. **SCIM への接続** — ID プロバイダーによるユーザープロビジョニングを有効化

このステップの後、データ製品は適切なアクセス制御で完全に運用可能になります。

**アカウントコンテキスト:** SECURITYADMIN を使用してターゲットアカウントでこの SQL を実行します。

## なぜこれが重要か？

適切に接続されたロール階層により以下が可能になります:
- **継承**: 上位のロールは自動的に下位ロールの権限を取得
- **シンプルさ**: 1 つのロールを割り当てるだけで適切なアクセス権を取得
- **柔軟性**: RBAC ロールは管理者の関与なしにアクセスを委任可能
- **統合**: SCIM プロビジョナーがユーザー割り当てを管理可能

## 前提条件

- コアロールの作成済み（ステップ 2.1）
- データベースとスキーマの作成済み（ステップ 2.2、2.3）
- ウェアハウスとアクセスロールの作成済み（ステップ 3.1、3.2）

## 主要な概念

**コアロール階層**
```
READ ← WRITE ← CREATE ← ADMIN
                         ↓
                       RBAC
```
各ロールはその下位ロールから権限を継承します。

**データベースロールの統合**
```
DB_R → READ   （全ゾーン）
DB_W → WRITE  （全ゾーン）
DB_C → CREATE （全ゾーン）
```

**アカウントアクセスロール**
| ロール | 権限 | 目的 |
|--------|------|------|
| `_AR_EXEC_TASK` | EXECUTE TASK | スケジュールタスクの実行 |
| `_AR_VIEW_AUSG` | SNOWFLAKE への IMPORTED PRIVILEGES | アカウント使用量の表示 |
| `_AR_APPLY_DDM` | APPLY MASKING POLICY | データマスキングの適用 |
| `_AR_APPLY_RAP` | APPLY ROW ACCESS POLICY | 行レベルセキュリティの適用 |
| `_AR_APPLY_TAG` | APPLY TAG | ガバナンスタグの適用 |

**追加情報:**
* [ロール階層](https://docs.snowflake.com/en/user-guide/security-access-control-overview#role-hierarchy-and-privilege-inheritance)
* [システム定義ロール](https://docs.snowflake.com/en/user-guide/security-access-control-overview#system-defined-roles)


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

#### 各ゾーンのスキーマを定義してください。（`schema_configuration`: object-list）
各ゾーン内のスキーマを定義します。スキーマはソースシステムまたはサブジェクトエリアでデータベース内のオブジェクトを整理します。

#### SCIM プロビジョナーロールのプレフィックスは何ですか？（`scim_prefix`: text）
SCIM を使用している場合は、SCIM プロビジョナーロールに使用するプレフィックスを入力します（例: AAD、OKTA）。SCIM を使用しない場合は `NONE` と入力します。
