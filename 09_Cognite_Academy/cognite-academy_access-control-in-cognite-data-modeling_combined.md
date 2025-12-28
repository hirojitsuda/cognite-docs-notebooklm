# Access Control in Cognite Data Modeling

Generated from: Cognite Academy (Access Control in Cognite Data Modeling)
Generated at: 2025-12-29 07:55:28

---

================================================================================

<!-- SOURCE_START: 0_Access Control in Cognite Data Modeling.md -->
## File: 0_Access Control in Cognite Data Modeling.md

---
title: "Access Control in Cognite Data Modeling"
source: "https://learn.cognite.com/access-control-in-cognite-data-modeling"
created: 2025-11-03
description: "Learn how to manage access control in data modeling, focusing on space-based permissions, ACLs, and secure handling of instances, schemas, and edge relations."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://cc.sj-cdn.net/instructor/hhi6o3bc6ve-cognite-academy/themes/mb9vgd2babsm/header-logo.1660031198.png"
---

# Access Control in Cognite Data Modeling

[About this course](https://learn.cognite.com/#)

## About this course

**このコースへようこそ：How to populate your data model (advanced)（データモデルをどのように取り込むか（上級））**

このコースでは、データモデリング内でアクセス制御を管理する方法を学習します。**space-based permissions**（スペースベースの権限）と**Access Control Lists (ACLs)**（アクセス・コントロール・リスト）の効果的な使用に焦点を当てます。インスタンスとスキーマの読み取りおよび書き込み権限を定義し、強制する方法を探求し、安全で構造化されたデータモデリングを確保します。コース終了時には、**autocreation limitations**（自動作成の制限）、**edge relations**（エッジ関係）、および様々なスペースにわたる権限管理についての理解が得られます。

### By the end of this course, you will be able to:

このコースを終了すると、以下のことができるようになります：

- **Space-based controls**（スペースベースのコントロール）を使用して、データモデリングでユーザー権限を効果的に管理する方法を学習
- **ACLs**がデータモデルインスタンスとスキーマの読み取りおよび書き込み権限を管理する上での役割の概念を理解
- 異なるアクションとスペースに対して、特定のアクセスと広範なアクセスを設定する方法を学習
- インスタンスの自動作成とスペース間のエッジ関係を管理するための制限と要件を理解

### Who should take this course?

**このコースを受講すべき人：**

**Cognite Data Fusion**におけるデータモデリング機能について詳しく知りたい方であれば、どなたでも受講できます。

### Instructor

**インストラクター：**

このコースは**Cognite Academy**（コグナイト・アカデミー）によって開発されました。

![[images/7fb75a750e0118273463803ee0bbe57f_MD5.png]]

**Arild Eide**（アリルド・エイデ）  
Technical Program Manager（テクニカル・プログラム・マネージャー）

### コースの概要

このコースでは、以下の重要なトピックをカバーします：

1. **Space-based permissions**：スペースベースの権限管理
2. **Access Control Lists (ACLs)**：アクセス制御リストの使用
3. **Read and write permissions**：読み取りおよび書き込み権限の管理
4. **Autocreation limitations**：自動作成の制限と要件
5. **Edge relations**：エッジ関係の管理
6. **Permissions management**：様々なスペースにわたる権限管理

### 学習目標

このコースを修了することで：

- データモデリングにおけるアクセス制御の基本概念を理解
- スペースベースの権限管理の実践的なスキルを習得
- ACLsを使用した詳細な権限設定の方法を獲得
- 安全で構造化されたデータモデリング環境を構築できるようになる
- インスタンスとスキーマの両方に対する読み取りおよび書き込み権限を効果的に管理できるようになる

### 期待される成果

- **安全なデータモデリング環境**の構築
- **細かい権限制御**の実装
- **複雑なグラフ構造**の管理
- **組織内の様々なロール**に合わせたアクセス制御の設定

<!-- SOURCE_END: 0_Access Control in Cognite Data Modeling.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-1_Welcome.md -->
## File: 1-1_Welcome.md

---
title: "Welcome"
source: "https://learn.cognite.com/access-control-in-cognite-data-modeling/2031078"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Welcome

**コースへようこそ！**

このコースでは、データモデリング内でアクセス制御を管理する方法を学習します。**space-based permissions**（スペースベースの権限）と**Access Control Lists (ACLs)**（アクセス・コントロール・リスト）の効果的な使用に焦点を当てます。インスタンスとスキーマの読み取りおよび書き込み権限を定義し、強制する方法を探求し、安全で構造化されたデータモデリングを確保します。コース終了時には、**autocreation limitations**（自動作成の制限）、**edge relations**（エッジ関係）、および様々なスペースにわたる権限管理についての理解が得られます。

## コースの概要

### 学習目標

このコースを終了すると、以下のことができるようになります：

- **Space-based controls**（スペースベースのコントロール）を使用して、データモデリングでユーザー権限を効果的に管理する方法を学習
- **ACLs**がデータモデルインスタンスとスキーマの読み取りおよび書き込み権限を管理する上での役割の概念を理解
- 異なるアクションとスペースに対して、特定のアクセスと広範なアクセスを設定する方法を学習
- インスタンスの自動作成とスペース間のエッジ関係を管理するための制限と要件を理解

### 重要な概念

このコースでは、以下の重要な概念をカバーします：

- **Space-based permissions**：スペースベースの権限管理
- **Access Control Lists (ACLs)**：アクセス制御リスト
- **Read and write permissions**：読み取りおよび書き込み権限
- **Autocreation limitations**：自動作成の制限
- **Edge relations**：エッジ関係
- **Permissions management**：権限管理

### コースの構成

1. **Welcome**：コースの紹介と概要
2. **Control access to a graph**：グラフへのアクセス制御の管理方法
3. **Summary**：学習内容のまとめ

### 期待される成果

このコースを修了することで：

- データモデリングにおけるアクセス制御の基本概念を理解
- スペースベースの権限管理の実践的なスキルを習得
- ACLsを使用した詳細な権限設定の方法を獲得
- 安全で構造化されたデータモデリング環境を構築できるようになる

<!-- SOURCE_END: 1-1_Welcome.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-2_Control access to a graph.md -->
## File: 1-2_Control access to a graph.md

---
title: "Control access to a graph"
source: "https://learn.cognite.com/access-control-in-cognite-data-modeling/2031079"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Control access to a graph

このセクションでは、データモデリング内で異なるユーザーやアクションに対する権限を効果的に管理する方法を詳しく探求します。**spaces**（スペース）に基づくアクセス制御の重要な役割を理解し、**ACLs**（アクセス・コントロール・リスト）、**autocreation limitations**（自動作成の制限）、**edge relations**（エッジ関係）の複雑さを理解します。

*詳細情報については、以下のボタンをクリックしてください。*

## Space-Based Control

### 概要

データモデリングにおけるアクセス制御は**spaces**（スペース）に基づいており、スキーマとインスタンスの読み取りおよび書き込み権限を個別に管理できます。この**dual control**（二重制御）により、様々なユーザーのニーズとロールに合わせたアクセスを確保します。

### 重要なポイント

- **Space-based access control**：アクセス制御はスペースに基づいて管理
- **Separate management**：読み取りと書き込みの権限を個別に管理可能
- **Tailored access**：異なるユーザーのニーズとロールに合わせたアクセス設定

## Controls for Instances and Schemas

### 概要

**dataModelInstances**の**ACLs**は、**nodes**（ノード）と**edges**（エッジ）へのアクセスを制御します。一方、**dataModels**の**ACLs**は、**spaces**、**containers**（コンテナ）、**views**（ビュー）、**data models**（データモデル）を含むスキーマへのアクセスを管理します。

### アクセス制御の種類

- **Instance control**（インスタンス制御）：**dataModelInstances**の**ACLs**が**nodes**と**edges**へのアクセスを管理
- **Schema control**（スキーマ制御）：**dataModels**の**ACLs**がスキーマ要素へのアクセスを管理
  - **Spaces**
  - **Containers**
  - **Views**
  - **Data models**

## Read and Write Actions with Scopes

### 概要

**ACLs**は**READ**（読み取り）と**WRITE**（書き込み）の両方のアクションをサポートし、**scopes**（スコープ）によって詳細なアクセス制御を提供します。

### Scopeの種類

- **'all'**：すべてのスペース内のすべてのリソースへのアクセス
- **'space'**：指定されたスペース内のリソースへのアクセス

### 重要なポイント

- **READ action**：データの読み取り権限
- **WRITE action**：データの書き込み権限
- **Scope-based control**：スコープベースの詳細なアクセス制御

## Example ACL Configuration

### Specific vs. Broad Access

この例では、すべてのスペース内のインスタンスへの読み取りアクセスを提供し、特定のスペース（**space1**）内のインスタンスを変更/削除し、すべてのスペースにわたってスキーマを読み取りおよび変更する方法を示しています。これにより、権限の詳細度を実証します。

![[images/74887ad468781a876f86bebc10c52c05_MD5.png]]

### 設定例の説明

この設定例では以下のことが示されています：

- **Broad read access**：すべてのスペースでのインスタンス読み取り
- **Specific write access**：特定のスペース（space1）でのインスタンス変更/削除
- **Schema access**：すべてのスペースでのスキーマ読み取りと変更

## Write Access Requirement

### 自動作成の制限

**Autocreation**（自動作成）は、書き込みアクセス権限があるスペースでのみ実行できます。これにより、自動作成が意図されたアクセス制限をバイパスしないことを確保し、データの整合性を維持します。

### 重要なポイント

- **Write access dependency**：自動作成には書き込みアクセスが必要
- **Access compliance**：アクセスポリシーへの準拠を確保
- **Data integrity**：データの整合性を維持

## Necessity of Read Access

### エッジとリレーションの要件

異なるスペースのノードを指すエッジや**direct relations**（直接関係）を設定するには、ターゲットスペースへの読み取りアクセスが必須です。この要件により、安全で整然としたグラフ構造を確保し、データモデリングの整合性と正確性を維持します。

### 重要なポイント

- **Read access requirement**：ターゲットスペースへの読み取りアクセスが必要
- **Edge pointing**：エッジを他のスペースのノードに向ける
- **Direct relations**：直接関係の設定
- **Graph structure security**：安全で整然としたグラフ構造の確保
- **Data accuracy**：データの正確性の維持

### セキュリティ上の考慮事項

- **Secure graph structure**：安全なグラフ構造の維持
- **Permission-based management**：権限ベースの管理
- **Data integrity**：データの整合性の確保

<!-- SOURCE_END: 1-2_Control access to a graph.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-3_Summary.md -->
## File: 1-3_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/access-control-in-cognite-data-modeling/2031080"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Summary

このコースでは、データモデリングにおけるアクセス制御の重要な概念について学習しました。以下にまとめます。

## 1. Understanding Access in Data Modeling

### 重要なポイント

- **Space-Based Access Control**（スペースベースのアクセス制御）：アクセス制御は**spaces**に依存し、スキーマとインスタンスの読み取りおよび書き込み権限を個別に管理できます
- **Dual Control System**（二重制御システム）：このシステムにより、権限が異なるユーザーロールとニーズに適切に調整されます
- **Balancing Access and Security**（アクセスとセキュリティのバランス）：データモデリングにおいて運用の柔軟性とセキュリティの両方を維持するために重要です

### 概要

- **Spaces**に基づく権限管理
- スキーマとインスタンスの個別管理
- 柔軟性とセキュリティのバランス

## 2. Access Control Lists (ACLs)

### 重要なポイント

- **Instance and Schema Control**（インスタンスとスキーマの制御）：**dataModelInstances**の**ACLs**が**nodes**と**edges**を管理し、**dataModels**の**ACLs**が**spaces**、**containers**、**views**、**data models**を制御します
- **Read and Write Permissions**（読み取りおよび書き込み権限）：包括的なアクセス管理のために、様々なスコープを持つ**READ**と**WRITE**アクションの両方をサポートします
- **Scope Variations**（スコープのバリエーション）：すべてのスペースにわたるユニバーサルアクセスのための**'all'**、または特定のスペースでのターゲット権限のための**'space'**のオプションを含みます

### ACLの機能

- **Instance control**：インスタンスへのアクセス制御
- **Schema control**：スキーマへのアクセス制御
- **Flexible scopes**：柔軟なスコープ設定

## 3. Granularity in Permissions

### 重要なポイント

- **Tailored Access Settings**（調整されたアクセス設定）：すべてのスペースに対する広範な権限、または個々のスペースに対するより詳細な権限を指定する能力
- **Flexible User Role Application**（柔軟なユーザーロール適用）：組織内の様々なユーザーロールに異なるアクセスレベルを適用するのに適しています
- **Example ACL Configuration**（ACL設定の例）：詳細な権限管理のために、異なるスコープで読み取りおよび書き込みアクセスを設定する方法を実証

### 権限の詳細度

- **Broad permissions**：広範な権限設定
- **Specific permissions**：詳細な権限設定
- **Role-based access**：ロールベースのアクセス制御

## 4. Autocreation with Access Restrictions

### 重要なポイント

- **Write Access Dependency**（書き込みアクセスの依存性）：スペース内のインスタンスの自動作成は、書き込みアクセスを持つことに依存し、アクセスポリシーへの準拠を確保します
- **Controlled Autocreation**（制御された自動作成）：この制限により、不正な自動作成を防ぎ、データの整合性とセキュリティを維持します
- **Impact on Data Modeling**（データモデリングへの影響）：制御され、安全なデータモデリング実践を維持するために不可欠です

### 自動作成の要件

- **Write access required**：自動作成には書き込みアクセスが必要
- **Access policy compliance**：アクセスポリシーへの準拠
- **Data integrity**：データの整合性の維持

## 5. Managing Edge and Relation Permissions

### 重要なポイント

- **Read Access for Edge Pointing**（エッジポイントのための読み取りアクセス）：異なるスペースのノードに向けるエッジやリレーションには、ターゲットスペースへの読み取りアクセスが必須です
- **Security in Graph Structure**（グラフ構造のセキュリティ）：安全で整然としたグラフを確保し、データの正確性を維持します
- **Permission-Based Graph Management**（権限ベースのグラフ管理）：複雑なグラフ関係を管理する際のアクセス制御の重要性を強調します

### エッジとリレーションの管理

- **Target space read access**：ターゲットスペースへの読み取りアクセスが必要
- **Secure graph structure**：安全なグラフ構造の維持
- **Complex relationship management**：複雑な関係の管理

## まとめ

このコースでは、以下の重要な概念を学習しました：

1. **Space-based access control**の基本概念
2. **ACLs**による詳細な権限管理
3. 権限の詳細度と柔軟な設定方法
4. **Autocreation**の制限と要件
5. **Edge and relation**の権限管理

これらの概念を理解することで、安全で構造化されたデータモデリング環境を構築し、効果的にアクセス制御を管理できるようになります。

<!-- SOURCE_END: 1-3_Summary.md -->

================================================================================

