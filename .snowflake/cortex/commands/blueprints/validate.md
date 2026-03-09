<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints 検証

ブループリント要件に対する回答ファイルの完全性をチェックし、ブループリントスキーマを検証します。

## 使用方法

```
/blueprints:validate <answer-file> --blueprint <blueprint-name>
```

## 引数

- `<answer-file>`: 検証する YAML 回答ファイルのパス
- `--blueprint <blueprint-name>`: 検証対象のブループリント ID

## 手順

以下をチェックして回答ファイルを検証します:

1. **ファイルの存在確認**: 回答ファイルが存在し、有効な YAML であることを確認
2. **ブループリントの一致**: ブループリントが存在することを確認
3. **ブループリントスキーマ**: ブループリントの meta.yaml 構造を検証（以下のスキーマ検証を参照）
4. **必須変数**: 各ステップのテンプレートで必須変数をチェック
5. **不足値**: 回答ファイルに不足している変数を特定
6. **Null 値**: 存在するが null/空の値を持つ変数を特定
7. **型の検証**: 値が期待される型（text、list、object-list、multi-select）と一致するかチェック

## スキーマ検証

バリデーターはフラット形式（ステップのみ）とネスト形式（タスク付きステップ）の両ブループリント形式をサポートします:

### フラット形式（タスクなし）
```yaml
blueprint_id: blueprint_abc123
name: Simple Setup
summary: Basic configuration
overview: Description here.
steps:
  - step-one
  - step-two
```

### ネスト形式（タスク付き）
```yaml
blueprint_id: blueprint_def456
name: Platform Setup
summary: Full platform configuration
overview: Description here.
steps:
  - step-one
  - step-two
tasks:
  - slug: task-one
    title: Task One
    summary: First task group
    role_requirements:
      - ACCOUNTADMIN
    external_requirements:
      - External system access
    personas:
      - Platform Administrator
    steps:
      - slug: step-one
        title: Step One
      - slug: step-two
        title: Step Two
```

### タスク構造の検証ルール

ブループリントに `tasks` が含まれる場合、以下の検証が実行されます:

1. **必須タスクフィールド**: 各タスクには `slug`、`title`、`summary` が必要
2. **オプションタスクフィールド**: `role_requirements`、`external_requirements`、`personas`、`description`、`steps`
3. **ステップ参照**: タスク内のステップスラグはブループリントの `steps` リストで定義された有効なステップを参照する必要がある
4. **スラグの一意性**: タスクスラグはブループリント内で一意である必要がある
5. **ステップのカバレッジ**: すべてのステップは少なくとも1つのタスクに割り当てられる必要がある（未割り当ての場合は警告）

## 出力フォーマット

### 有効な回答ファイル
```
検証中: answers.yaml
ブループリント: platform-foundation-setup

✅ 回答ファイルは完全です！

サマリー:
- 合計ステップ数: 22
- レンダリング可能なステップ数: 22
- 必須変数がすべて提供されています
```

### 無効な回答ファイル
```
検証中: answers.yaml
ブループリント: platform-foundation-setup

⚠️ 回答ファイルに不足または無効な値があります

サマリー:
- 合計ステップ数: 22
- レンダリング可能なステップ数: 18
- 問題のあるステップ数: 4

不足変数:
| 変数 | 必要なステップ |
|----------|-------------------|
| org_admin_email | create-organization-account, provision-account-administrators |
| breakglass_accounts | create-break-glass-emergency-access |

Null 変数:
| 変数 | 必要なステップ |
|----------|-------------------|
| scim_admin_users | configure-scim-integration |

レンダリング不可能なステップ:
| ステップ | 不足 | Null |
|------|---------|------|
| create-organization-account | org_admin_email | - |
| configure-scim-integration | - | scim_admin_users |
| provision-account-administrators | org_admin_email | - |
| create-break-glass-emergency-access | breakglass_accounts | - |

'blueprints build <blueprint>' を実行して不足値をインタラクティブに入力してください。
```

## 実装

1. 回答 YAML ファイルを読み込む
2. ブループリントの meta.yaml を読み込む
3. ブループリントの各ステップについて:
   - `code.sql.jinja` と `dynamic.md.jinja` テンプレートを検索
   - テンプレートを解析してすべての参照変数を検索（render_journey.py のような Jinja2 AST を使用）
   - 各変数が回答に存在し、null でないことを確認
4. 結果を集計して検証レポートを表示

## エラー処理

- 回答ファイルが存在しない場合: `エラー: 回答ファイルが見つかりません: <path>`
- 回答ファイルが無効な YAML の場合: `エラー: 回答ファイルの無効な YAML: <error>`
- ブループリントが存在しない場合: `エラー: ブループリント '<name>' が見つかりません`

## 終了コード

- `0`: 回答ファイルは完全かつ有効
- `1`: 回答ファイルに不足または無効な値がある
- `2`: ファイルまたはブループリントが見つからない

指定された回答ファイルとブループリントを読み込み、検証を実行することで実行してください。
