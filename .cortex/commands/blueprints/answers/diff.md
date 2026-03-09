<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints 回答ファイル差分

2 つの回答ファイルを比較して差分を表示します。

## 使用方法

```
/blueprints:answers:diff <file1> <file2>
```

## 引数

- `<file1>`: 最初の回答ファイルのパス（ベース）
- `<file2>`: 2 番目の回答ファイルのパス（比較対象）

## 手順

2 つの回答ファイルを比較して以下を表示します:

1. **追加**: file2 に存在するが file1 に存在しない変数
2. **削除**: file1 に存在するが file2 に存在しない変数
3. **変更**: 異なる値を持つ変数
4. **未変更**: 同一の値を持つ変数（サマリーのみ）

## 出力フォーマット

```
回答ファイルを比較中:
  ベース:    answers_v1.yaml
  比較対象: answers_v2.yaml

## サマリー
- 追加: 3 変数
- 削除: 1 変数
- 変更: 5 変数
- 未変更: 37 変数

## 追加 (answers_v2.yaml に追加)
| 変数 | 値 |
|----------|-------|
| new_budget_limit | 5000 |
| enable_new_feature | Yes |
| extra_domains | [analytics, ml] |

## 削除 (answers_v2.yaml に存在しない)
| 変数 | 元の値 |
|----------|----------------|
| deprecated_setting | old_value |

## 変更
| 変数 | ベース値 | 新しい値 |
|----------|------------|-----------|
| account_strategy | Single Account | Hub-and-Spoke |
| budget_alert_emails | [old@example.com] | [new@example.com, finance@example.com] |
| identity_provider | Okta | Azure AD |
| mfa_enforcement | Immediately | 30 days grace |
| resource_monitor_limit | 100 | 500 |
```

## 実装

1. 両方の YAML ファイルを読み込む
2. 両ファイルからすべてのユニークなキーを取得する
3. 各キーについて:
   - file1 のみに存在する場合: 削除としてマーク
   - file2 のみに存在する場合: 追加としてマーク
   - 両方に存在する場合: 値を比較（リスト/オブジェクトのディープ比較）
4. 分類された差分を表示する

## オプション

- `--format <table|yaml|json>`: 出力フォーマット（デフォルト: table）
- `--only <added|removed|changed>`: 特定の変更のみ表示
- `--ignore <key1,key2>`: 比較で特定のキーを無視

## エラー処理

- ファイルが見つからない: `エラー: ファイルが見つかりません: <path>`
- 無効な YAML: `エラー: <file> の無効な YAML: <error>`
- 同じファイル: `警告: ファイルを自分自身と比較しています - 差分なし`

## ユースケース

1. **バージョン比較**: 回答の古いバージョンと新しいバージョンを比較する
2. **環境差分**: 本番環境と開発環境の設定を比較する
3. **変更のレビュー**: 再レンダリング前に変更内容を確認する
4. **マージ支援**: 回答ファイルを結合する際の競合を特定する

## 例

```bash
# 2 つの回答ファイルを比較
blueprints answers diff answers_old.yaml answers_new.yaml

# プロジェクト間で比較
blueprints answers diff projects/dev/answers.yaml projects/prod/answers.yaml

# 追加処理用に YAML として出力
blueprints answers diff file1.yaml file2.yaml --format yaml
```

両ファイルを読み込み、差分を計算することで実行してください。
