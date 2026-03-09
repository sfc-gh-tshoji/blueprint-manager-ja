<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints コマンド

Snowflake ブループリント、プロジェクト、回答ファイルを管理します。

## 使用方法

```
/blueprints:<サブコマンド> [オプション]
```

## 利用可能なサブコマンド

### コアコマンド
| コマンド | 説明 |
|---------|-------------|
| `/blueprints:list` | メタデータ付きで利用可能なブループリントを一覧表示 |
| `/blueprints:describe <name>` | タスク/ステップツリーを含むブループリントの詳細を表示 |
| `/blueprints:build <blueprint-name>` | ブループリント構築プロセスをインタラクティブに開始 |
| `/blueprints:validate <answer-file>` | ブループリント要件に対して回答ファイルの完全性をチェック |
| `/blueprints:render <answer-file>` | 回答から SQL/Terraform/ドキュメントを生成 |

### プロジェクト管理
| コマンド | 説明 |
|---------|-------------|
| `/blueprints:projects:list` | 既存のプロジェクトを一覧表示 |
| `/blueprints:projects:create <name>` | 新しいプロジェクトのディレクトリ構造を作成 |
| `/blueprints:projects:describe <name>` | プロジェクトの状態（回答、出力、履歴）を表示 |

### 回答ファイル操作
| コマンド | 説明 |
|---------|-------------|
| `/blueprints:answers:init <blueprint-name>` | すべての質問を含むスケルトン回答ファイルを生成 |
| `/blueprints:answers:validate <file>` | 不足/無効な値をチェック |
| `/blueprints:answers:diff <file1> <file2>` | 2つの回答ファイルを比較 |

## 使用例

```bash
# 利用可能なブループリントをすべて一覧表示
/blueprints:list

# 特定のブループリントの詳細を表示
/blueprints:describe platform-foundation-setup

# ブループリントをインタラクティブに構築開始
/blueprints:build platform-foundation-setup

# 新しいプロジェクトを作成
/blueprints:projects:create my-customer-project

# スケルトン回答ファイルを生成
/blueprints:answers:init platform-foundation-setup --output answers.yaml

# 回答ファイルを検証
/blueprints:validate answers.yaml --blueprint platform-foundation-setup

# 回答から出力をレンダリング
/blueprints:render answers.yaml --blueprint platform-foundation-setup --lang sql
```

## はじめに

1. **利用可能なブループリントを一覧表示**: `/blueprints:list`
2. **プロジェクトを作成**: `/blueprints:projects:create <project-name>`
3. **回答を初期化**: `/blueprints:answers:init <blueprint-name>`
4. **インタラクティブに構築**: `/blueprints:build <blueprint-name>`（または手動で回答を編集）
5. **回答を検証**: `/blueprints:validate <answer-file>`
6. **出力を生成**: `/blueprints:render <answer-file>`

サブコマンドの詳細ヘルプは次のコマンドで確認できます: `/blueprints:<サブコマンド> --help`
