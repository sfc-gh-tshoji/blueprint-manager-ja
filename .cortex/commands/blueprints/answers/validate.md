<!-- Copyright (c) 2026 Snowflake Inc. All rights reserved.
     Licensed under the Snowflake Skills License. 
     Refer to the LICENSE file in the root of this repository for full terms. -->

# Blueprints 回答ファイル検証

ブループリントを指定しなくても、回答ファイルの不足または無効な値をチェックします。

## 使用方法

```
/blueprints:answers:validate <file> [--blueprint <name>]
```

## 引数

- `<file>`: 検証する YAML 回答ファイルのパス

## オプション

- `--blueprint <name>`: 検証対象のブループリント（未指定の場合はファイルから自動検出）

## 手順

これは回答ファイルの観点に焦点を当てた `blueprints validate` のエイリアス/代替です。

以下の手順で回答ファイルを検証します:

1. **YAML 解析**: YAML 構文を読み込み、検証する
2. **ブループリント検出**: 未指定の場合、ファイルパスまたはコメントからブループリントを推測する
3. **値のチェック**: null、空、または不足している必須値を特定する
4. **型検証**: 値が questions.yaml の期待される型と一致することを確認する

## 出力フォーマット

### 有効な回答ファイル
```
検証中: projects/acme-corp/answers/platform-foundation-setup/answers.yaml

✅ 回答ファイルは有効です！

サマリー:
- 変数の合計: 45
- 入力済み: 45
- 空/Null: 0

次のコマンドでレンダリング可能: blueprints render <file> --blueprint platform-foundation-setup
```

### 問題のある回答ファイル
```
検証中: answers.yaml

⚠️ 回答ファイルに問題があります

サマリー:
- 変数の合計: 45
- 入力済み: 38
- 空/Null: 5
- 型エラー: 2

Null 値（入力が必要）:
| 変数 | 期待される型 | 説明 |
|----------|---------------|-------------|
| org_admin_email | text | 組織管理者のメールアドレス |
| breakglass_accounts | list | 緊急アクセスアカウント |

型エラー:
| 変数 | 期待 | 実際 | 値 |
|----------|----------|-----|-------|
| domain_list | list | text | "sales" |
| budget_alert_emails | list | text | "finance@example.com" |

修正の提案:
- domain_list は YAML リストである必要があります: 
    domain_list:
      - sales
      - marketing
- budget_alert_emails は YAML リストである必要があります:
    budget_alert_emails:
      - finance@example.com
```

## 実装

1. 回答 YAML ファイルを読み込む
2. ブループリントが指定されていない場合、以下から検出を試みる:
   - ファイルパス（例: `answers/platform-foundation-setup/answers.yaml`）
   - YAML コメント（例: `# Blueprint: platform-foundation-setup`）
3. 期待される型を取得するために questions.yaml を読み込む
4. 各値を検証する:
   - 必須フィールドが null でないこと
   - 正しい型（text、list、multi-select、object-list）であること
   - multi-select フィールドの有効なオプションであること
5. 検証レポートを表示する

## エラー処理

- ファイルが見つからない: `エラー: 回答ファイルが見つかりません: <path>`
- 無効な YAML: `エラー: 無効な YAML 構文: <error>`
- ブループリントが検出できない: `エラー: ブループリントを検出できません。--blueprint で指定してください。`

回答ファイルを読み込み、検証を実行することで実行してください。
