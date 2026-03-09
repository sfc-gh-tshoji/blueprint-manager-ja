このステップでは、Snowflake の**組織名**を記録します。これはすべての接続 URL とアカウント識別子の重要なコンポーネントです。例えば、URL https://ACME-prod.snowflakecomputing.com の場合、組織名は「ACME」です。

この値は、接続 URL、SCIM エンドポイント、SAML 設定、およびドキュメントを構成するために、後続のステップ全体で使用されます。作成するすべてのアカウントにはその URL にこの組織名が含まれるため、今すぐ正しい値を記録することが不可欠です。

**注記:** このステップは*既存の*組織名を記録します — 組織名を作成または変更するものではありません。システム生成された名前（XY12345 など）があり、このステップと後続のステップを進める前にカスタム名を希望する場合は、最初に別途 Snowflake サポートに連絡する必要があります。

## **なぜこれが重要か？**

組織は、ビジネスエンティティが所有するアカウントをリンクする Snowflake オブジェクトです。組織名が確立され、ユーザーとシステムがアカウント識別子と URL を使用して Snowflake に安全に接続するアカウントが設立されると、変更が必要なすべての場所で組織名を変更することは困難です。**このため、顧客は事前に計画し、組織名が将来の接続ニーズを満たしていることを確認することを強くお勧めします。**

## **前提条件**

* **現在の組織名を特定する** — このステップで組織名を確認する前に、まず現在の組織名を変更したいかどうかを確認してください。これを見つけるには、現在の Snowflake URL を確認してください。組織名はダッシュの前の部分です。例えば:
  * https://ACME-prod.snowflakecomputing.com → 組織名は ACME
  * https://XY12345-prod.snowflakecomputing.com → 組織名は XY12345

## **主要な概念**

