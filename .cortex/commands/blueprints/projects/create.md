<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints プロジェクト作成

ブループリント作業を整理するための新しいプロジェクトディレクトリ構造を作成します。

## 使用方法

```
/blueprints:projects:create <name>
```

## 引数

- `<name>`: プロジェクト名（英数字、アンダースコア、ハイフンのみ）

## 手順

標準構造で新しいプロジェクトディレクトリを作成します:

```
projects/<name>/
├── answers/
│   └── .gitkeep
└── output/
    ├── iac/
    │   └── sql/
    │       └── .gitkeep
    └── documentation/
        └── .gitkeep
```

## 実装

1. プロジェクト名を検証（英数字、アンダースコア、ハイフンのみ）
2. プロジェクトが既に存在するか確認
3. ディレクトリ構造を作成
4. git で空のディレクトリを保持するための .gitkeep ファイルを作成

## 出力フォーマット

### 成功
```
✓ プロジェクト 'my-project' が正常に作成されました！

プロジェクト構造:
  projects/my-project/
  ├── answers/
  └── output/
      ├── iac/sql/
      └── documentation/

次のステップ:
1. 回答を初期化: /blueprints:answers:init <blueprint-name> --project my-project
2. インタラクティブに構築: /blueprints:build <blueprint-name> --project my-project
```

### プロジェクトが既に存在する場合
```
エラー: プロジェクト 'my-project' は既に存在します。

'/blueprints:projects:describe my-project' を使用してその内容を確認してください。
```

### 無効な名前
```
エラー: プロジェクト名 'my project!' は無効です。
プロジェクト名には英数字、アンダースコア、ハイフンのみ使用できます。

有効な名前の例:
  - my-project
  - acme_corp
  - customer123
```

## エラー処理

- 名前に無効な文字が含まれる場合: 有効な名前の例でエラーを表示
- プロジェクトが既に存在する場合: エラーを表示して describe コマンドを提案
- 権限の問題: ファイルシステムエラーを表示

プロジェクトのディレクトリ構造を作成することで実行してください。
