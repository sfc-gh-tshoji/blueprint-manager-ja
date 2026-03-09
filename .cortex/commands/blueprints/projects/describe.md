<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints プロジェクト詳細

プロジェクトの詳細な状態（回答ファイル、出力、履歴を含む）を表示します。

## 使用方法

```
/blueprints:projects:describe <name>
```

## 引数

- `<name>`: 詳細表示するプロジェクト名

## 手順

プロジェクトに関する包括的な情報を表示します:

1. **プロジェクト概要**: プロジェクトの基本情報
2. **回答ファイル**: ブループリントごとに整理されたすべての回答ファイルを一覧表示
3. **レンダリング済み出力**: 生成されたすべての IaC とドキュメントファイルを一覧表示
4. **履歴**: プロジェクト活動のタイムライン

## 出力フォーマット

```
# プロジェクト: my-project

**場所:** projects/my-project/
**作成日:** 2025-01-15
**最終更新:** 2025-02-10

## 回答ファイル

### platform-foundation-setup
| ファイル | 作成日 | 状態 |
|------|---------|--------|
| answers_20250115_143022.yaml | 2025-01-15 14:30 | 完了 |
| answers_20250210_091500.yaml | 2025-02-10 09:15 | 3 件の値が不足 |

### data-product-setup
| ファイル | 作成日 | 状態 |
|------|---------|--------|
| answers_20250201_120000.yaml | 2025-02-01 12:00 | 完了 |

## レンダリング済み出力

### IaC (SQL)
| ファイル | ブループリント | 生成日 |
|------|-----------|-----------|
| platform-foundation-setup_20250115_143022.sql | platform-foundation-setup | 2025-01-15 |
| data-product-setup_20250201_120000.sql | data-product-setup | 2025-02-01 |

### ドキュメント
| ファイル | ブループリント | 生成日 |
|------|-----------|-----------|
| platform-foundation-setup_20250115_143022.md | platform-foundation-setup | 2025-01-15 |
| data-product-setup_20250201_120000.md | data-product-setup | 2025-02-01 |

## サマリー
- 使用ブループリント数: 2
- 合計回答ファイル数: 3
- 合計レンダリング数: 4
```

## 実装

1. プロジェクトが存在することを確認
2. `projects/<name>/answers/` で回答ファイルをスキャン
3. `projects/<name>/output/` でレンダリング済みファイルをスキャン
4. 回答ファイルを解析して完全性の状態をチェック
5. 整形されたレポートを表示

## エラー処理

プロジェクトが見つからない場合:
```
エラー: プロジェクト 'my-project' が見つかりません。

利用可能なプロジェクト:
- sample-project
- acme-corp

'blueprints projects list' を実行してすべてのプロジェクトを確認してください。
```

プロジェクトディレクトリを読み込み、情報を提示することで実行してください。
