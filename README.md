# はじめに

### ブループリント  
*エキスパートが監修した Snowflake セットアップのガイダンス*    
ブループリントは、Snowflake の SME（専門家）が構築したステップバイステップのワークフローで、環境設定をガイドします。すべてのブループリントにベストプラクティスが組み込まれているため、実績あるパターンに従い、将来のニーズにも対応できることを確信して設定できます。

### Blueprint Manager  
*エキスパートが監修した、セルフサービスのプラットフォーム設定*    
Blueprint Manager は各決定事項を案内し、回答を収集して、環境に合わせた SQL と設定を生成します。推測不要、助けを待つ必要なし — 明確な方向性ですばやく生産性を高められます。

## なぜブループリントを使うのか？

ベストプラクティスに従い、将来のニーズにも対応できることを確信して環境設定できる、エキスパートによるガイダンス。
- **自信** - エキスパートが定義したワークフローで正しく実施できる
- **ベストプラクティス** - スケールするアーキテクチャからスタート
- **スピード** - ガイド付きセットアップで迅速に生産性を向上
- **セルフサービス** - 自分のペースで進められる

## どのように機能するのか？

**ブループリント**  
ブループリントはベストプラクティスの Snowflake 実装とセットアップのためのテンプレートで、エキスパートガイダンス、規定の設定、バリデーションルール、生成ロジックを含みます。ブループリントは Cortex Code を通じてビジネス要件を入力するよう促し、ベストプラクティスの設定を実装するためのすぐに実行できる SQL スクリプトを生成し、意思決定を記録したキュレーション済みのドキュメントを提供します。

**Cortex Code スキル**  
ブループリントは `$blueprint-builder` スキルと `$best-practices-skill` を介して Cortex Code 内で実行されます。スキルはブループリントをナビゲートして実行する方法を Cortex Code に指示し、入力に基づいて適切なベストプラクティスへ AI を誘導し、特定の要件に対する正確なアウトプットを保証します。

*ブループリントをすべての実装レシピと専門知識を含む「料理本」、スキルをニーズに合った正しいレシピを Cortex Code が見つけてたどるための「索引」と考えてください。*

<img width="6800" height="1200" alt="Blueprint Manager Cortex Code Overview" src="https://github.com/user-attachments/assets/6434d217-395d-4092-916a-e32944b41f39" />

# Blueprint Manager

このリポジトリには、Snowflake ブループリントをセットアップするためのインフラストラクチャ・アズ・コードのテンプレートとブループリントが含まれています。

## 構造

- `definitions/` - 設定のための質問定義
- `blueprints/` - 利用可能なブループリント設定
- `scripts/` - テンプレートレンダリング用のユーティリティスクリプト
- `projects/` - 回答とアウトプットを整理するプロジェクトワークスペース
- `output/` - 生成されたインフラコードとドキュメント

## Snowflake Cortex を使ったブループリントのセットアップ（推奨）

Snowflake ブループリントを設定する最も簡単な方法は、Snowflake Cortex の **Blueprint Builder** スキルを使用することです。ガイド付きのインタラクティブな体験を提供します。

### はじめ方

0. 前提条件: Cortex Code CLI

ガイド付きの Cortex Code 体験を得るには、まずマシンにコマンドラインインターフェースをセットアップする必要があります。手順はこちら: https://docs.snowflake.com/LIMITEDACCESS/cortex-code/cortex-code-overview

1. **リポジトリをクローン:**

```bash
git clone https://github.com/Snowflake-Labs/blueprint-manager.git
cd blueprint-manager
```

2. **Cortex CLI を起動:**

```bash
cortex
```

3. **Blueprint Builder を起動:**

```bash
/blueprints:build platform-foundation-setup
```

### 動作の仕組み:

1. **アプローチを選択:**
   - **オプション A:** 組織の説明（規模、ユースケース、セキュリティ要件など）を提供すると、Cortex ができる限り多くの設定を自動的に行います
   - **オプション B:** 完全なガイダンスとともに各質問をステップバイステップで進めます

2. **設定を確認** — Cortex が以下を表示します:
   - ✅ 自動的に回答した質問（理由とともに）
   - ❓ 入力が必要な質問（アカウント名、メールなど）
   - ⚠️ より多くのコンテキストが必要な質問

3. **SQL を生成** — 回答が完成したら、Cortex がレンダースクリプトを実行して実行可能な Snowflake SQL を生成します

