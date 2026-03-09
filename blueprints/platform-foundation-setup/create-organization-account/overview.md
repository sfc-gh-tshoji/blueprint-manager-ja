このステップでは、設定パラメーターを提供し、組織アカウントを作成するための SQL を実行します。このステップで生成されるもの:

1. **組織アカウント** — CREATE ORGANIZATION ACCOUNT で作成された新しい Snowflake アカウント（集中管理機能付き）
2. **初期管理者** — 新しいアカウントへのアクセスをブートストラップする最初の ACCOUNTADMIN ユーザー
3. **アカウント設定** — エディション（Enterprise/Business Critical）とクラウドリージョンの設定

## **なぜこれが重要か？**

Enterprise 以上のアカウントエディションを持つすべての顧客が組織アカウントを作成することを強くお勧めします。最初はシングルアカウント戦略に傾いている場合でも、組織アカウントは集中管理機能と将来の拡張のための対策を提供します。マルチアカウント戦略がある場合は、ORGADMIN ロールが追加のアカウントのプロビジョニングに必要なため、マルチアカウントデプロイメントの他のアカウントの前に作成する必要があります。詳細については[ドキュメント](https://docs.snowflake.com/en/user-guide/organization-accounts)を参照してください。

## **アカウントコンテキスト**

このステップで生成された SQL（組織アカウントを作成するための SQL）は、現在の（初期）アカウントから実行します。その後、このワークフローの残りのステップを続けるために**新しい組織アカウントに切り替えます**。初期アカウントは組織の通常のメンバーアカウントになります。

組織アカウントは、アクセスのブートストラップのために単一の初期 [ACCOUNTADMIN](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system) ユーザーで作成されます。作成後に冗長性のために 1〜2 人の追加 ACCOUNTADMIN ユーザーを追加してください。

## **前提条件**

* 初期[アカウント管理者の](https://docs.snowflake.com/en/user-guide/organization-administrators)メールアドレスを準備してください — 彼らは新しいアカウントにアクセスするための認証情報を受け取ります。
* Snowflake Standard を使用している場合は、[Snowflake Enterprise エディション](https://docs.snowflake.com/en/user-guide/intro-editions)以上を選択またはアップグレードしてください（組織アカウントは Standard エディションでは利用できません）
* アカウントを作成するための [ORGADMIN](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system) ロールアクセス

**注記:** このステップでは ORGADMIN ロールが使用されます。これは初期（非組織）アカウントから実行するためです。組織アカウントが作成された後、将来のアカウント管理は組織アカウント内の GLOBALORGADMIN ロールを使用してください。Snowflake はマルチアカウント組織の ORGADMIN を段階的に廃止しています。

## **主要な概念**

* 組織アカウントと組織アカウント名は、以前のステップで確認された組織と組織名とは異なることに注意することが重要です:
  * **組織**: 組織は、ビジネスエンティティが所有するアカウントをリンクする Snowflake オブジェクトです。**組織名** = [アカウント識別子](https://docs.snowflake.com/en/user-guide/admin-account-identifier)に表示されるビジネスエンティティの名前
  * **組織アカウント**: このステップで設定される[特殊なタイプのアカウント](https://docs.snowflake.com/en/user-guide/organization-accounts)で、複数の Snowflake アカウントを監視および管理するための集中管理機能を提供します。**組織アカウント名** = このステップで設定される組織アカウントの名前。
* **初期管理者:** 組織アカウントでアカウントをブートストラップする [ACCOUNTADMIN](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system) 権限を持つ最初のユーザー。
* **Snowflake エディション:** [Enterprise または Business Critical](https://docs.snowflake.com/en/user-guide/intro-editions) である必要があります（Standard は組織アカウントではサポートされていません）
* **クラウドリージョン:** 組織アカウントがホストされる地理的な[リージョン](https://docs.snowflake.com/en/user-guide/intro-regions)

## **追加情報**

* [System Roles](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system) - ACCOUNTADMIN および ORGADMIN 機能の概要
* [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts) — 概要と機能
* [Creating Accounts](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts-create) — 組織内にアカウントを作成する方法
* [Snowflake Editions](https://docs.snowflake.com/en/user-guide/intro-editions) — エディション間の機能比較
* [Supported Cloud Regions](https://docs.snowflake.com/en/user-guide/intro-regions) — クラウドプロバイダーごとの利用可能なリージョン
* [CREATE ACCOUNT](https://docs.snowflake.com/en/sql-reference/sql/create-account) — SQL コマンドリファレンス

### 設定の質問

#### 組織アカウントに付ける名前は何ですか？（`org_account_name`: text）
**推奨名:** ORG
  組織ごとに 1 つの組織アカウントしか作成できないため、名前はこの特別な目的を明確に示す必要があります。単純に ORG と名前を付けることをお勧めします。

  **組織アカウント名 ORG の URL 例:**
  * カスタム組織名の場合: [https://ACME-ORG.snowflakecomputing.com](https://ACME-ORG.snowflakecomputing.com)
    * 組織名 = ACME
    * 組織アカウント名 = Org
  * システム生成組織名: [https://XY12345-ORG.snowflakecomputing.com](https://XY12345-ORG.snowflakecomputing.com)
    * 組織名 = XY12345
    * 組織アカウント名 = Org
* **要件:**
  * Snowflake Enterprise エディション以上
  * 既存のアカウントに ORGADMIN ロールが付与されていること
* **追加情報:**
  * [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts)
  * [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### 初期管理者に使用するユーザー名は何ですか？（`org_admin_name`: text）
初期管理者は、組織アカウントで ACCOUNTADMIN 権限を持つ最初のユーザーです。これは**ユーザー名**（ログイン名）であり、人のフルネームではありません。

  **推奨事項:**
  * 標準的なユーザー名フォーマットを使用する（例: jsmith、john.smith、またはメールアドレス）
  * プラットフォームまたはセキュリティチームの信頼できるメンバーを選ぶ
  * 一貫性のために小文字を使用する
  * .、\_、または \- 以外の特殊文字を避ける
* **例:**
  * platform\_admin
  * john.smith
  * jsmith@company.com
* **セキュリティ注記:**
  * パスワードは最初のログイン時に変更が必要に設定されます（MUST\_CHANGE\_PASSWORD = TRUE）
  * [MFA](https://docs.snowflake.com/en/user-guide/security-mfa) が必要になります
  * RSA 公開鍵認証はアカウント作成後に設定できます
  * 冗長性のために追加の ACCOUNTADMIN ユーザーを追加してください
* **追加情報:**
  * [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user)
  * [System Roles](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system)
  * [ACCOUNTADMIN Role](https://docs.snowflake.com/en/user-guide/security-access-control-overview#accountadmin-role)
  * [MFA](https://docs.snowflake.com/en/user-guide/security-mfa)

#### 組織アカウント管理者に使用するメールアドレスは何ですか？（`org_admin_email`: text）
* **ガイダンス:**
  このメールアドレスは初期 ACCOUNTADMIN ユーザーに関連付けられ、以下のために使用されます:
  * アカウントのアクティベーションとパスワードリセット通知
  * 重要なセキュリティアラートと通知
  * Snowflake サポートのコミュニケーション
* **ベストプラクティス:**
  * 監視されているメールアドレスを使用する（非アクティブになる可能性のある個人用メールではなく）
  * 継続性のために共有メールボックスまたは配信リストの使用を検討する（例: snowflake-admin@yourcompany.com）
  * メールが名前の管理者によって所有または利用可能であることを確認する
* **注記:** アカウント作成後に変更できますが、最初から正しく設定することが重要です。
  **追加情報:**
  * [CREATE USER](https://docs.snowflake.com/en/sql-reference/sql/create-user)
  * [System Roles](https://docs.snowflake.com/en/user-guide/security-access-control-overview#label-access-control-overview-roles-system)
  * [ACCOUNTADMIN Role](https://docs.snowflake.com/en/user-guide/security-access-control-overview#accountadmin-role)

#### 組織アカウントに使用する Snowflake エディションは何ですか？（`org_account_edition`: multi-select）
組織アカウントには **Enterprise エディション以上**が必要です。Standard エディションは組織アカウント機能をサポートしていません。

**Enterprise エディション**（推奨）:
* 完全な組織管理機能
* 同時実行スケーリングのためのマルチクラスターウェアハウス
* 列レベルのセキュリティと最大 90 日のタイムトラベル
* ビジネス継続性のためのフェイルオーバー/フェイルバック
* 最適な対象: ほとんどの組織

**Business Critical エディション:**
* Enterprise のすべて、さらに:
* HIPAA および PCI DSS コンプライアンスサポート
* 顧客管理の暗号化キー（Tri-Secret Secure）
* プライベート接続（AWS PrivateLink、Azure Private Link、GCP Private Service Connect）
* 最適な対象: 高度に規制された業界、または組織アカウントが厳格なコンプライアンス要件を満たす必要がある場合

**推奨事項:** Enterprise エディションは、機密性の高いビジネスデータをホストするのではなく、主に管理目的のために機能するため、通常組織アカウントには十分です。

**追加情報:**
* [Snowflake Editions](https://docs.snowflake.com/en/user-guide/intro-editions)
**オプション:**
- ENTERPRISE
- BUSINESS_CRITICAL

#### 組織アカウントをホストするクラウドリージョンはどこですか？（`org_account_region`: text）
  組織アカウントが作成される Snowflake リージョン ID を選択してください。

  **利用可能なリージョンの確認方法:**
  ```sql
  -- 組織で利用可能なすべてのリージョンを一覧表示する
  SHOW REGIONS;
  ```

  **主な考慮事項:**
  * **規制対象リージョン:** 組織が米国 SnowGov リージョンにアカウントを持つ場合、Snowflake はその規制対象リージョンに組織アカウントを作成することを推奨します
  * **管理者の場所:** 低レイテンシのためにプラットフォーム管理者に近いリージョンを選択する
  * **プライマリアカウントの場所:** プライマリデータアカウントと同じリージョンの使用を検討する
  * **データ居住:** 組織アカウントはビジネスデータを含まないべきですが、一部の組織はすべてのアカウントを同じ地理的リージョンに保持することを好みます

  **一般的なリージョン ID:**
  * AWS US West 2（オレゴン）: `AWS_US_WEST_2`
  * AWS US East 1（バージニア州北部）: `AWS_US_EAST_1`
  * Azure East US 2: `AZURE_EASTUS2`
  * GCP US Central 1: `GCP_US_CENTRAL1`

  **注記:** REGION を省略すると、組織アカウントはコマンドを実行しているアカウントと同じリージョンに作成されます。

  **追加情報:**
  * [CREATE ORGANIZATION ACCOUNT](https://docs.snowflake.com/en/sql-reference/sql/create-organization-account)
  * [Supported Cloud Regions](https://docs.snowflake.com/en/user-guide/intro-regions)

#### あなたの Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
Snowflake 組織名はアカウント URL と接続識別子の最初の部分です。これはすべてのアカウント識別子の必須コンポーネントです。
  **組織名の見つけ方:**
  現在の Snowflake URL を確認してください。組織名はダッシュの前の部分です:
  * https://\*\*ACME\*\*-prod.snowflakecomputing.com → 組織名は ACME
  * https://\*\*XY12345\*\*-prod.snowflakecomputing.com → 組織名は XY12345
* **組織名のタイプ:**
  * **カスタム名:** Snowflake から要求された ACME や INITECH のような人間が読める名前。より良いブランディングとより読みやすい URL を提供します。
  * **システム生成:** セルフサービスのサインアップ時に自動的に作成された XY12345 や AB98765 のような自動割り当ての英数字コード。
* **カスタム名の要求方法:** システム生成された名前がありそれを変更したい場合は、[Snowflake サポートに連絡する](https://community.snowflake.com/s/article/How-To-Submit-a-Support-Case-in-Snowflake-Lodge)かアカウントチームに連絡してください。
  **追加情報:**
  * [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### 組織アカウントを作成しますか？（`enable_org_account`: multi-select）
組織アカウントは、Snowflake 環境の集中管理機能を提供する特殊なアカウントです。

  **⚠️ 強い推奨事項: 組織アカウントを作成してください**
  シングルアカウント戦略を選択した場合でも、組織アカウントの作成を強くお勧めします。その理由は:
  * **将来に向けた対策:** 後でアカウントを追加する可能性がある場合、組織アカウントがすでに設定されていれば拡張がシームレスになります
  * **集中型機能:** 組織レベルのビュー、請求、およびガバナンス機能へのアクセス
  * **移行の容易さ:** 既存の組織アカウントがあれば、後でマルチアカウント戦略への移行が大幅に容易になります
  * **デメリットなし:** 組織アカウントのオーバーヘッドは最小限で、シングルアカウント運用に影響しません
* **マルチアカウント戦略の場合:** 組織アカウントは**必須**です。以下を提供します:
  * すべてのアカウントの集中ビュー
  * 統一請求とコスト管理
  * プログラムによる子アカウントの作成と管理
  * 組織レベルのポリシーとガバナンス
* **要件:**
  * Snowflake Enterprise エディション以上
  * [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts) — 組織アカウントの概要と機能
  * [ORGADMIN Role](https://docs.snowflake.com/en/user-guide/security-access-control-overview#orgadmin-role) — 権限と責任
  * [Snowflake Editions](https://docs.snowflake.com/en/user-guide/intro-editions) — Snowflake エディションのオプション
**オプション:**
- Yes
- No
