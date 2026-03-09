# AGENTS.md — AIエージェント向け挙動指示

このファイルは、本リポジトリ上で動作するすべての AI エージェント（Cursor、Claude、Codex など）が従うべき挙動を定義します。

---

## リポジトリの目的

**Blueprint Manager** は、Snowflake 環境を正しく構築するためのブループリント（ステップバイステップのワークフロー）と、それを管理・実行するためのツール群を提供するリポジトリです。

- **ブループリント**: Snowflake SME が設計したベストプラクティス実装のテンプレート
- **Blueprint Manager**: 質問への回答を収集し、環境に合わせた SQL を生成するガイドシステム
- **Cortex Code スキル**: `$blueprint-builder` および `$snowflake-best-practices` スキルを介して Cortex Code 内でブループリントを実行する仕組み

---

## リポジトリ構造

```
blueprint-manager-ja/
├── .cortex/
│   ├── commands/blueprints/   # Cortex Code コマンド定義 (.md)
│   └── skills/                # blueprint-builder / snowflake-best-practices スキル定義
├── blueprints/                # 利用可能なブループリント（各ブループリントにステップ定義）
│   ├── platform-foundation-setup/
│   ├── data-product-setup/
│   └── account-creation/
├── definitions/               # 質問定義ファイル (questions.yaml など)
├── projects/                  # プロジェクトごとの回答ファイルとアウトプット
├── scripts/                   # render_journey.py などのユーティリティスクリプト
└── output/                    # 生成された SQL / ドキュメント
```

---

## 利用可能なコマンド

エージェントはユーザーから以下のコマンド実行を求められることがあります。各コマンドの意味を正確に理解して応答してください。

### コアコマンド

| コマンド | 説明 |
|---------|------|
| `/blueprints:list` | 利用可能なブループリントを一覧表示 |
| `/blueprints:describe <name>` | ブループリントのタスク/ステップ構造を表示 |
| `/blueprints:build <name>` | インタラクティブなブループリント構築プロセスを開始 |
| `/blueprints:validate <file> --blueprint <name>` | 回答ファイルのバリデーション |
| `/blueprints:render <file> --blueprint <name>` | 回答ファイルから SQL / ドキュメントを生成 |

### プロジェクト管理

| コマンド | 説明 |
|---------|------|
| `/blueprints:projects:list` | 既存プロジェクト一覧 |
| `/blueprints:projects:create <name>` | 新しいプロジェクトのディレクトリ構造を作成 |
| `/blueprints:projects:describe <name>` | プロジェクトの状態（回答・アウトプット・履歴）を表示 |

### 回答ファイル操作

| コマンド | 説明 |
|---------|------|
| `/blueprints:answers:init <name>` | スケルトン回答ファイルを生成 |
| `/blueprints:answers:validate <file>` | 欠落/無効な値を確認 |
| `/blueprints:answers:diff <file1> <file2>` | 2 つの回答ファイルを比較 |

---

## スキルの役割

### `$blueprint-builder`
- ユーザーがインタラクティブに回答ファイルを作成するプロセスを担当
- ブループリントのセットアップ、Snowflake 環境の新規作成、設定に関するヘルプ依頼でトリガー
- `render_journey.py` を呼び出して SQL / ドキュメントを生成する

### `$snowflake-best-practices`
- Snowflake SME のキュレーション済みガイダンスを提供
- ベストプラクティス、RBAC、セキュリティ、コスト管理、命名規則、アーキテクチャ設計の質問でトリガー

---

## AIエージェントが守るべき挙動ルール

### 1. 回答収集フローを遵守する
- ブループリントの構築時は、`definitions/questions.yaml` に定義された質問の順序・形式に従うこと
- 自動回答できる質問は理由を明示して自動回答し、判断できないものはユーザーに確認すること

### 2. SQL 生成は `render_journey.py` を経由する
- SQL を直接手書きで生成するのではなく、`scripts/render_journey.py` を使用して回答ファイルから生成すること
- 生成された SQL は `projects/<project>/output/iac/sql/` に出力される

### 3. ブループリント定義ファイルを直接編集しない
- `blueprints/` 配下の `overview.md`、`code.sql.jinja`、`meta.yaml` はテンプレート定義であり、ユーザーからの明示的な依頼がない限り変更しない
- ユーザーの設定データは `projects/` 配下の回答ファイルに保存する

### 4. プロジェクト構造を正しく作成する
新規プロジェクト作成時は以下のディレクトリ構造を使用する:
```
projects/<project-name>/
├── answers/<blueprint_id>/
├── output/iac/sql/
└── output/documentation/
```

### 5. バリデーションを省略しない
- 回答ファイルが揃ったら、`/blueprints:answers:validate` を実行してから SQL 生成を行うこと

### 6. Snowflake のベストプラクティスに従う
- RBAC（ロールベースアクセス制御）、命名規則、コスト管理、セキュリティ構成について、`$snowflake-best-practices` スキルの指針を優先すること

### 7. 不明な点は質問する
- 要件が曖昧な場合、コードを書く前にユーザーへの確認を行うこと
- 特に、アカウント戦略（Single / Multi-Account）、ドメイン設計、SDLC 環境構成など、影響範囲が大きい決定事項については必ず確認すること

---

## 主要ブループリントの概要

| ブループリント | 目的 |
|--------------|------|
| `platform-foundation-setup` | Snowflake 基盤インフラの初期構築（アカウント戦略、セキュリティ、コスト管理など） |
| `data-product-setup` | データプロダクトの構築（データベース、スキーマ、ウェアハウス、ロール、コスト管理） |
| `account-creation` | 追加 Snowflake アカウントの作成と設定（セキュリティ、ID 管理、コスト管理） |

---

## 参考リンク

- Cortex Code 概要: https://docs.snowflake.com/en/user-guide/cortex-code/cortex-code
- Snowflake SQL リファレンス: https://docs.snowflake.com/en/sql-reference-commands