[**アカウント識別子**](https://docs.snowflake.com/en/user-guide/admin-account-identifier)は、組織内の Snowflake アカウントを識別します。これらは、使用するアカウントを指定する必要がある Snowflake のあらゆる場所で必要で、以下が含まれますが、これらに限定されません:

* Snowflake ウェブインターフェースにアクセスするための URL。
* Snowflake に接続するための Snowflake CLI、SnowSQL、およびその他のクライアント（コネクター、ドライバーなど）。
* Snowflake エコシステムを構成するサードパーティのアプリケーションとサービス。

Snowflake に接続する際のアカウント識別子の使用/構造:

* Snowflake UI へのサインイン URL: &lt;orgname&gt;-&lt;account_name&gt;.snowflakecomputing.com
* クライアント、ドライバー、またはライブラリを Snowflake に接続するための設定: &lt;orgname&gt;-&lt;account_name&gt;

上記の例で見られるように、アカウント識別子は 2 つの主要なコンポーネントで構成されています:

1. &lt;orgname&gt; は Snowflake 組織の名前です。組織はビジネスエンティティが所有するアカウントをリンクする Snowflake オブジェクトです。
2. &lt;account_name&gt; は組織内のアカウントの一意の名前です。新しいアカウントを作成するときにアカウント名を指定しますが、[変更](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts-rename)することもできます。

組織の &lt;orgname&gt; の命名方法は 2 つあります:

1. **カスタム名:** Snowflake のアカウントチームと初期セットアップ活動を直接進めた場合、Snowflake は組織のカスタム名を割り当てることができます。システム生成された名前をカスタマイズ/変更するために Snowflake サポートに連絡することもできます。
2. **システム生成:** セルフサービスオプションを使用して最初の Snowflake アカウントが作成された場合、グローバルに一意の組織名が自動的に生成されます（例: XY12345、AB98765）

このステップでは、使用したい組織名のタイプ（システム生成またはカスタム）を決定します。上述のように、組織名が確立され、ユーザーとシステムがアカウント識別子と URL を使用して Snowflake に安全に接続すると、変更が必要なすべての場所で組織名を変更することは困難です。このため、事前にこの決定を検討することが重要です！

**注記**: このステップは組織名のみに焦点を当てています。アカウントとその他のオブジェクト命名規則の確立と管理方法については、後続のステップで説明します！

## **オプションの詳細**

### 組織名をカスタマイズする

Snowflake のアカウントチームと初期セットアップ活動を直接進めた顧客の場合、Snowflake がすでに組織のカスタム名を割り当てている可能性があります。システム生成された名前があり、カスタムの人間が読める名前に変更したい場合は、[Snowflake サポートに連絡する](https://docs.snowflake.com/en/user-guide/admin-account-identifier#organization-and-account-names)か、アカウントチームに連絡してカスタム組織名を要求してください。適切な利用可能な名前を見つけるためにサポートします。

**例**
URL: https://acme-prod.snowflakecomputing.com

* 組織名 = ACME（カスタム）
* アカウント名 = prod — カスタム組織名の場合、組織名がすでに URL のコンテキストを提供しているため、アカウント名をシンプルにできます

**カスタム組織名のガイダンス:**

* このカスタム名は、Snowflake の他のすべての組織全体でユニークでなければなりません。
* 名前は文字で始まり、文字と数字のみを含むことができます。
* 名前にはアンダースコアや他の区切り文字を含めることができません。
* 名前は簡潔（3〜8 文字）である必要があります。

✅ **この戦略を選ぶ場合:**

* 顧客に Snowflake アカウントのブランドを示す場合。例: ACME vs XY12345
* URL をより簡潔で読みやすくする場合

❌ **このアプローチを避ける場合:**

* URL における組織の透明性が不要または望ましくない場合

### システム生成された組織名を使用する

プライバシーまたはシンプルさのために、システム生成された組織名（例: XY12345）を保持できます。アカウント名自体は引き続き説明的にできます。

**例**
URL: https://xy12345-prod.snowflakecomputing.com

* 組織名 = XY12345（システム生成）
* アカウント名 = prod — システム生成された組織名でもアカウント名を説明的にできます

✅ **このアプローチを選ぶ場合:**

* URL における組織名の透明性が不要または望ましくない場合
* デフォルトのシステム生成された名前を保持することを好む場合

❌ **代わりにカスタム名を検討する場合:**

* ブランディングのために URL に組織名を表示したい場合
* より読みやすくプロフェッショナルな URL が欲しい場合

## **追加情報**

* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier) — URL における組織名とアカウント名の理解
* [Organizations](https://docs.snowflake.com/en/user-guide/organizations) — Snowflake 組織の概要
* [Renaming an Account](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts-rename) — アカウント名の変更方法

### 設定の質問

#### あなたの Snowflake 組織名は何ですか？（`snowflake_org_name`: text）
Snowflake 組織名はアカウント URL と接続識別子の最初の部分です。これはすべてのアカウント識別子の必須コンポーネントです。
  **組織名の見つけ方:**
  現在の Snowflake URL を確認してください。組織名はダッシュの前の部分です:
  * https://\*\*ACME\*\*-prod.snowflakecomputing.com → 組織名は ACME
  * https://\*\*XY12345\*\*-prod.snowflakecomputing.com → 組織名は XY12345
* **組織名のタイプ:**
  * **カスタム名:** Snowflake から要求された ACME や INITECH のような人間が読める名前。より良いブランディングとより読みやすい URL を提供します。
  * **システム生成:** セルフサービスのサインアップ時に自動的に作成された XY12345 や AB98765 のような自動割り当ての英数字コード。URL における組織名の透明性が不要または望ましくない場合、企業は通常この名前を保持します。
* **カスタム名の要求方法:** システム生成された名前がありそれを変更したい場合は、[Snowflake サポートに連絡する](https://community.snowflake.com/s/article/How-To-Submit-a-Support-Case-in-Snowflake-Lodge)かアカウントチームに連絡してください。カスタム名はグローバルにユニークで、文字で始まり、文字と数字のみを含む必要があります。
  **追加情報:**
  * [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)

#### すべてのアカウント名に追加するプレフィックス（ある場合）は何ですか？（`account_name_prefix`: text）
アカウント名プレフィックスは、一貫性と組織識別のためにすべてのアカウント名の先頭に追加されるオプションの文字列です。

**プレフィックスを使用するとき:**
* 組織名がシステム生成（例: `XY12345`）で、アカウント名に会社名を表示したい場合
* すべてのアカウントにわたって一貫した命名を強制したい場合
* Snowflake を共有する複数の組織またはビジネスユニットがあり、差別化が必要な場合

**プレフィックスあり例:**
* プレフィックス: `acme`
* アカウント名は: `acme_prod`、`acme_dev`、`acme_finance` になります
* URL: `https://XY12345-acme_prod.snowflakecomputing.com`

**プレフィックスなし例:**
* アカウント名: `prod`、`dev`、`finance`
* URL: `https://ACME-prod.snowflakecomputing.com`

**推奨事項:**
* **カスタム組織名**（`ACME` など）がある場合、アイデンティティがすでに URL にあるため、通常プレフィックスは不要です
* **システム生成された名前**がある場合は、プレフィックスとして会社名の略称の使用を検討してください
* プレフィックスは短く（3〜8 文字）、アンダースコアなしにしてください

**アカウント名プレフィックスを使用しない場合は `NONE` を入力してください。**

**追加情報:**
* [Account Identifiers](https://docs.snowflake.com/en/user-guide/admin-account-identifier)