### メリット:
- 回答ファイルのフォーマットを理解する必要がない
- 組織プロファイルに基づくインテリジェントなデフォルト値
- 各設定の決定事項の明確な説明
- プロセス全体でバリデーションとガイダンスを提供

## Cortex Code コマンド

このリポジトリで Cortex Code を使用する際に利用できるコマンド:

### コアコマンド

| コマンド | 説明 |
|---------|------|
| `/blueprints:list` | メタデータとともに利用可能なブループリントを一覧表示 |
| `/blueprints:describe <name>` | タスク/ステップのツリーを含むブループリントの詳細を表示 |
| `/blueprints:build <name>` | インタラクティブなブループリント構築プロセスを開始 |
| `/blueprints:validate <file> --blueprint <name>` | 回答ファイルの完全性を確認 |
| `/blueprints:render <file> --blueprint <name>` | 回答から SQL/Terraform/ドキュメントを生成 |

### プロジェクト管理

| コマンド | 説明 |
|---------|------|
| `/blueprints:projects:list` | 既存のプロジェクトを一覧表示 |
| `/blueprints:projects:create <name>` | 新しいプロジェクトのディレクトリ構造を作成 |
| `/blueprints:projects:describe <name>` | プロジェクトの状態（回答、アウトプット、履歴）を表示 |

### 回答ファイル操作

| コマンド | 説明 |
|---------|------|
| `/blueprints:answers:init <name>` | すべての質問を含むスケルトン回答ファイルを生成 |
| `/blueprints:answers:validate <file>` | 欠落/無効な値を確認 |
| `/blueprints:answers:diff <file1> <file2>` | 2 つの回答ファイルを比較 |

### ワークフロー例

```bash
# 1. 利用可能なブループリントを一覧表示
/blueprints:list

# 2. 作業用プロジェクトを作成
/blueprints:projects:create my-company

# 3. インタラクティブに構築を開始
/blueprints:build platform-foundation-setup --project my-company

# 4. またはスケルトンを生成して手動で記入
/blueprints:answers:init platform-foundation-setup --project my-company

# 5. 回答をバリデート
/blueprints:validate answers.yaml --blueprint platform-foundation-setup

# 6. SQL アウトプットを生成
/blueprints:render answers.yaml --blueprint platform-foundation-setup --project my-company
```

## スキル

このリポジトリには自動的にアクティブ化される 2 つの Cortex Code スキルが含まれています:

### Blueprint Builder

ユーザーがインタラクティブに回答ファイルを構築するガイドを行います。以下の場合にトリガーされます:
- ブループリントのセットアップまたは設定を要求する場合
- Snowflake 環境を作成したい場合
- ブループリントの設定についてヘルプが必要な場合

### Snowflake ベストプラクティス

Snowflake SME のキュレーション済みガイダンスを提供します。以下について質問したときにトリガーされます:
- ベストプラクティスや推奨事項
- アカウント戦略、RBAC、セキュリティパターン
- コスト管理とリソースモニタリング
- 命名規則とアーキテクチャの決定

## スキーマリファレンス

### ブループリント `meta.yaml` スキーマ

各ブループリントにはその構造を定義する `meta.yaml` ファイルが含まれています。スキーマは後方互換性のために、フラット（ステップのみ）とネスト（タスクとステップ）の両形式をサポートします。

#### 必須フィールド

| フィールド | 型 | 説明 |
|-----------|-----|------|
| `blueprint_id` | 文字列 | ブループリントの一意識別子 |
| `name` | 文字列 | ブループリントの表示名 |
| `summary` | 文字列 | ブループリントの目的の簡単な説明 |
| `overview` | 文字列 | ブループリントが達成することの詳細な説明 |
| `steps` | リスト | ステップのスラグのリスト（ステップディレクトリへの参照） |

#### オプションフィールド

| フィールド | 型 | 説明 |
|-----------|-----|------|
| `is_repeatable` | ブール値 | ブループリントを複数回実行できるか（デフォルト: false） |
| `tasks` | リスト | メタデータを持つステップのグループ（以下参照） |

#### タスク構造

`tasks` フィールドはステップを明示的なメタデータを持つ論理ユニットにグループ化することを可能にします。各グループが何を達成するか、誰が実行すべきか、どのような前提条件が必要かについてのコンテキストを提供します。

