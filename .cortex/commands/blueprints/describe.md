<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints 詳細表示

特定のブループリントに関する詳細情報（タスク/ステップツリーを含む）を表示します。

## 使用方法

```
/blueprints:describe <blueprint-name>
```

## 引数

- `<blueprint-name>`: ブループリントの ID/ディレクトリ名（例: `platform-foundation-setup`、`account-creation`、`data-product-setup`）

## 手順

1. `blueprints/<blueprint-name>/meta.yaml` からブループリントの `meta.yaml` を読み込む
2. ブループリントが存在しない場合、利用可能なブループリント名でエラーを表示
3. 包括的なブループリント情報を表示

## 出力フォーマット

```
# ブループリント: <name>

**ID:** <blueprint_id>
**サマリー:** <summary>
**繰り返し可能:** <はい/いいえ>

## 概要

<overview text>

## ステップ（合計 <count> 件）

| # | ステップ ID | タイトル |
|---|---------|-------|
| 1 | determine-account-strategy | アカウント戦略の決定 |
| 2 | configure-organization-name | 組織名の設定 |
...

## ステップ詳細

各ステップについて、ステップディレクトリに `overview.md` が存在する場合は以下を表示:
- ステップ番号と ID
- 概要の最初の見出し（タイトル）
- 最初の段落（簡単な説明）
```

## 実装

1. `blueprints/<blueprint-name>/meta.yaml` を読み込む
2. YAML を解析して `name`、`blueprint_id`、`summary`、`overview`、`is_repeatable`、`steps` を取得
3. `steps` リストの各ステップについて:
   - `blueprints/<blueprint-name>/<step-id>/` が存在するか確認
   - `dynamic.md.jinja` を読み込んでタイトル（最初の `# ` 見出し）を抽出
   - 追加のコンテキストのために `overview.md` が存在する場合は読み込む
4. 整形されたマークダウン構造ですべての情報を表示

## エラー処理

ブループリントが見つからない場合:
```
エラー: ブループリント '<name>' が見つかりません。

利用可能なブループリント:
- account-creation
- data-product-setup  
- platform-foundation-setup

'blueprints list' を実行して利用可能なブループリントの詳細を確認してください。
```

指定されたブループリントの meta.yaml を読み込み、情報を提示することで実行してください。
