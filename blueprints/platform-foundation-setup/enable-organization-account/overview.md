このステップでは、[**組織アカウント**](https://docs.snowflake.com/en/user-guide/organization-accounts)を有効にするかどうかを決定します。組織アカウントは、集中管理、統一請求、複数の Snowflake アカウントを作成および管理する機能を提供します。注記: 組織アカウントは [Snowflake エディション](https://docs.snowflake.com/en/user-guide/intro-editions) **Enterprise** 以上でのみ利用可能です（唯一のアカウントが Standard エディションの場合は利用できません）。

## **なぜこれが重要か？**

Enterprise 以上のアカウントエディションを持つすべての顧客が組織アカウントを作成することを強くお勧めします。最初はシングルアカウント戦略に傾いている場合でも、将来複数のアカウントを持つ可能性がある場合は、最初から組織アカウントを設定することが最善です。これにより、いくつかの主要な機能の集中化が可能になります。詳細については[ドキュメント](https://docs.snowflake.com/en/user-guide/organization-accounts)を参照してください。

## **主要な概念**

「組織」と組織アカウントは異なることに注意することが重要です:

* [**組織**](https://docs.snowflake.com/en/user-guide/organizations): 組織は、ビジネスエンティティが所有するアカウントをリンクする Snowflake オブジェクトです。
  * **組織名** = [アカウント識別子](https://docs.snowflake.com/en/user-guide/admin-account-identifier)に表示されるビジネスエンティティの名前
* [**組織アカウント**](https://docs.snowflake.com/en/user-guide/organization-accounts): 複数の Snowflake アカウントを監視および管理するための集中管理機能を提供する特殊なタイプのアカウント。他のアカウントを管理する ORGADMIN 権限を持ちます。
  * **組織アカウント名** = 組織アカウントの名前

## **決定に基づく次のステップ**

ここでの決定は、すべての後続の設定ステップが実行される場所を決定します:

**組織アカウントを作成する場合:**

* 次のステップは、現在の（初期）アカウントから組織アカウントを作成することです
* その後、**新しい組織アカウントに切り替えます**
* このワークフローの残りは**組織アカウントで**実行されます
* 初期アカウントは組織の通常のメンバーアカウントになります

**組織アカウントを作成しない場合:**

* すべての後続のステップは**現在のアカウント**で実行されます
* これは拡張の計画がないシングルアカウント戦略に適しています

## **前提条件**

* 組織アカウントを作成したい場合は、[Snowflake Enterprise エディション](https://docs.snowflake.com/en/user-guide/intro-editions)以上を選択またはアップグレードする必要があります

## **追加情報**

* [Organization Accounts](https://docs.snowflake.com/en/user-guide/organization-accounts) — 組織アカウントの概要と機能
* [ORGADMIN Role](https://docs.snowflake.com/en/user-guide/security-access-control-overview#orgadmin-role) — 権限と責任
* [Creating Accounts](https://docs.snowflake.com/en/user-guide/organizations-manage-accounts-create) — 組織内にアカウントを作成する方法
* [Snowflake Editions](https://docs.snowflake.com/en/user-guide/intro-editions) — Snowflake エディションのオプション

### 設定の質問

#### 組織アカウントを作成しますか？（`enable_org_account`: multi-select）
組織アカウントは、Snowflake 環境の集中管理機能を提供する特殊なアカウントです。

  **⚠️ 強い推奨事項: 組織アカウントを作成してください**
  シングルアカウント戦略を選択した場合でも、組織アカウントの作成を強くお勧めします。その理由は:
  * **将来に向けた対策:** 後でアカウントを追加する可能性がある場合、組織アカウントがすでに設定されていれば、拡張がシームレスになります
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