```yaml
tasks:
  - slug: string              # タスクの一意識別子（例: "platform-planning"）
    title: string             # タスクの表示タイトル
    summary: string           # 達成されることの短い概要
    role_requirements:        # Snowflake ロール要件
      - string
    external_requirements:    # 外部要件（SSO、データ統合ソースなど）
      - string
    personas:                 # このタスクに必要なペルソナ/ロール
      - string
    description: string       # スキルと将来の UI のためのオプションの詳細コンテンツ
    steps:                    # このタスクに属するステップ
      - slug: string          # ブループリントのステップリスト内のステップスラグへの参照
        title: string         # このタスク内のステップの表示タイトル
```

#### 例: 最小ブループリント（タスクなし、フラット）

```yaml
blueprint_id: blueprint_abc123
name: Simple Setup
summary: Basic configuration workflow
overview: A straightforward setup process.
steps:
  - step-one
  - step-two
  - step-three
```

#### 例: 完全なブループリント（タスクあり）

```yaml
blueprint_id: blueprint_def456
name: Platform Foundation Setup
summary: Establish core platform infrastructure
overview: Complete platform setup workflow.
is_repeatable: false
steps:
  - determine-account-strategy
  - configure-organization-name
  - create-infrastructure-database
tasks:
  - slug: platform-foundation
    title: Platform Foundation
    summary: Define account strategy and create shared infrastructure.
    external_requirements:
      - Snowflake account (trial or provisioned)
      - Organization information
    personas:
      - Platform Administrator
      - Cloud/Infrastructure Team
    role_requirements:
      - ORGADMIN or ACCOUNTADMIN privileges
    steps:
      - slug: determine-account-strategy
        title: Determine Account Strategy
      - slug: configure-organization-name
        title: Configure Organization Name
      - slug: create-infrastructure-database
        title: Create Infrastructure Database
```

#### タスクコンテンツファイル

タスクの概要コンテンツは、フラットなディレクトリ構造を使用して別のマークダウンファイルに保存することもできます:

```
blueprints/<blueprint-name>/
├── meta.yaml
├── overview.md
├── tasks/
│   ├── platform-planning.md      # フラット構造（推奨）
│   ├── security-setup.md
│   └── cost-management.md
└── <step-slug>/
    ├── overview.md
    ├── code.sql.jinja
    └── dynamic.md.jinja
```

タスクのマークダウンファイルには、`meta.yaml` の構造化フィールドを補完する所要時間の見積もり、主要な決定事項、成果物などの詳細を含めることができます。

## 手動設定（代替方法）

ガイド付き体験なしで直接ファイルを管理したい場合:

### 1. ブループリントを選択

```bash
ls blueprints/
```

ブループリントの `meta.yaml` とステップの `overview.md` ファイルを確認して、何が設定されるかを理解します。

### 2. プロジェクトと回答ファイルを作成

プロジェクトのディレクトリ構造を作成:

```bash
# プロジェクト構造を作成
mkdir -p projects/my-project/answers/<blueprint_id>
mkdir -p projects/my-project/output/iac/sql
mkdir -p projects/my-project/output/documentation
```

回答ファイル（例: `projects/my-project/answers/<blueprint_id>/my_answers.yaml`）を作成し、各質問の値を提供します。質問の詳細と有効なオプションについては `definitions/questions.yaml` を参照してください。

### 3. インフラコードを生成

```bash
python scripts/render_journey.py \
  projects/my-project/answers/<blueprint_id>/my_answers.yaml \
  --blueprint <blueprint_id> \
  --project my-project \
  --lang sql
```

**オプション:**
- `--lang sql` または `--lang terraform` — 出力言語を選択
- `--project <name>` — アウトプットを整理するプロジェクト名
- `--skip-guidance` — ドキュメント生成をスキップ

**アウトプット:**
- SQL/Terraform ファイルは `projects/<project>/output/iac/sql/` に出力
- ドキュメントは `projects/<project>/output/documentation/` に出力

### 4. 生成されたコードを実行

生成された SQL ファイルを確認し、Snowflake ワークシートで実行します。SQL はべき等性があり、複数回実行しても安全です。

ライセンス  
Copyright (c) 2026 Snowflake Inc. All rights reserved.  
このリポジトリはソース利用可能であり、これらの[利用規約](/LICENSE)の下でライセンスされています。
