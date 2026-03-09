<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints 回答ファイル初期化

ブループリントのすべての質問を含むスケルトン回答ファイルを生成します。

## 使用方法

```
/blueprints:answers:init <blueprint-name> [options]
```

## 引数

- `<blueprint-name>`: 回答を生成するブループリント ID

## オプション

- `--output <file>`: 出力ファイルパス（デフォルト: `answers_<blueprint>_<timestamp>.yaml`）
- `--project <name>`: 回答ファイルを保存するプロジェクト
- `--format <full|minimal>`: 出力フォーマット（デフォルト: full）
  - `full`: コメントとして質問テキストとガイダンスを含める
  - `minimal`: null 値の変数名のみ

## 手順

以下の手順でスケルトン回答ファイルを生成します:

1. ブループリントの meta.yaml を読み込んでステップリストを取得する
2. すべてのステップテンプレート（code.sql.jinja、dynamic.md.jinja）を検索して必要な変数を特定する
3. `definitions/questions.yaml` から質問定義を読み込む
4. カテゴリ別に整理されたすべての変数を含む YAML ファイルを作成する

## 出力フォーマット（Full）

```yaml
# ブループリント: プラットフォーム基盤セットアップ
# 生成日: 2025-02-10 14:30:22
# 
# 手順:
# - 以下の各変数に値を入力してください
# - [REQUIRED] とマークされた変数はレンダリングに値が必要です
# - 'blueprints validate' を実行して完全性を確認してください

# ==============================================================================
# アカウント戦略
# ==============================================================================

# 組織の Snowflake アカウント戦略は何ですか？
# オプション: Single Account、Hub-and-Spoke、Multi-Account
# [REQUIRED]
account_strategy: null

# Snowflake の組織名は何ですか？
# これは接続設定に使用されます
# [REQUIRED]
snowflake_org_name: null

# ==============================================================================
# IDマネジメント
# ==============================================================================

# どの ID プロバイダーを使用しますか？
# オプション: Okta、Azure AD、その他の SAML プロバイダー、Snowflake ネイティブ
identity_provider: null

# ... 追加の変数 ...
```

## 出力フォーマット（Minimal）

```yaml
# ブループリント: プラットフォーム基盤セットアップ
# 生成日: 2025-02-10 14:30:22

account_strategy: null
snowflake_org_name: null
identity_provider: null
# ... 追加の変数 ...
```

## 実装

1. ブループリントの meta.yaml を読み込む
2. 各ステップのテンプレートを解析して参照される変数を特定する
3. definitions/questions.yaml を読み込む
4. メタデータのために変数と質問を照合する
5. コメント付きの整理された YAML を生成する
6. 出力ファイルに書き込む

## エラー処理

- ブループリントが見つからない: `エラー: ブループリント '<name>' が見つかりません`
- 出力に書き込めない: `エラー: '<path>' に書き込めません`

## 例

```bash
# 完全な回答ファイルを生成
blueprints answers init platform-foundation-setup

# 特定の場所に最小限のファイルを生成
blueprints answers init data-product-setup --output my-answers.yaml --format minimal

# プロジェクト内に生成
blueprints answers init account-creation --project acme-corp
```

ブループリントを分析し、スケルトン回答ファイルを生成することで実行してください。
