<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints プロジェクト一覧

ブループリントマネージャーワークスペース内の既存プロジェクトをすべて一覧表示します。

## 使用方法

```
blueprints projects list
```

## 手順

`projects/` ディレクトリをスキャンし、すべてのプロジェクトサブディレクトリとその状態情報を一覧表示します。

各プロジェクトについて以下を表示:
- **名前**: プロジェクトのディレクトリ名
- **ブループリント**: 回答ファイルが存在するブループリント
- **最終更新**: プロジェクトが最後に更新された日時
- **状態**: 簡単な状態（例: "3 件の回答ファイル、2 件のレンダリング"）

## 出力フォーマット

```
プロジェクト:

| プロジェクト | ブループリント | 回答ファイル数 | 最終更新 |
|---------|------------|--------------|---------------|
| sample-project | platform-foundation-setup, account-creation, data-product-setup | 3 | 2025-01-15 |
| acme-corp | platform-foundation-setup | 1 | 2025-02-01 |
| demo-customer | data-product-setup | 2 | 2025-02-10 |

合計: 3 件のプロジェクト
```

## 実装

1. `projects/` 内のすべてのディレクトリを一覧表示
2. 各プロジェクトディレクトリについて:
   - ブループリント回答ファイルの `answers/` サブディレクトリをチェック
   - ブループリントごとの回答ファイル数をカウント
   - 最終更新時刻を取得
3. 整形されたテーブルで結果を表示

## 空の状態

プロジェクトが存在しない場合:
```
プロジェクトが見つかりません。

新しいプロジェクトを作成するには: /blueprints:projects:create <name>
```

プロジェクトディレクトリをスキャンして情報を提示することで実行してください。
