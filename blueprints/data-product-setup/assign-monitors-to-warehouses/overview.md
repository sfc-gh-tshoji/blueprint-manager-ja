リソースモニターを作成した後、追跡したいウェアハウスに割り当てる必要があります。ウェアハウスは一度に 1 つのリソースモニターにのみ割り当てられますが、1 つのリソースモニターは複数のウェアハウスを追跡できます。

このステップでは、データ製品のリソースモニターをデータ製品用に設定されたすべてのウェアハウスに割り当てます。割り当てられたすべてのウェアハウスのクレジット消費がモニターのクォータに計上されます。

**アカウントコンテキスト:** リソースモニターを割り当てるには ACCOUNTADMIN でこの SQL を実行します。

## なぜこれが重要か？

ウェアハウスにモニターを割り当てることで以下が可能になります:
- **統合された追跡**: すべてのデータ製品コンピューティングを 1 つの予算で管理
- **自動的な適用**: クォータを超えたときに一時停止
- **コスト帰属**: コンピューティングコストの明確な所有権

## 前提条件

- リソースモニターの作成済み（ステップ 5.1）
- ウェアハウスの作成済み（ステップ 3.1）

## 主要な概念

**モニターとウェアハウスの関係**
```
リソースモニター ──┬── ウェアハウス 1
                   ├── ウェアハウス 2
                   └── ウェアハウス N
```
すべてのウェアハウスはモニターのクォータを共有します。

**クォータの消費**
- 割り当てられたすべてのウェアハウスが同じクォータに寄与する
- クレジットは消費されるとすぐにカウントされる
- クォータは頻度設定に従ってリセットされる

**モニター割り当ての削除**
ウェアハウスのモニタリングを削除するには:
```sql
ALTER WAREHOUSE <name> SET RESOURCE_MONITOR = NULL;
```

**モニター割り当ての変更**
ウェアハウスを別のモニターに移動するには:
```sql
ALTER WAREHOUSE <name> SET RESOURCE_MONITOR = <new_monitor>;
```

**追加情報:**
* [ALTER WAREHOUSE](https://docs.snowflake.com/en/sql-reference/sql/alter-warehouse)
* [リソースモニターの使用](https://docs.snowflake.com/en/user-guide/resource-monitors#viewing-and-modifying-resource-monitors)


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

#### リソースモニターのクレジット上限は何クレジットにしますか？（`resource_monitor_credits`: text）
このデータ製品のウェアハウスの最大クレジット消費量を設定します。
