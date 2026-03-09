<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints ビルド

インタラクティブなブループリント構築プロセスを開始します。このコマンドは `blueprint-builder` スキルをラップして、ブループリントの完全な回答ファイルの作成をガイドします。

## 使用方法

```
/blueprints:build <blueprint-name> [--project <project-name>]
```

## 引数

- `<blueprint-name>`: 構築するブループリントの ID（例: `platform-foundation-setup`）
- `--project <project-name>`: 回答と出力を整理するためのオプションのプロジェクト名

## 手順

このコマンドは指定されたブループリントで `blueprint-builder` スキルを呼び出します。スキルは以下を実行します:

1. **プロジェクトの選択/作成** - プロジェクトワークスペースを選択または作成
2. **ブループリントの読み込み** - 指定されたブループリント定義を読み込み
3. **回答ファイルの初期化** - 新規作成または既存の回答を読み込み
4. **ユーザーコンテキストの収集** - オープンエンドの説明またはステップバイステップで要件を収集
5. **回答の生成** - 提供されたコンテキストに基づいて回答を自動入力
6. **サマリーの提示** - 回答済み/未回答の質問を表示
7. **インタラクティブウォークスルー** - 各ステップをレビューして残りの値を入力
8. **IaC の生成** - SQL/Terraform/ドキュメントをレンダリング

## 実装

$blueprint-builder Help me build the {{blueprint-name}} blueprint{{#if project}} for project {{project}}{{/if}}

## 使用例

```bash
# プラットフォームファウンデーションブループリントの構築を開始
/blueprints:build platform-foundation-setup

# 特定のプロジェクトでビルド
/blueprints:build data-product-setup --project acme-corp
```

## エラー処理

ブループリントが見つからない場合:
```
エラー: ブループリント '<name>' が見つかりません。

利用可能なブループリント:
- account-creation
- data-product-setup  
- platform-foundation-setup

'/blueprints:list' を実行して利用可能なブループリントをすべて確認してください。
```

## 注意事項

- 構築プロセスはインタラクティブで、要件を収集するための質問が行われます
- 自動入力のために事前に組織/要件の説明を提供できます
- 進捗は保存され、後で再開できます
- 回答の完全性を確認するには `/blueprints:validate` を使用してください
