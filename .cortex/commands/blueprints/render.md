<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints レンダリング

回答ファイルから SQL/Terraform/ドキュメントを生成します。このコマンドは `render_journey.py` スクリプトをラップします。

## 使用方法

```
/blueprints:render <answer-file> --blueprint <blueprint-name> [options]
```

## 引数

- `<answer-file>`: YAML 回答ファイルのパス
- `--blueprint <blueprint-name>`: レンダリングするブループリントの ID

## オプション

- `--lang <sql|terraform>`: 出力言語（デフォルト: sql）
- `--project <name>`: 出力を整理するためのプロジェクト名
- `--skip-guidance`: ドキュメントのレンダリングをスキップし、IaC コードのみを生成

## 手順

提供された引数で `render_journey.py` スクリプトを実行して以下を生成します:

1. **IaC コード**（SQL または Terraform）: 各ステップからレンダリングされたテンプレート
2. **ドキュメント**: 値が入力されたステップバイステップのガイダンス

## 出力構造

`--project` 使用時:
```
projects/<project-name>/
├── answers/
│   └── <blueprint-id>/
│       └── answers_<timestamp>.yaml
└── output/
    ├── iac/
    │   └── sql/
    │       └── <blueprint-id>_<timestamp>.sql
    └── documentation/
        └── <blueprint-id>_<timestamp>.md
```

## 実装

render_journey.py スクリプトを実行:

```bash
python scripts/render_journey.py \
  <answer-file> \
  --blueprint <blueprint-name> \
  --lang <language> \
  --project <project-name>
```

## 出力フォーマット

```
ブループリントをレンダリング中: platform-foundation-setup
言語: sql
プロジェクト: my-project

ステップを処理中...
  ✓ determine-account-strategy
  ✓ configure-organization-name-for-connectivity
  ⚠ enable-organization-account（スキップ: 変数が不足）
  ...

出力が生成されました:
  IaC:    projects/my-project/output/iac/sql/platform-foundation-setup_20250210143022.sql
  Docs:   projects/my-project/output/documentation/platform-foundation-setup_20250210143022.md

サマリー:
  レンダリングされたステップ: 18/22
  スキップされたステップ: 4（変数が不足）

ヒント: '/blueprints:validate <answer-file> --blueprint <blueprint>' を実行して不足変数を確認してください。
```

## エラー処理

- 回答ファイルが存在しない場合: `エラー: 回答ファイルが見つかりません: <path>`
- ブループリントが存在しない場合: `エラー: ブループリント '<name>' が見つかりません`
- レンダリングが失敗した場合: render_journey.py からのエラーメッセージを表示

## 使用例

```bash
# デフォルトプロジェクトで SQL をレンダリング
/blueprints:render answers.yaml --blueprint platform-foundation-setup

# 特定のプロジェクトにレンダリング
/blueprints:render answers.yaml --blueprint data-product-setup --project acme-corp --lang sql

# IaC のみレンダリング（ドキュメントをスキップ）
/blueprints:render answers.yaml --blueprint account-creation --skip-guidance
```

指定された引数で render_journey.py スクリプトを実行してください。
