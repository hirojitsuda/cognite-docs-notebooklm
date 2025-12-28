# Cognite Data Modeling

Generated from: Cognite Academy (Cognite Data Modeling)
Generated at: 2025-12-29 07:55:28

---

================================================================================

<!-- SOURCE_START: 0_Cognite Data Modeling.md -->
## File: 0_Cognite Data Modeling.md

---
title: "Cognite Data Modeling"
source: "https://learn.cognite.com/cognite-data-modeling"
created: 2025-11-03
description: "Explore the fundamental concepts, practical applications, and advanced techniques for effectively organizing, and managing industrial data."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://cc.sj-cdn.net/instructor/hhi6o3bc6ve-cognite-academy/themes/mb9vgd2babsm/header-logo.1660031198.png"
---

# Cognite Data Modeling

[Content](https://learn.cognite.com/#)

## Course Overview 4 hrs (Show All)

---

[About this course](https://learn.cognite.com/#)

## About this course

![[images/bcb29c1aea1e239d24a70ba86a2233da_MD5.png]]

**Cognite Data Fusion (CDF)**（コグナイト・データ・フュージョン・シーディーエフ）におけるデータモデリングの包括的なコースへようこそ。このコースでは、産業データを効果的に整理・管理するための基本概念、実践的アプリケーション、高度なテクニックを探求します。データモデリングが初めての方でも、理解を深めたい方でも、このコースはCDFでデータモデリングの力を活用するための知識とスキルを提供します。

### Course Overview（コース概要）

CDFにおけるデータモデリングは、複雑なデータ関係の理解を簡素化します。データモデリングの核心的な目的は、現実世界のエンティティとそれらのシステム内での相互作用を、どのように認識するかを整理し、標準化することです。これを**Cognite Data Modeling**（コグナイト・データモデリング）を通じて実現します。これは、モデルの構築、データの取り込み、モデルのクエリを含み、最終的に**Cognite Data Fusion (CDF)**で産業知識を強化します。

### 学習目標

このコースを終了すると、**Cognite Data Fusion (CDF)**におけるデータモデリングとその基本概念について、しっかりとした理解が得られます。シンプルなモデルを構築し、データを取り込み、基本的なクエリを実行する方法を学習します。さらに、このモデルをCDF内でさらにどのように適用できるかについての洞察を得られます。

### Key Concepts（主要概念）

このコースでは、以下の**Data Modeling**（データモデリング）の主要概念をカバーします：

- **Data Models**（データモデル）
- **Spaces**（スペース）
- **Containers**（コンテナ）
- **Views**（ビュー）
- **Instances**（インスタンス）
- **REST API**と**GraphQL API**（アールイーエスティー・エーピーアイとグラフキューエル・エーピーアイ）

### Who should take this course?（このコースを受講すべき人）

**Cognite Data Fusion**におけるデータモデリング機能について詳しく知りたい方であれば、どなたでも受講できます。

### Instructor（インストラクター）

このコースは**Cognite Academy**（コグナイト・アカデミー）によって開発されました。

![[images/eb5604fc8d5f73a4567534661cb6b793_MD5.png]]

**Elka Sierra**（エルカ・シエラ）  
Lead Product Manager - Data Modeling（リード・プロダクト・マネージャー - データモデリング）

### コースの構成

このコースでは、以下の内容を学習します：

1. **基本概念の理解**
   - データモデリングの基礎
   - CDFにおけるデータモデリングの役割

2. **実践的なアプリケーション**
   - モデルの構築方法
   - データの取り込みプロセス
   - クエリの実行

3. **高度なテクニック**
   - 複雑なデータ関係の管理
   - 産業知識の強化方法

### 期待される成果

このコースを修了することで：

- データモデリングの基本概念を理解
- CDFでのデータモデリングの実践的なスキルを習得
- 産業データを効果的に整理・管理する能力を獲得
- CDFアプリケーションでデータモデルを活用できるようになる

<!-- SOURCE_END: 0_Cognite Data Modeling.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-1_What is Cognite Data Modeling.md -->
## File: 1-1_What is Cognite Data Modeling.md

---
title: "What is Cognite Data Modeling?"
source: "https://learn.cognite.com/cognite-data-modeling/1875728"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# What is Cognite Data Modeling?

- **Data modeling in Cognite Data Fusion** は、組織が産業上の現実を表現するための表現力豊かでスケーラブルな「データモデル」を構築し、ソースシステム全体でデータの価値を解き放つことを可能にします。

## A data model

- **A data model** は、データオブジェクトを整理し、現実世界のエンティティのプロパティとそれらが相互にどのように関連するかを標準化します。
- 「データモデル」は、**ontology**（オントロジー）、**knowledge graph**（ナレッジグラフ）、または「業界標準」の核であり、「データサイエンスモデル」、「モバイルアプリ」、および「ウェブアプリ」のようなソリューションを構築する上で重要です。

---

- 新しい都市を計画している建築家を想像してみてください。
- 異なる建物や道路がどのように接続するかを理解するために青写真が必要なように、「データモデリング」は、デジタル環境で「データオブジェクト」がどのように相互作用し、関連するかを示す青写真を提供します。
- この青写真、つまり「データモデル」は、開発者やアナリストのためのガイドとして機能し、石油やガスのような産業における複雑なデータ関係を理解するのに役立ちます。これにより、より効率的な資源管理につながる可能性があります。

![[images/5ecef6390ba9446d3087e1bf7387a0f4_MD5.jpg]]

---

## Evolution of Data Modeling in Cognite Data Fusion

- 「Cognite Data Fusion」の「データモデリング」機能は、過去数年間で著しく進化しました。
- 当初、私たちは「asset-centric data model」（資産中心のデータモデル）に焦点を当てていました。これは効果的ではあったものの、多様な業界ワークフローに対応するには柔軟性に欠けていました。
- これを解決するために、「Flexible Data Model」（フレキシブルデータモデル）を開発しました。これにより多くの制約が取り除かれましたが、その広範な柔軟性ゆえに新たな複雑さが生じました。

---

### Introducing Cognite Data Models

- 私たちの経験から、最良のアプローチはこれら二つの極端な中間にあることが示されています。
- この改良は「flexible data model」を基盤とし、柔軟性とシンプルさのバランスを取り、多様な業界のユースケースのニーズに合致するようにしています。
- **Cognite Data Models** は、以下の要素を組み合わせたバランスの取れたアプローチを提供します。
    - **Structure:** 「Core data models」（コアデータモデル）は、産業データのための標準化された構成要素を提供し、より専門的なモデルの基礎を形成します。
    - **Industry-Specific Tailoring:** 「Industry data models」（業界データモデル）は、「core model」を拡張し、特定の業界要件（例：プロセス、製造）に対応します。
    - **Customizable Solutions:** 「Solution data models」（ソリューションデータモデル）は、特定のアプリケーションに合わせて「データモデル」をカスタマイズできるため、独自のニーズに完全に合致します。

---

## Benefits of Cognite Data Models

- 「Cognite Data Models」の利点は以下の通りです。
    - **Faster Time to Value:** 「core」および「industry data models」の事前構築されたモデルを活用して、迅速に開始できます。
    - **Reduced Complexity:** 事前作成されたコンテナとビューによる構造化モデリングにより、データアクセスと分析が簡素化されます。
    - **Flexibility and Adaptability:** 業界ソリューションまたはカスタムソリューションを使用して、「データモデル」を特定のニーズに合わせて調整できます。
    - **Future-Proofing:** バージョン管理により、既存のアプリケーションに影響を与えることなくモデルを進化させることができます。

<!-- SOURCE_END: 1-1_What is Cognite Data Modeling.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-2_Explore Cognite Data Models.md -->
## File: 1-2_Explore Cognite Data Models.md

---
title: "Explore Cognite Data Models"
source: "https://learn.cognite.com/cognite-data-modeling/2043259"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
Cogniteは、産業データに関する構造化され、柔軟性があり、文脈化された**knowledge graph**の構築を開始するために、すぐに使える**data models**を提供します。

1.  **core data model**は、より専門的なモデルの基礎となる標準化されたビルディングブロックを提供します。
2.  **industry data models**は、**processing industry**などの特定の産業要件に対応するために、**core model**を拡張します。
3.  **Custom data models**は、ユースケース、ソリューション、またはアプリケーションに特化したデータに関する視点を提供します。Cognite **data models**から拡張することも、ゼロから新しいモデルを作成することもできます。

![[images/f7c16576e15899ad1bf21fff92cf65e4_MD5.png]]

cognite\_data\_models

<!-- SOURCE_END: 1-2_Explore Cognite Data Models.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-3_Overview of Data Modeling phases in Cognite Data Fusion.md -->
## File: 1-3_Overview of Data Modeling phases in Cognite Data Fusion.md

---
title: "Overview of Data Modeling phases in Cognite Data Fusion"
source: "https://learn.cognite.com/cognite-data-modeling/1875662/scorm/i3zje97s8tuk"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
[[bc8eedb47c6deb3fe4b7a42bee6764ed_MD5.jpg|Open: Pasted image 20251103093354.png]]
![[images/bc8eedb47c6deb3fe4b7a42bee6764ed_MD5.jpg]]
[[e497059415329ec1e5b6bf5d317fd7ca_MD5.jpg|Open: Pasted image 20251103093411.png]]
![[images/e497059415329ec1e5b6bf5d317fd7ca_MD5.jpg]]
[[4ca3dc3d57fa941b4c0e72710e0dad00_MD5.jpg|Open: Pasted image 20251103093327.png]]
![[images/4ca3dc3d57fa941b4c0e72710e0dad00_MD5.jpg]]



<!-- SOURCE_END: 1-3_Overview of Data Modeling phases in Cognite Data Fusion.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-4_Example.md -->
## File: 1-4_Example.md

---
title: Example
source: https://learn.cognite.com/cognite-data-modeling/1875663/scorm/116as3s04ooeg
created: 2025-11-03
description:
tags:
  - CDF
  - CogniteDataFusion
image:
---
[[79af3a3ef7d0aa063658bd5557e4079b_MD5.jpg|Open: Pasted image 20251103093504.png]]
![[images/79af3a3ef7d0aa063658bd5557e4079b_MD5.jpg]]

<!-- SOURCE_END: 1-4_Example.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-5_Summary.md -->
## File: 1-5_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/cognite-data-modeling/1876095"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Summary

### 1. What is Cognite Data Modeling?
*   データモデリング（Data Modeling）は、データの関係性を視覚化し理解することを簡素化します。これは建物の設計図を作成するようなもので、この場合はデータのためのものです。
*   初期の『アセット中心モデル（asset-centric models）』は効果的でしたが、多様な業界ワークフローに対応するには硬直的すぎました。『柔軟なデータモデル（Flexible Data Model）』は自由度を高めましたが、課題がありました。
*   コグナイトのデータモデル（Cognite Data Models）は、構造化された『コアモデル（core models）』、『業界固有の拡張（industry-specific extensions）』、および『カスタマイズ可能なソリューション（customizable solutions）』とのバランスを取っています。

#### Benefits of Cognite Data Models
*   『事前構築済みモデル（pre-built models）』による迅速な実装。
*   『構造化コンテナ（structured containers）』による複雑さの軽減。
*   カスタムニーズに対応する柔軟性。
*   『バージョン管理（version control）』による将来性。

### 2. Explore Cognite Data Models
*   『コアモデル（Core models）』は標準的な構成要素を提供します。
*   『業界モデル（Industry models）』は、特定のセクター向けに『コアモデル（core models）』を拡張します。
*   『カスタムモデル（Custom models）』は、特定のユースケースやアプリケーションに合わせたソリューションを提供します。

### 3. Overview of Data Modeling Phases
*   **Building Models**: 詳細でスケーラブルな『データ（data）』表現を作成します。
*   **Ingesting Data**: モデルに実際の『運用データ（operational data）』を入力します。
*   **Querying Models**: これらのモデル内の『データ（data）』を抽出・分析します。

<!-- SOURCE_END: 1-5_Summary.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-1_Cognite's Data Modeling Services basic concepts.md -->
## File: 2-1_Cognite's Data Modeling Services basic concepts.md

---
title: "Cognite's Data Modeling Services: basic concepts"
source: "https://learn.cognite.com/cognite-data-modeling/1876094"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Cognite's Data Modeling Services: basic concepts

データサイエンティストやエンジニアであるあなたは、大規模で多面的なデータ環境を扱う際に、堅牢な構造と動的な相互作用の必要性によく精通していることでしょう。

**Cognite Data Modeling Services**は、**spaces**、**instances**、**containers**、**views**、**data models**の5つの基本的な概念を包含しています。これらの要素は、複雑な産業知識グラフを構築し、ナビゲートするための構成要素として機能します。

![[images/88bd000d61696186c9e0604ff67f854c_MD5.png]]

*   **Space**: これらは、特定のデータやモデルが存在する個別の領域、つまり「部屋」と考えてください。
*   **Data Model**: データの編成方法と関連付け方を定義する設計図または構造。
*   **View**: データを見るための、あるいはデータと相互作用するためのカスタムの視点や方法。
*   **Container**: `Space`には`Containers`が含まれます。`Containers`はプロパティのグループです。
*   **Node**: オブジェクト（油田のポンプなど）を表す基本的な要素。
*   **Edge**: `Nodes`を接続する線で、関係（ポンプ間のパイプラインなど）を示します。
*   **externalId**: インスタンスをその`space`内で一意に識別するIDです。これは、インスタンスを個別に参照し、管理するために不可欠です。
*   **startNode, endNode**: （`edges`の場合）`edge`が接続する開始`nodes`を指定し、関係の方向と性質を示します。

### REST APIs

**REST APIs**は、このエコシステムで重要な役割を果たし、これらの概念を効率的に作成、更新、削除するためのプログラム可能なインターフェースを提供します。これらは、あなたの専門知識が**Cognite's Data Modeling Services**フレームワーク内で実行可能な洞察に変換されるための導管となります。

<!-- SOURCE_END: 2-1_Cognite's Data Modeling Services basic concepts.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-2_Understanding instances in CDF.md -->
## File: 2-2_Understanding instances in CDF.md

---
title: "Understanding instances in CDF"
source: "https://learn.cognite.com/cognite-data-modeling/1876098"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Understanding instances in CDF

「**instance**」は、グラフ内の個々の要素を説明するために使用される一般的な用語であり、それが「node」（グラフ内の点）であるか、「edge」（2つのnode間の接続）であるかに関わらず使用されます。

*以下の各カードをクリックして詳細をご覧ください。*

## Nodes and Edges

### Node

「instance」が「node」である場合、それはエンティティまたはオブジェクトを表します。産業データでは、これはポンプ、センサーのような物理コンポーネント、または部署やプロセスのような概念的なエンティティである場合があります。

### Edge

「instance」が「edge」である場合、それは2つの「node」間の関係またはリンクを表します。これは、2つのバルブを接続するパイプラインや、センサー間の通信リンクなど、さまざまな種類の接続を示すことができます。

[[d0804398583e34acc28c8e3c5c05360b_MD5.jpg|Open: Pasted image 20251026195857.png]]
![[images/d0804398583e34acc28c8e3c5c05360b_MD5.jpg]]
## External ID

「External ID」は、各「instance」に割り当てられ、特定の「space」内で一意の識別子です。これにより、各要素（「node」であろうと「edge」であろうと）が一意に識別され、参照されることが保証されます。これは「**fully qualified format**」で指定できます。

「**Fully qualified format**」: 「fully qualified External ID」は、「instance」が属する「space」と割り当てられた識別子で構成されます。

*以下のボタンをクリックして詳細をご覧ください。*

### Why does the External ID have to be unique?

-   **「Avoids conflicts」**: 各「instance」がその「space」内で一意の「External ID」を持つことを保証することで、衝突や混乱を回避します。これはデータの整合性と正確なクエリにとって極めて重要です。
-   **「Enables referencing and linking」**: 一意のIDは、「node」間にリレーションシップを作成したり、システム内の特定の要素をクエリおよび更新したりする際に不可欠です。

### Constraints and Best Practices

-   **「Length Limitation」**: 「External ID」の最大長は255文字です。この制限により、IDが管理しやすく、システムパフォーマンスが過度に長い識別子によって妨げられないことが保証されます。

<!-- SOURCE_END: 2-2_Understanding instances in CDF.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-3_About views.md -->
## File: 2-3_About views.md

---
title: "About views"
source: "https://learn.cognite.com/cognite-data-modeling/1876105"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# About views

`Views`は、`graph`内の`data`をナビゲートおよび操作するための`logical schemas`を作成できる強力な構築物です。これらは、特定の`use cases`に合わせて調整された`data`の消費と入力のための合理化された方法を提供します。

---

### Use case 1: Creating logical schemas

`Views`には、`container properties`をマッピングするか、`connection properties`を作成することによって定義される`properties`のグループが含まれています。これらにより、`graph`における期待される関係を表現できます。

[[893296d71d4fd658e8630fb22f09aaa5_MD5.jpg|Open: Pasted image 20251026200539.png]]
![[images/893296d71d4fd658e8630fb22f09aaa5_MD5.jpg]]

### Use case 2: Queried data through views

`data`は、定義された`views`を介してクエリされ、`containers`から直接クエリされるわけではないことを覚えておくことが重要です。この抽象化層により、より柔軟で`tailored data retrieval`が可能になります。

### Use case 3: Mapped properties

`Views`は、異なる`containers`からの`properties`を`flat object`にマッピングすることを可能にし、整合性と明確性のために`properties`の名前を変更またはエイリアスすることを可能にします。例えば、`view`は、`Equipment`および`Pump` `containers`から、それぞれ`'manufacturer'`と`'maxPressure'`の`properties`を持つ`flat object`を作成する場合があります。

### Use case 4: Connection properties

これらの`properties`により、`graph`内の`nodes`間の期待される関係を記述できます。例えば、`'BasicPump'`に`data`を持つ`nodes`が`'Valve'` `nodes`への`'flows-to'` `edges`を持つことが期待されることを表現できます。この`metadata`は、永続化されると、特定の`view`を介して`instances`を表示する際に`related data`を取得することを可能にします。

[[42b914d56656e9159184b8cf13a17f27_MD5.jpg|Open: Pasted image 20251026200553.png]]
![[images/42b914d56656e9159184b8cf13a17f27_MD5.jpg]]

<!-- SOURCE_END: 2-3_About views.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-4_Relationships.md -->
## File: 2-4_Relationships.md

---
title: "Relationships"
source: "https://learn.cognite.com/cognite-data-modeling/2043261"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Relationships

1.  **Direct relationship**: ダイレクトな関係は、ノードまたはノードのリストへの参照を保持するプロパティであり、リレーショナルモデルにおける*foreign key*（外部キー）に似ています。
2.  **Reverse direct relationship**: リバースダイレクトな関係は接続プロパティです。RDR（Reverse Direct Relationships）はビューで定義され、包含ビュー内のデータを持つノードを指す参照を伴うダイレクトな関係が存在するという期待を表します。以下の例では、`Equipment`（機器）が`Manufacturer`（製造元）へのダイレクトな関係を持っています。関係プロパティは`Equipment`のプロパティの一部であるため、製造元ビューはリバースダイレクトな関係を記述できます。
    1.  `connectionType`: `single_reverse_direct_relation`または`multi_reverse_direct_relation`のいずれか。単一の関係を期待するか、複数のオブジェクトを期待するかによって異なります。

![[images/b30916eca5976795801f026bda90ba8c_MD5.png]]

<!-- SOURCE_END: 2-4_Relationships.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-5_Exploring nodes, edges and direct relations.md -->
## File: 2-5_Exploring nodes, edges and direct relations.md

---
title: "Exploring nodes, edges and direct relations"
source: "https://learn.cognite.com/cognite-data-modeling/1877836"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Exploring nodes, edges and direct relations

## Direct Relations

前のレッスン「Understanding Instances in CDF」で、あなたは**エッジ（edges）**について学びました。しかし、**ダイレクトリレーション（direct relations）**とは何で、**エッジ**とはどう違うのでしょうか？

| | **Direct Relations** | **Edges** |
| :--------------------------------------------------------------------------------------------- | :------- | :-------- |
| Can have properties of their own? (独自のプロパティを持つことができますか？) | No       | **Yes**   |
| Can be traversed recursively? (再帰的にたどることができますか？) | No       | **Yes**   |
| Can restrict which container the target node must have data in? (ターゲットノードがどのコンテナにデータを持つかを制限できますか？) | **Yes**  | No        |
| Cheap, supporting a large number of direct relations with minimal overhead? (オーバーヘッドが最小限で、多数のダイレクトリレーションをサポートし、低コストですか？) | **Yes**  | No        |
| Relatively costly, making direct relations a consideration for large quantities, as they count toward instance limits? (インスタンスの制限にカウントされるため、大量のデータの場合、ダイレクトリレーションは比較的高価になりますか？) | No       | **Yes**   |
| Can enforce that a set of edges form a tree or a Directed Acyclic Graph (DAG)? (一連のエッジがツリーまたは有向非巡回グラフ（DAG）を形成することを強制できますか？) | No       | **Yes**   |

## Understanding Type Node

グラフにおいて、**ノード（nodes）**は、物理的な**エンティティ（entities）**から、コメントや物理的な**エンティティ**の*タイプ*といった抽象的な**コンセプト（concepts）**まで、あらゆるものを表すことができます。すべての**インスタンス（instance）**は`type`プロパティを持ち、その意図された**タイプ（type）**を定義する**ノード**を指す**ダイレクトリレーション（direct relation）**です。

**タイプノード（Type nodes）**では、一般的な**ノード/エッジ（nodes/edges）**と**タイプノード**の間に明確な区別があります。**タイプノード**はそれ自体が物理的な**エンティティ**を表すのではなく、**エンティティ**の*タイプ*を表すためです。

**タイプノード**がどのように使用されるかについては、次のレッスンで詳しく学びます。

<!-- SOURCE_END: 2-5_Exploring nodes, edges and direct relations.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-6_Example how type nodes are used.md -->
## File: 2-6_Example how type nodes are used.md

---
title: "Example: how type nodes are used?"
source: "https://learn.cognite.com/cognite-data-modeling/1875672/scorm/2gy73un2rj4sy"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Example: how type nodes are used?

[[4673a64aacecca3149a7e1d2af243652_MD5.jpg|Open: Pasted image 20251026210753.png]]
![[images/4673a64aacecca3149a7e1d2af243652_MD5.jpg]]
[[3fcc980b9df39ee06e8971e6d17e7ac1_MD5.jpg|Open: Pasted image 20251026210857.png]]
![[images/3fcc980b9df39ee06e8971e6d17e7ac1_MD5.jpg]]
[[2751d82bdc70f493396a79a31df7ca64_MD5.jpg|Open: Pasted image 20251026210913.png]]
![[images/2751d82bdc70f493396a79a31df7ca64_MD5.jpg]]
[[5e4df15e1a766e5aa26d34aa7cd9565f_MD5.jpg|Open: Pasted image 20251026210929.png]]
![[images/5e4df15e1a766e5aa26d34aa7cd9565f_MD5.jpg]]
[[b958cafd8bbbbfb757f810e3e3b4735d_MD5.jpg|Open: Pasted image 20251026210947.png]]
![[images/b958cafd8bbbbfb757f810e3e3b4735d_MD5.jpg]]
[[ce2859dc95f09861750d69d63d63e3bc_MD5.jpg|Open: Pasted image 20251026211003.png]]
![[images/ce2859dc95f09861750d69d63d63e3bc_MD5.jpg]]
[[4a6649100a390b70539d12596d41fa57_MD5.jpg|Open: Pasted image 20251026211017.png]]
![[images/4a6649100a390b70539d12596d41fa57_MD5.jpg]]
#### Summary
**type nodes**がどのように使用されているかを、以下のインタラクティブなコンテンツで確認してください。`❮`および`❯`ボタンで移動するか、画面の任意の場所をクリックして次のスライドに進んでください。

<!-- SOURCE_END: 2-6_Example how type nodes are used.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 2-7_Summary.md -->
## File: 2-7_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/cognite-data-modeling/1876103"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Summary

### 1. Cognite's Data Modeling Services: basic concepts

*   **Spaces**: これらは、特定のデータやモデルが存在する個別の領域、つまり「部屋」と考えることができます。
*   **Instances**: 「Spaces」内のデータやオブジェクトの個々の出現です。
*   **Containers**: 関連する「Instances」や「Spaces」を保持するグループです。
*   **Views**: データを見る、操作するためのカスタムの視点や方法です。
*   **Data Models**: データがどのように整理され、関連付けられているかを定義する設計図または構造です。
*   **REST APIs Role**: これらは、これらの概念を作成、更新、または削除し、効果的に産業知識グラフを形成するためのツールです。

### 2. Understanding Instances in CDF

*   「Instances」には、グラフ内の「nodes」と「edges」が含まれます。
*   「Nodes」はエンティティを表し、「edges」は「nodes」間の関係を示します。
*   「space」、「externalId」、「type」などのコアプロパティは、「instances」全体で一貫して存在します。
*   「Edges」は複数の「spaces」にまたがることができ、関連する開始「node」と終了「node」に依存します。

### 3. External ID

*   「External IDs」は、「space」内の「instances」の一意の識別子です。
*   完全修飾形式（「space」を含む）により、明確な識別が保証されます。
*   一意の「ID」は、競合を防ぎ、参照を助け、「relationship」の作成をサポートします。
*   制約には、長さの制限とヌルバイトの非存在が含まれます。

### 4. Views - Establishing Logical Schemas

*   「Views」は、「container」プロパティから論理「schemas」を作成する手段です。
*   「views」を介したデータクエリにより、オーダーメイドの取得が可能です。
*   「properties」のマッピングと名前変更により、明確さと一貫性を実現します。

### 5. Relationships, Direct Relationships vs Edges

*   **Direct Relationship**: リレーショナルモデルにおける「foreign key」に似た、「node」または「nodes」のリストを参照するプロパティです。「edges」と比較して独自の特徴を持っています。
*   **Reverse Direct Relationship (RDR)**: 「views」上の接続プロパティで、「direct relationship」が存在し、データを含む「nodes」を指す参照があることを示します。
*   **Connection Type**: 「RDR」が単一または複数の「relationships」を含むかどうかを指定し、「single_reverse_direct_relation」または「multi_reverse_direct_relation」のいずれかを使用します。
*   「Direct relations」は「edges」と比較して独自の特徴を持っています。「Edges」はプロパティを持つことができ、再帰的に辿ることができ、インスタンスの制限にカウントされます。

<!-- SOURCE_END: 2-7_Summary.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-1_Defining a schema.md -->
## File: 3-1_Defining a schema.md

---
title: "Defining a schema"
source: "https://learn.cognite.com/cognite-data-modeling/1876104"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Defining a schema

独自の`schema`を作成して、カスタム`property`で`instance`を豊かにすることができます。`schema`は、3つの主要な要素で構成されています。

*   **Containers**：`property`を含む物理的な`storage`を定義します。
*   **Views**：`property`をマッピングする論理的な`schema`を確立します。
*   **Data models**：1つ以上の`view`のコレクションであり、`graph data`の消費と取り込みに使用されます。

3つの要素はすべて、`instance`と同様に、`space`にスコープされています。

![[images/4c0ced8b97dde7bb0c794581123cfdde_MD5.png]]

`schema`におけるこれらの構成要素それぞれの役割を見てみましょう。

### Containers - Defining Physical Storage

`container`を、さまざまな`property`を保持する`graph`の「引き出し」と考えてください。これらは物理`storage`を定義し、`instance`の特定の`property`を整理し、含んでいます。この構成は、効率的な`data retrieval`と`management`のために重要です。`container`は`instance`（`node`と`edge`）の`property`を保存します。

### Views - Establishing Logical Schemas

`View`は、`data`を見るための「レンズ」として機能します。これらは`container`からの`property`をマッピングすることで論理的な`schema`を確立します。これにより、さまざまな`use case`や`analysis`ニーズに合わせて、同じ`data`の異なる視点や表現を作成できます。

### Data Models - Structuring for Consumption and Ingestion

`Data models`は、`knowledge graph`の「設計図」のようなものです。これらは1つ以上の`view`のコレクションであり、`data`が`graph`にどのように消費され、取り込まれるかを定義するために使用されます。`data model`を慎重に設計することで、`graph`が構造化され、一貫性があり、さまざまな`application`に対応できることを保証できます。

<!-- SOURCE_END: 3-1_Defining a schema.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-2_Understanding spaces.md -->
## File: 3-2_Understanding spaces.md

---
title: "Understanding spaces"
source: "https://learn.cognite.com/cognite-data-modeling/1876097"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Understanding spaces

**Spaces** は、グラフを管理しやすく、重複しないセクションに整理する **namespaces** として機能します。これらは、関連する **schemas** や **instances** を保存できる専用のワークスペースやフォルダーのようなものです。

**knowledge graphs** における **spaces** は、コンピューター上の **folders** のように考えてください。各フォルダーは関連するファイルやドキュメントを保持します。同様に、**knowledge graph** では、**spaces** は情報を特定の **categories** や **domains** にきちんと整理するフォルダーのようなものです。

### Access Management

**Spaces** は単に整理のためだけではありません。**誰がスペースに read または write できるか** を制御します。さまざまな **access levels** を持つ **spaces** を設定することで、機密性の高い **data models** を保護しつつ、より広範な対話のための **collaborative spaces** を許可することができます。

**space** を削除するには、まずそれに割り当てられているすべての **schema resources** を削除する必要があります。

<!-- SOURCE_END: 3-2_Understanding spaces.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-3_Understanding containers.md -->
## File: 3-3_Understanding containers.md

---
title: "Understanding containers"
source: "https://learn.cognite.com/cognite-data-modeling/1877846"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Understanding containers

### Properties

ナレッジグラフでコンテナを構築する際、それが保持する「properties」を定義する必要があります。これらの「properties」は、「nodes」や「edges」といった「instances」に割り当てられる属性であり、モデル化される情報のニュアンスを捉えるためにさまざまな「data types」が存在します。

| **Property type** | Description |
| :---------------- | :---------- |
| text              | テキストデータ、文字列を保存するために使用されます。 |
| int64             | 大きな数値を保存するための64ビット整数です。 |
| float64           | 小数を含む数値表現の精度を可能にする64ビット浮動小数点数です。 |
| float32           | `float64`よりも精度とストレージ要件が少ない32ビット浮動小数点数です。 |
| boolean           | 真偽値（`true`または`false`の値を保存するため）です。 |
| timestamp         | タイムゾーンを含む日時情報です。 |
| date              | タイムゾーンを考慮しない日付コンポーネントのみです。 |
| json              | 構造化された「JSON objects」を保存するための汎用的なプロパティタイプです。 |
| direct            | 別の「instance」への「direct relation」です。 |
| enum              | 固定された名前付き値のセットです。 |

**List support:** 「direct type」を除くすべての「base and native reference types」は、`files: [File]`のような構文で「lists」として宣言できます。

**Nullability and Defaults:** 「Properties」は「nullable」として定義でき、空にできることを示し、「default values」を割り当てることで「instances」全体の一貫性を確保できます。

**How to specify a property?**

「Properties」は、「**name, description, nullability, defaults, list support, type**」といった属性で定義する必要があります。必須の文字列プロパティの完全な仕様は次のようになります。

![[images/9521f5c354d0c206910e1b04f20b0cff_MD5.png]]

> Tip: コンテナを設定する際は、データをどのように検索するかを考えてください。クエリの「filter」や「sort」によく使用する「properties」を選択し、「indexes」を設定して検索を高速化します。ただし、「indexes」が増えると「data updates」が遅くなる可能性があることに注意してください。

コンテナの主な側面は次のとおりです。

1.  **Indexes:** パフォーマンス、特に「query operations」を向上させるために、コンテナには「single or multi-column indexes」を設定でき、コンテナごとに最大10個の「indexes」が可能です。
2.  **Constraints:** データの整合性と「business logic」の遵守を維持するために、コンテナプロパティに「constraints」を設定できます。「Constraints」は、事前定義された「rules」を尊重する有効なデータのみが保存されるようにします。

    **Supported constraint types:** 「Constraints」は「REST API」または「GraphQL」の`@container`ディレクティブを介して定義でき、データモデルの管理方法に柔軟性を提供します。許可される「constraints」には「Uniqueness」と「Requires」の2種類があります。

    *   *Select each tab to learn more.*

    **UNIQUENESS**

    この「constraint」は、「property」または「set of properties」の値がコンテナ内で「unique」であることを保証します。例えば、「equipment container」では、「serial_number」プロパティに「uniqueness」を強制することで、同じ識別子を持つ2つの機器が誤って記録されることを防ぐことができます。

    **REQUIRES**

    この「constraint」はコンテナをリンクし、ある「instance」が1つのコンテナにデータを持つ場合、指定された別のコンテナにも対応するデータを持つことを要求します。例えば、ポンプの「node」が「'Pump' container」にデータを持つ場合、「'requires' constraint」は、それが「'Equipment' container」にも関連データを持つことを保証します。

3.  **Default Values and Auto-Increment:** コンテナでは、各「property」に「default values」を設定できます。さらに、「integer properties」は「auto-increment」するように設定でき、「instances」の一意で「sequential identifiers」を確保します。

### Containers are organised like schema in a relational database

「space」と「externalId」の組み合わせは、「foreign key」と類似の目的を果たし、コンテナ内のデータエントリを中央の「node table」にリンクさせます。これは「relational databases」における「snowflake schema」を彷彿とさせる構造です。

このアナロジーは、「separation of concerns」にも及びます。データベース「schema」における「foreign keys」がデータの存在を強制することなく参照を作成するのと同様に、ノードと「type」（例えば、「[types, pump]」がノードがポンプを表すことを示す）の関連付けは、ポンプコンテナにそれに対応するデータがあることを本質的に保証するものではありません。この分離は、ナレッジグラフの「data model」が「relational schema」とは異なる概念レベルで機能し、厳密なテーブル接続よりも柔軟な関係に焦点を当てていることを強調しています。

「Views」は、ノードのデータがその「type」に従って入力されていることを確認するために利用できます。「Views」は、データがユーザーフレンドリーな方法で形成され、提示されるようにカスタマイズ可能なレンズとして機能し、「data model」が現実世界の関係を反映するだけでなく、「application requirements」に従って「business logic」を強制するようにします。

`usedFor`フィールドでは、コンテナが使用できる「instances」の種類を定義できます。次のいずれかの値を指定します。

*   `node`: コンテナは、「node」上の「properties」を埋めるためにのみ使用できます。
*   `edge`: コンテナは、「edge」上の「properties」を埋めるためにのみ使用できます。
*   `all`: コンテナは、「nodes」と「edges」の両方の「properties」を埋めるために使用できます。
    *   `all`を選択すると、「data ingestion」中の「resource consumption」が増加する可能性があることに注意することが重要です。これは、システムがより複雑な関連付けのセットを処理する必要があり、`node`と`edge`の両方に影響を与える可能性があるためです。

**オフショア石油掘削装置の監視システムにおける`node`、`edge`、および`all`のコンテナの使用**

オフショア石油掘削装置の監視システムでは、`node`用に設定されたコンテナは掘削装置の「equipment specifications」を保存する可能性がありますが、`edge`用に設定された別のコンテナは、さまざまな機器間の「connectivity」と「communication protocols」を追跡する可能性があります。「lastCommunicationTimestamp」のような同じ追跡パラメータが、個々の機器（`nodes`）と通信リンク（`edges`）の両方に関連する場合、「all」用に設定されたコンテナが採用されます。

<!-- SOURCE_END: 3-3_Understanding containers.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-4_Understanding views.md -->
## File: 3-4_Understanding views.md

---
title: "Understanding views"
source: "https://learn.cognite.com/cognite-data-modeling/1876119"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Understanding views

### How do you establish relationships between two types of nodes?

以下の例は、グラフ内の2つの種類のノード、特に **'BasicPump'** ノードから **'BasicValve'** ノードへの **'flows-to'** 接続を使用して関係を確立する方法を示しています。この接続は、ポンプ/バルブ関係の例で確認できるように、接続プロパティ内のいくつかのフィールドを通じて詳細に記述されています。

```javascript
type:
   space: types
   externalId: flows-to
source:
   space: equipment
   externalId: BasicValve
   version: v1
direction: outwards
```

*   **Type**:
    このフィールドには、ノード間の関係またはエッジのタイプを識別する完全修飾された外部IDが保持されます。この場合、「flows-to」エッジのIDとなり、ポンプからバルブへの流れの方向を示します。
*   **Source**:
    これは、関係の受信側にあるノードを観察できるビューを指します。本質的に、「BasicValve」ノードに関する詳細を提供するビューへの参照です。
*   **Direction**:
    これは関係の横断方向を指定します。これは **'inwards'** または **'outwards'** のいずれかです。ポンプからバルブへの「flows-to」接続の場合、方向は通常 **'outwards'** であり、流れがポンプから発生することを示します。

オプションで **'EdgeSource'** と呼ばれるフィールドを追加して、「flows-to」エッジ自体に関する追加の詳細を提供するビューを参照できます。これは常に必要ではありませんが、必要に応じてエッジのさらなるコンテキストまたはプロパティを提供できます。

### Implementing Other Views

ビューは、そのプロパティを継承するために他のビューを実装できます。この機能は、複数の他のビューのプロパティを組み合わせてデータモデルの柔軟性と深さを高めるビューを作成する際に特に役立ちます。

以下のYAMLを使用すると、**Pump view** の有効なプロパティは **manufacturer** と **maxPressure** になります。

![[images/48245558c51b5daa587f262b68f203b4_MD5.png]]

> **Handle View Properties with Care!**
> ビュー内のプロパティはクエリ時に決定されることに注意してください。ビューへの破壊的な変更は防止していますが、他のビューが依存するビューを削除すると、継承されたプロパティが削除される可能性があります。この削除は、これらのプロパティに依存するアプリケーションを潜在的に中断させる可能性があります。意図しない結果を避けるため、ビューの変更または削除は常に注意して行ってください。

### Managing Property Conflicts and Precedence in Views

複数のビュー（A、B、C、Dなど）が互いを実装し、それぞれが独自のプロパティを持つ場合、どのプロパティが優先されるかを理解することが重要です。システムは実装されたグラフの順序に基づいて競合を解決し、最終的なビューが最も関連性の高い優先されたプロパティを反映するようにします。

![[images/eaac3acace7d66392099e4c6a427d865_MD5.png]]

グラフ内で競合するプロパティ識別子が発生した場合、それらは実装グラフの位相ソートによって解決されます。「implements」配列内の順序が重要であり、後のエントリが優先されます。たとえば、Bが `[C, D]` を実装する場合、優先順位のシーケンスはA、B、**D, C** となります。

![[images/f5626d9fb2b7d00964d5107df334f793_MD5.png]]

逆に、Bが `[D, C]` を実装する場合、シーケンスはA、B、**C, D** に変わります。この方法は、明確で予測可能な競合解決を保証します。

![[images/5da616954cf30db5dfdca628f4fc609f_MD5.png]]

**Implementing multiple views in an oil rig monitoring system**

石油掘削装置に様々なセンサーと機器があると想像してみてください。**'Sensor'** コンテナと **'Equipment'** コンテナがあるかもしれません。

![[images/74d0f7c2abb3043a31befd374eb9ef92_MD5.png]]

ビューを効果的に理解し実装することで、データモデルのユーティリティ、柔軟性、アクセシビリティを大幅に向上させ、様々なアプリケーションや分析にとって強力なツールにすることができます。

### Understanding View Versioning in Data Modeling

ビューのバージョン管理は、データモデリングにおいて変更を管理し、システムの整合性を維持するために非常に重要です。バージョンフィールドは、任意の値に設定できる文字列であることに注意してください。

ビューのバージョン管理の詳細については、[Cognite Documentation](https://docs.cognite.com/cdf/dm/dm_concepts/dm_containers_views_datamodels#view-versioning)で確認できます。

<!-- SOURCE_END: 3-4_Understanding views.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-5_View filters.md -->
## File: 3-5_View filters.md

---
title: "View filters"
source: "https://learn.cognite.com/cognite-data-modeling/1877849/scorm/3iv0ks9bjr1dn"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# View filters

[[b0713a721048889c60a91df3c7bc9d2a_MD5.jpg|Open: Pasted image 20251026212741.png]]
![[images/b0713a721048889c60a91df3c7bc9d2a_MD5.jpg]]

### Summary

**View filters**は、**data modeling**における強力な機能であり、**querying a view**の際に含まれる**nodes**を絞り込むことができます。これらは、関連するデータのみが取得されることを確実にし、**queries**の効率と精度を向上させる上で特に役立ちます。

**filters**は、**queries**に含まれる**nodes**を絞り込むために**views**に適用されます。**data model services**のほとんどの**query endpoints**では、これらの**filters**は自動的に適用されます。ただし、高度な**endpoints**の場合、手動で**filters**を適用する必要があるかもしれません。

**Learn more from the interactive content above. Use ❮ and ❯ buttons to navigate or click anywhere on the screen to go to the next slide.** (上記の**interactive content**からさらに学びましょう。❮ および ❯ ボタンを使用して移動するか、画面上の任意の場所をクリックして次のスライドに進んでください。)

<!-- SOURCE_END: 3-5_View filters.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-6_Exploring property graphs a guide to nodes, edges, and attributes.md -->
## File: 3-6_Exploring property graphs a guide to nodes, edges, and attributes.md

---
title: "Exploring property graphs: a guide to nodes, edges, and attributes"
source: "https://learn.cognite.com/cognite-data-modeling/1875665/scorm/egctaaylu63s"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Exploring property graphs: a guide to nodes, edges, and attributes

**重要事項:** このページの内容の一部（メインレッスンコンテンツ）は埋め込み型のSCORMパッケージ（iframe内）であるため、その内部テキストを直接文字起こしすることはできません。以下の内容は、Webサイトのフレーム、ナビゲーション、および現在のレッスンに関する利用可能な概要情報に基づいています。

[[32e14c9b500f309fdb134b7720be8562_MD5.jpg|Open: Pasted image 20251026213202.png]]
![[images/32e14c9b500f309fdb134b7720be8562_MD5.jpg]]
[[21a80bd84d119c055c9bc441d927b27d_MD5.jpg|Open: Pasted image 20251026213227.png]]
![[images/21a80bd84d119c055c9bc441d927b27d_MD5.jpg]]
[[f1187edc1636a42432a8ca7d9e268014_MD5.jpg|Open: Pasted image 20251026213258.png]]
![[images/f1187edc1636a42432a8ca7d9e268014_MD5.jpg]]
[[2439941c935f99bc3b529785c674a87f_MD5.jpg|Open: Pasted image 20251026213313.png]]
![[images/2439941c935f99bc3b529785c674a87f_MD5.jpg]]
[[8bfda33e80960efe7bac60c3db52465b_MD5.jpg|Open: Pasted image 20251026213506.png]]
![[images/8bfda33e80960efe7bac60c3db52465b_MD5.jpg]]
[[4b234de86635e804e682e6c2c758401b_MD5.jpg|Open: Pasted image 20251026213755.png]]
![[images/4b234de86635e804e682e6c2c758401b_MD5.jpg]]
[[ea25b2545d4be162c342ec5ecbd1c35c_MD5.jpg|Open: Pasted image 20251026213818.png]]
![[images/ea25b2545d4be162c342ec5ecbd1c35c_MD5.jpg]]
[[09d79e1dd384eba2f79435e3447b33c4_MD5.jpg|Open: Pasted image 20251026213836.png]]
![[images/09d79e1dd384eba2f79435e3447b33c4_MD5.jpg]]

**Summary** (概要)

*   Use **❮** and **❯** buttons to navigate or click anywhere on the screen to go to the next slide.
*   **❮**および**❯**ボタンを使用してナビゲートするか、画面上の任意の場所をクリックして次のスライドに進んでください。

<!-- SOURCE_END: 3-6_Exploring property graphs a guide to nodes, edges, and attributes.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-7_Example Constraints in action.md -->
## File: 3-7_Example Constraints in action.md

---
title: "Example: Constraints in action"
source: "https://learn.cognite.com/cognite-data-modeling/1875682/scorm/3r63gny981p0e"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
[[1354aa51694d3e27f5dbd149affb712d_MD5.jpg|Open: Pasted image 20251026214226.png]]
![[images/1354aa51694d3e27f5dbd149affb712d_MD5.jpg]]
[[3b814a105dca3eac16f7086e6d429230_MD5.jpg|Open: Pasted image 20251026214242.png]]
![[images/3b814a105dca3eac16f7086e6d429230_MD5.jpg]]
[[bc6538fd1730c2a792acea89d72d1293_MD5.jpg|Open: Pasted image 20251026214258.png]]
![[images/bc6538fd1730c2a792acea89d72d1293_MD5.jpg]]
[[45e1cb466025b235f758bf444d489381_MD5.jpg|Open: Pasted image 20251026214314.png]]
![[images/45e1cb466025b235f758bf444d489381_MD5.jpg]]
[[d0ff64605b21ccea8f2bacddf401a90e_MD5.jpg|Open: Pasted image 20251026214329.png]]
![[images/d0ff64605b21ccea8f2bacddf401a90e_MD5.jpg]]
[[a603efe3eb166d41997fc4907004b888_MD5.jpg|Open: Pasted image 20251026214342.png]]
![[images/a603efe3eb166d41997fc4907004b888_MD5.jpg]]
[[38d89640420ba4ffa4ec2daca058c527_MD5.jpg|Open: Pasted image 20251026214357.png]]
![[images/38d89640420ba4ffa4ec2daca058c527_MD5.jpg]]
[[10e53c8d7921588ac3560efc30c4ae67_MD5.jpg|Open: Pasted image 20251026214411.png]]
![[images/10e53c8d7921588ac3560efc30c4ae67_MD5.jpg]]
[[683d5e0af0b4e9a9a54716ed14d8ca9e_MD5.jpg|Open: Pasted image 20251026214424.png]]
![[images/683d5e0af0b4e9a9a54716ed14d8ca9e_MD5.jpg]]
###  Summary
上記の**インタラクティブコンテンツ (interactive content)**を探索して、2種類の**Constraints**が実際に機能している様子をご覧ください。**❮**および**❯**ボタンを使用してナビゲートするか、画面上の任意の場所をクリックして次の**スライド (slide)**に進んでください。

<!-- SOURCE_END: 3-7_Example Constraints in action.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-8_Example of a knowledge graph industrial fluid system.md -->
## File: 3-8_Example of a knowledge graph industrial fluid system.md

---
title: "Example of a knowledge graph: industrial fluid system"
source: "https://learn.cognite.com/cognite-data-modeling/1875673/scorm/2h229q3051zop"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
# Example of a knowledge graph: industrial fluid system

[[2fa0423b80c14cdc48e2168be610b55c_MD5.jpg|Open: Pasted image 20251026214913.png]]
![[images/2fa0423b80c14cdc48e2168be610b55c_MD5.jpg]]
[[088b8ed7e25405e4dd2d1ca168358714_MD5.jpg|Open: Pasted image 20251026214928.png]]
![[images/088b8ed7e25405e4dd2d1ca168358714_MD5.jpg]]

### Summary

知識グラフの主要な要素を理解したところで、上記のインタラクティブな例を見て、*industrial fluid system* のこの例でそれらがどのように結びついているかを探ってみましょう。

*各コンポーネントの詳細については、箇条書きをクリックしてください。*

産業環境では、*pump system* が一連の *pipes* を通して液体を複数の *valves* に送るために利用されます。このシステムは運用にとって極めて重要であり、施設のプロセスに不可欠な流体の流れと分配を制御します。*knowledge graph* は、*nodes* が点として、*edges* がそれらを結ぶ線として、そして *direct relations* がラベル付きリンクとして、図で視覚化される可能性が高いです。この視覚化により、ユーザーは流体システムの構造と状態を素早く理解することができます。

**In practice, this knowledge graph serves multiple purposes:**

1.  **Operational Monitoring:** エンジニアはシステムをリアルタイムで監視し、流量と圧力を観察して、すべてが正しく機能していることを確認できます。
2.  **Maintenance Planning:** グラフはメンテナンス活動の計画に役立ち、どのコンポーネントがサービス時期を迎えているか、最近どのような問題が報告されたかを特定します。
3.  **Troubleshooting:** 誤動作が発生した場合、グラフは問題領域を迅速に特定し、あるコンポーネントの故障がシステム全体にどのように影響するかを理解するのに役立ちます。

<!-- SOURCE_END: 3-8_Example of a knowledge graph industrial fluid system.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 3-9_Summary.md -->
## File: 3-9_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/cognite-data-modeling/1876121"
created: 2025-10-26
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Summary

1.  **Defining a Schema**
    *   特定のニーズに合わせたカスタムの `schemas` を作成することの重要性。
    *   豊富なデータ構造化のために、`containers`、`views`、`data models` を活用すること。
    *   特定の `namespaces` 内でのインスタンスを整理すること。

2.  **Spaces: Organizational Units**
    *   `Spaces` はグラフ要素を整理するための `namespaces` として機能します。
    *   実用的な組織、アクセス管理、データ制御を提供します。
    *   `Spaces` は様々なアクセスレベルを持つことができ、データ保護とコラボレーションを促進します。

3.  **Containers - Defining Physical Storage**
    *   プロパティストレージのための `PostgreSQL tables` としての `Containers`。
    *   パフォーマンスとデータ整合性のための `indexes` と `constraints` の使用。
    *   異なる `spaces` 全体での `container` 配置の柔軟性。

4.  **Views - Establishing Logical Schemas**
    *   `Container properties` から論理 `schemas` を作成する手段としての `Views`。
    *   カスタマイズされたデータ取得のための `views` を通じたデータクエリ。
    *   明確さと一貫性のための `properties` のマッピングと名前変更。

5.  **Data Models - Structuring for Consumption and Ingestion**
    *   データ消費のための `views` のコレクションとしての `Data models`。
    *   構造化され一貫性のあるグラフ管理のための `data models` の設計の重要性。
    *   `data model` の変更を管理するためのバージョン管理戦略の活用。

6.  **Exploring Property Graphs: Basics**
    *   `Nodes`: オブジェクト（油田の `pumps` など）を表す基本的な要素。
    *   `Edges`: `Nodes` を接続し、関係性（`pumps` 間の `pipelines` など）を示す線。
    *   `Properties`: `Nodes` と `Edges` の追加の詳細または `attributes` で、より多くのコンテキストを提供します。

<!-- SOURCE_END: 3-9_Summary.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 4-1_Cognite Core Data Model.md -->
## File: 4-1_Cognite Core Data Model.md

---
title: "Cognite Core Data Model"
source: "https://learn.cognite.com/cognite-data-modeling/1876941"
created: 2025-10-27
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Cognite Data Modeling

Cogniteの**CogniteCore**は、産業データ向けに標準化された構成要素を提供し、より専門的なモデルの基礎を形成します。このコアデータモデルは、`core features`と`core concepts`の2つの方法でプロパティを定義します。

*   **core features** は、**多くの** `concept`に利用可能なプロパティのセットです。
*   **core concepts** は、**単一の** `concept`に固有のプロパティを持ちます。

各 `core feature` と `core concept` には、一致する `container` と `view` が付属しています。詳細については、[Cognite core data model](https://docs.cognite.com/cdf/dm/dm_reference/dm_core_data_model) を参照してください。

<!-- SOURCE_END: 4-1_Cognite Core Data Model.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 4-2_Building an asset hierarchy  Cognite Documentation.md -->
## File: 4-2_Building an asset hierarchy  Cognite Documentation.md

---
title: "Building an asset hierarchy | Cognite Documentation"
source: "https://docs.cognite.com/cdf/dm/dm_guides/dm_cdm_build_asset_hierarchy"
created: 2025-10-27
description: "The CogniteAsset concept in the Cognite core data model has properties to represent systems that support industrial functions or processes. You can structure assets hierarchically according to your organization's criteria. For example, an asset can represent a water pump that's part of a larger pump station asset at a clarification plant asset."
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Building an asset hierarchy with the Cognite core data model

**CogniteAsset**（コグナイト・アセット）の概念は、Cognite core data model（コグナイト・コア・データモデル）において、産業機能やプロセスを支援するシステムを表現するプロパティを持っています。組織の基準に従って、アセットを階層的に構造化できます。例えば、アセットは、**clarification plant**（浄化プラント）アセットにある**pump station**（ポンプステーション）アセットの一部である**water pump**（給水ポンプ）を表現できます。

**CogniteAsset**には、対応する**container**（コンテナ）と**view**（ビュー）が付属しています。コンテナには効率的な**indexes**（インデックス）と**constraints**（制約）があり、すぐに使用できるスケールで使用できます。

## Build an asset hierarchy with CogniteAsset

### 重要なポイント

- **Root**（ルート）ノードから始めて、`parent`プロパティを使用して**child**（子）アセットを追加することを推奨
- `parent`プロパティが空のノードが**root nodes**（ルートノード）
- `parent`プロパティを後で追加または変更して、ノードとその子を既存の階層に追加可能
- 階層内のノードを追加または変更すると、バックグラウンドジョブである**path materializer**（パス・マテリアライザー）が、変更の影響を受けるすべてのノードのルートノードからのパスを再計算
- ジョブは非同期で実行され、すべてのパスを更新するのに要する時間は、影響を受けるノードの数に依存

### データ取り込みの手順

**CogniteAsset view**（コグナイト・アセット・ビュー）を使用して汎用的なアセット階層を作成するには、取り込みプロセス中に**CogniteAsset view**を`source`として設定し、親アセットを指定するために`parent`プロパティを設定するか、ルートアセットの場合は空白のままにします。

### Populate data with Transformations

この例では、**Transformations**（トランスフォーメーション）を使用して、CDF staging area（CDFステージングエリア）のRAWから`lift-stations-pumps`テーブルのデータを取り込みます。

#### 手順

1. **データをステージングエリアにアップロード**
   - 以下のようなスクリプトを使用してデータをアップロードします：
   - **collections_pump.csv**をRAWステージングにアップロード
   - **Cognite Python SDK**の例とドキュメントは以下のリンクで確認できます：
	- [Authentication](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/credential_providers.html#)（認証）
	- [RAW](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_ingestion.html#raw)（RAWデータ取り込み）

```python
import pandas as pd
from cognite.client import ClientConfig, CogniteClient

# client_config = … ここで認証を設定
client = CogniteClient(client_config)

# RAWデータベースとテーブルを作成
raw_database = client.raw.databases.create("cdm-asset-demo")
raw_table = client.raw.tables.create(raw_database.name, "lift-stations-pumps")

# pandasを使用してCSVファイルを読み込み
pump_data_url = "https://github.com/cognitedata/toolkit/raw/f9c2ce8c4de8928718acc48eb6688338c6b3508f/cognite_toolkit/cognite_modules/examples/example_pump_asset_hierarchy/raw/collections_pump.csv"
lift_station_pumps_frame = pd.read_csv(pump_data_url)

# データフレームをRAWにアップロード
client.raw.rows.insert_dataframe(raw_database.name, raw_table.name, lift_station_pumps_frame)
```

2. **Transformationの作成**
   - ***Data management*** > ***Integrate*** > ***Transformations***に移動
   - **Create transformation**を選択し、一意の**name**と一意の**external ID**を入力
   - **Next**を選択
   - **Target data models**の下で**Cognite core data model**を選択し、**Version**で**v1**を選択
   - **Target space**で、アセットインスタンスを取り込むスペースを選択（この例では**asset-demo**）
   - **Target type or relationship**として**CogniteAsset**を選択
   - **Action**として**Create or Update**を選択
   - **Create**を選択
   
   ![[images/f42d548ff6a189f0ea25ee21c60f1696_MD5.png]]

3. **SQL仕様の追加と実行**
   - 以下のSQL仕様を追加して**run**し、アセット階層を作成：

```sql
-- Add the root manually. The set for lift stations doesn't specify a root asset.
select
  'lift_pump_stations:root' as externalId,
  'Lift Station Root' as name,
  'Root node to group all lift stations.' as description,
  null as parent
UNION ALL
-- select lift station data
select distinct
  concat('lift_station:', cast(`LiftStationID` as STRING)) as externalId,
  cast(`LiftStationID` as STRING) as name,
  cast(`LocationDescription` as STRING) as description,
  node_reference('asset-demo','lift_pump_stations:root') as parent
from
  `cdm-asset-demo`.`lift-stations-pumps`
where isnotnull(nullif(cast(`LiftStationID` as STRING), ' '))
UNION ALL
-- select pump data
select
  concat('pump:', cast(`FacilityID` as STRING)) as externalId,
  cast(`Position` as STRING) as name,
  null as description,
  node_reference('asset-demo', concat('lift_station:', cast(`LiftStationID` as STRING))) as parent
from
  `cdm-asset-demo`.`lift-stations-pumps`
where isnotnull(nullif(cast(`LiftStationID` as STRING), ' '));
```

この階層は2つのレベルで構成されています：
- **lift stations**（リフトステーション）
- **pumps**（ポンプ）

**pumps**は**lift station**の子として設定されます。

## Build an asset hierarchy and extend CogniteAsset

### 概要

汎用的な**CogniteAsset**を他の**core data model concepts**（コア・データモデル・コンセプト）と組み合わせて使用することで、多くの場合、必要なデータモデルを作成するのに十分です。カスタムデータモデルを作成する必要がある場合は、Cogniteデータモデルを**extend**（拡張）して、ユースケース、ソリューション、またはアプリケーションに合わせて調整できます。

### 拡張の例

以下の例は、**CogniteAsset**を拡張して、より多くのポンプデータ（`DesignPointFlowGPM`、`DesignPointHeadFT`、`LowHeadFT`、`LowHeadFlowGPM`）を保持する方法を示しています。

#### 手順1: Containerの作成

追加データを保持するための**container**（コンテナ）を作成します：

```yaml
Pump container definitionexternalId: Pump
name: Pump
space: asset-demo
usedFor: node
properties:
  DesignPointFlowGPM:
    autoIncrement: false
    defaultValue: null
    description: The flow the pump was designed for, specified in gallons per minute.
    name: DesignPointFlowGPM
    nullable: true
    type:
      list: false
      type: float64
  DesignPointHeadFT:
    autoIncrement: false
    defaultValue: null
    description: The flow head pump was designed for, specified in feet.
    name: DesignPointHeadFT
    nullable: true
    type:
      list: false
      type: float64
  LowHeadFT:
    autoIncrement: false
    defaultValue: null
    description: The low head of the pump, specified in feet.
    name: LowHeadFT
    nullable: true
    type:
      list: false
      type: float64
  LowHeadFlowGPM:
    autoIncrement: false
    defaultValue: null
    description: The low head flow of the pump, specified in gallons per minute.
    name: DesignPointHeadFT
    nullable: true
    type:
      list: false
      type: float64
constraints:
requiredAsset:
  constraintType: requires
  require:
    type: container
    space: cdf_cdm
    externalId: CogniteAsset
```

**重要なポイント：**
- `requires`**constraint**（制約）により、**Pump**データが常に**CogniteAsset**データと一緒に保持されることが保証されます

#### 手順2: Viewの作成

ノードを**lift-station**（リフトステーション）アセットと**pump**（ポンプ）アセットとして表示する2つの**view**（ビュー）を作成します。**CogniteAsset**ビューを**implementing**（実装）することで、そのビューで定義されたすべてのプロパティが自動的に含まれます。

**Pump viewの定義：**

```yaml
Pump view definitionexternalId: Pump
name: Pump
space: asset-demo
version: v1
implements: # <-- Declares that the view implements the CogniteAsset view
  - space: cdf_cdm
    externalId: CogniteAsset
    version: v1
properties:
  DesignPointFlowGPM:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: DesignPointFlowGPM
    name: DesignPointFlowGPM
  DesignPointHeadFT:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: DesignPointHeadFT
    name: DesignPointHeadFT
  LowHeadFT:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: LowHeadFT
    name: LowHeadFT
  LowHeadFlowGPM:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: LowHeadFlowGPM
    name: LowHeadFlowGPM
```

**LiftStation viewの定義：**

```yaml
LiftStation view definitionspace: asset-demo
externalId: LiftStation
version: v1
implements: # <-- Declares that the view implements the CogniteAsset view
  - space: cdf_cdm
    externalId: CogniteAsset
    version: v1
filter: # <-- Adds additional filter to only include lift-stations
  prefix:
    property:
      - node
      - externalId
    value: lift_station
```

**重要なポイント：**

- **LiftStation**ビューの`filter`プロパティに注意
- **Pump**ビューには、**Pump**コンテナと**core data model**（コア・データモデル）コンテナのプロパティが含まれます
- デフォルトの`hasData`フィルターにより、ビューは**pumps**または**pump**データを持つインスタンスのみを表示します
- **LiftStation**ビューには、**CogniteAsset**ビューと同じフィールドがありますが、明示的なフィルターがないと、**pumps**と**lift stations**の両方を返します

**パフォーマンスに関する注意：**
- ビューで明示的なフィルターを使用する場合、クエリパフォーマンスを考慮することが重要です
- ビューフィルターは、誰かがビューを使用してデータをクエリするたびに実行されます
- 必要に応じて、パフォーマンスの低下を避けるために**indexes**（インデックス）を追加します
- システムは常に`externalId`プロパティにインデックスを作成します

**インスタンスデータの取り込みとクエリ：**
- ビューを`source`として使用してインスタンスデータを取り込む場合、システムは必要なすべてのフィールドが入力されることを保証します
- クエリ時、ビューの`hasData`フィルターを使用することで、返される結果にビューに必要なすべてのフィールドがあることが保証されます
- ビューに必須フィールドがない場合でも、ビューを使用して「空白」値を書き込み、`hasData`フィルターの条件を満たすことができます

#### 手順3: Data Modelの作成

カスタムアセット階層を取り込むために、カスタムビューを表示する**data model**（データモデル）を作成します：

```yaml
externalId: liftStationsPumps
name: Lift stations and pumps
space: asset-demo
version: v1
views:
  - externalId: LiftStation
    space: asset-demo
    type: view
    version: v1
  - externalId: Pump
    space: asset-demo
    type: view
    version: v1
```

---
tip

以下のPythonスクリプトは、**Cognite Python SDK**と`.load()`関数を使用して、**YAML**表現からデータモデリングオブジェクトを作成する方法を示しています。**`CogniteClient`**を適切に[認証](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/credential_providers.html#)することを忘れないでください。

---

## Creating custom schema components with Cognite Python SDK

### カスタムスキーマコンポーネントの作成

**Cognite Python SDK**を使用してカスタムスキーマコンポーネントを作成する例：
```
from cognite.client import ClientConfig, CogniteClient
from cognite.client.data_classes.data_modeling import (
    ContainerApply,
    DataModelApply,
    ViewApply,
)

# client_config = … <-- 認証を設定することを忘れないでください

client = CogniteClient(client_config)

pump_container_yaml = """
externalId: Pump
name: Pump
space: asset-demo
usedFor: node
properties:
  DesignPointFlowGPM:
    autoIncrement: false
    defaultValue: null
    description: The flow the pump was designed for, specified in gallons per minute.
    name: DesignPointFlowGPM
    nullable: true
    type:
      list: false
      type: float64
  DesignPointHeadFT:
    autoIncrement: false
    defaultValue: null
    description: The flow head pump was designed for, specified in feet.
    name: DesignPointHeadFT
    nullable: true
    type:
      list: false
      type: float64
  LowHeadFT:
    autoIncrement: false
    defaultValue: null
    description: The low head of the pump, specified in feet.
    name: LowHeadFT
    nullable: true
    type:
      list: false
      type: float64
  LowHeadFlowGPM:
    autoIncrement: false
    defaultValue: null
    description: The low head flow of the pump, specified in gallons per minute.
    name: DesignPointHeadFT
    nullable: true
    type:
      list: false
      type: float64
constraints:
  requiredAsset:
    constraintType: requires
    require:
      type: container
      space: cdf_cdm
      externalId: CogniteAsset
"""

pump_view_yaml = """
externalId: Pump
name: Pump
space: asset-demo
version: v1
implements: # <-- Declares that the view implements the CogniteAsset view
  - space: cdf_cdm
    externalId: CogniteAsset
    version: v1
properties:
  DesignPointFlowGPM:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: DesignPointFlowGPM
    name: DesignPointFlowGPM
  DesignPointHeadFT:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: DesignPointHeadFT
    name: DesignPointHeadFT
  LowHeadFT:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: LowHeadFT
    name: LowHeadFT
  LowHeadFlowGPM:
    container:
      externalId: Pump
      space: asset-demo
      type: container
    containerPropertyIdentifier: LowHeadFlowGPM
    name: LowHeadFlowGPM
"""

lift_station_view_yaml = """
space: asset-demo
externalId: LiftStation
version: v1
implements: # <-- Declares that the view implements the CogniteAsset view
  - space: cdf_cdm
    externalId: CogniteAsset
    version: v1
filter: # <-- Adds additional filter to only include lift-stations
  prefix:
    property:
      - node
      - externalId
    value: lift_station
"""

data_model_yaml = """
externalId: liftStationsPumps
name: Lift stations and pumps
space: asset-demo
version: v1
views:
  - externalId: LiftStation
    space: asset-demo
    type: view
    version: v1
  - externalId: Pump
    space: asset-demo
    type: view
    version: v1
"""

pump_container = ContainerApply.load(pump_container_yaml)
client.data_modeling.containers.apply(pump_container)

pump_view = ViewApply.load(pump_view_yaml)
lift_station_view = ViewApply.load(lift_station_view_yaml)
client.data_modeling.views.apply([pump_view, lift_station_view])

data_model = DataModelApply.load(data_model_yaml)
client.data_modeling.data_models.apply(data_model)
```
#### 手順4: Transformationの作成

前の例では、**CogniteAsset**ビューを使用して**lift stations**と**pumps**のデータを書き込むことができました。**Transformations**は単一のビューへの書き込みをサポートするため、**LiftStation**ビューと**Pump**ビューの両方に書き込むには、それぞれのビューに対して変換を作成する必要があります。

**LiftStation**と**Pump**ビューの変換を作成するには、前の例の手順に従います。ターゲットデータモデルとして**Lift stations and pumps**を選択します。

![[images/6eda05cf1bbdc0bf4c853f446c117e5b_MD5.png]]

#### 手順5: SQL仕様の追加と実行

**Pumps用のTransformation：**

```sql
Transformation for Pumpsselect
  concat('pump:', cast(`FacilityID` as STRING)) as externalId,
  cast(`Position` as STRING) as name,
  concat(cast(`Position` as STRING), " " , cast(`FacilityID` as STRING)) as description,
  node_reference('asset-demo', concat('lift_station:', cast(`LiftStationID` as STRING))) as parent,
  double(`DesignPointHeadFT`) as DesignPointHeadFT,
  double(`DesignPointFlowGPM`) as DesignPointFlowGPM,
  double(`LowHeadFT`) as LowHeadFT,
  double(`LowHeadFlowGPM`) as LowHeadFlowGPM
from
  `cdm-asset-demo`.`lift-stations-pumps`
where isnotnull(nullif(cast(`LiftStationID` as STRING), ' '));
```

**LiftStations用のTransformation：**

```sql
Transformation for LiftStations-- Add root manual root node as the set does not have a root for lift stations
select
  'lift_pump_stations:root' as externalId,
  'Lift Station Root' as name,
  'Root node to group all lift stations.' as description,
  null as parent
UNION ALL
-- select lift station data
select distinct
  concat('lift_station:', cast(`LiftStationID` as STRING)) as externalId,
  cast(`LiftStationID` as STRING) as name,
  cast(`LocationDescription` as STRING) as description,
  node_reference('asset-demo','lift_pump_stations:root') as parent
from
  `cdm-asset-demo`.`lift-stations-pumps`
where isnotnull(nullif(cast(`LiftStationID` as STRING), ' '))
```

## Query hierarchical data

以下の例は、**Cognite Python SDK**を使用して階層データをクエリする方法を示しています。

### List by source view

**View**（ビュー）は、データの特殊な表現であり、クエリとフィルタリングの優れたツールです。例えば、**CogniteAsset**、**LiftStation**、**Pump**ビューを使用してノードを取得できます：

```python
assets = client.data_modeling.instances.list(sources=ViewId(space="cdf_cdm", external_id="CogniteAsset", version="v1"))
lift_stations = client.data_modeling.instances.list(sources=ViewId(space="asset-demo", external_id="LiftStation", version="v1"))
pumps = client.data_modeling.instances.list(sources=ViewId(space="asset-demo", external_id="Pump", version="v1"))
```

**重要なポイント：**
- インスタンスをリストする際にビューを`source`として設定すると、クエリに`hasData`フィルターが自動的に適用されます
- **CogniteAsset**を`source`としてインスタンスを取得すると、すべてのアセット（**lift-stations**と**pumps**の両方）が返されます
- それぞれのビューは**CogniteAsset**ビューを実装しているため、`hasData`フィルターを満たします

### Aggregate based on a common parent

多くの場合、アセットの子に関する集約情報を見つけたい場合があります。この例では、**lift station**内に**pumps**がいくつあるか、および**lift station**内のすべての**pumps**の`LowHeadFT`プロパティの平均値を見つけます：

```python
from cognite.client.data_classes import aggregations
from cognite.client.data_classes.filters import Equals

# count number of pumps for Riverhouse lift station
count_pumps_for_lift_station = client.data_modeling.instances.aggregate(
    view=ViewId(space="asset-demo", external_id="Pump", version="v1"),
    space="asset-demo",
    filter=Equals(
        property=["asset-demo", "Pump/v1", "parent"],
        value={"space": "asset-demo", "externalId": "lift_station:Riverhouse"},
    ),
    aggregates=aggregations.Count("externalId"),
).value
# average pumpOn value for lift-station someId
average_for_for_lift_station = client.data_modeling.instances.aggregate(
    view=ViewId(space="asset-demo", external_id="Pump", version="v1"),
    space="asset-demo",
    filter=Equals(
        property=["asset-demo", "Pump/v1", "parent"],
        value={"space": "asset-demo", "externalId": "lift_station:Riverhouse"},
    ),
    aggregates=aggregations.Avg("LowHeadFT"),
).value
```

### List all assets in a subtree

アセットのすべての子孫をリストするには、**CogniteAsset**ビューのシステム管理の`path`プロパティを使用します。

```python
from cognite.client.data_classes.data_modeling import NodeId
from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteAsset
from cognite.client.data_classes.filters import Prefix

sub_tree_root = client.data_modeling.instances.retrieve_nodes(
    NodeId("asset-demo", "lift_station:Riverhouse"), node_cls=CogniteAsset
)

sub_tree_nodes = client.data_modeling.instances.list(
    instance_type=CogniteAsset,
    filter=Prefix(property=["cdf_cdm", "CogniteAsset/v1", "path"], value=sub_tree_root.path),
)
```

**重要なポイント：**
- この例では、コアデータモデル定義からの**CogniteAsset** **TypedNode**を使用しています
- これは、データオブジェクトの**de-serialization**（デシリアライゼーション）のためのヘルパークラスです

<!-- SOURCE_END: 4-2_Building an asset hierarchy  Cognite Documentation.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 4-3_Summary.md -->
## File: 4-3_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/cognite-data-modeling/1876123"
created: 2025-10-27
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Summary

## 1. Cognite core model

**Cognite core model**（コグナイト・コアモデル）は、産業データの標準化された構築ブロックを提供します。

### 重要なポイント

- **CogniteCore data model**（コグナイト・コアデータモデル）は、産業データの標準化された構築ブロックを提供します
- プロパティを定義するために2つの方法を使用します：
	- **Core Features**（コア・フィーチャー）：複数のコンセプト間で使用可能なプロパティのセット
	- **Core Concepts**（コア・コンセプト）：単一のコンセプトに固有のプロパティ
- 各**Core Feature**と**Core Concept**は、対応する**container**（コンテナ）と**view**（ビュー）にリンクされています

## 2. Building an asset hierarchy with the Cognite core data model

**CogniteAsset**（コグナイト・アセット）を使用したアセット階層の構築方法についてのまとめです。

### 重要なポイント

- **CogniteAsset**コンセプトは、産業機能やプロセスを支援するシステムを表現し、対応する**container**と**view**が付属しています
- アセットは階層的に構造化でき、**root nodes**（ルートノード）と**child assets**（子アセット）が`parent`プロパティを通じて接続されます
- アセットが追加または変更されると、**path materializer**（パス・マテリアライザー）が非同期で、影響を受けるノードのルートノードからのパスを再計算します

<!-- SOURCE_END: 4-3_Summary.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-1_Populating containers with data.md -->
## File: 5-1_Populating containers with data.md

---
title: "Populating containers with data"
source: "https://learn.cognite.com/cognite-data-modeling/1875679/scorm/oevreog3zz3n"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
[[images/cae7b6bac4496f59ff50d9f68bc31d43_MD5.jpg|Open: Pasted image 20251103100946.png]]
![[images/cae7b6bac4496f59ff50d9f68bc31d43_MD5.jpg]]
![[images/e429666c361845ddb594b450b86966e8_MD5.jpg]]
[[images/472867bc57a825b064605321c2f1fdf4_MD5.jpg|Open: Pasted image 20251103101010.png]]
![[images/472867bc57a825b064605321c2f1fdf4_MD5.jpg]]
[[images/e429666c361845ddb594b450b86966e8_MD5.jpg|Open: Pasted image 20251103101036.png]]
![[images/e429666c361845ddb594b450b86966e8_MD5.jpg]]
[[images/56e0f09822cde44f527f051d3b95264e_MD5.jpg|Open: Pasted image 20251103101052.png]]
![[images/56e0f09822cde44f527f051d3b95264e_MD5.jpg]]
[[images/cb3b8d2f370c416fb5d30eeae1bd4cdb_MD5.jpg|Open: Pasted image 20251103101106.png]]
![[images/cb3b8d2f370c416fb5d30eeae1bd4cdb_MD5.jpg]]

<!-- SOURCE_END: 5-1_Populating containers with data.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-2_Retrieve data with queries in GraphQL.md -->
## File: 5-2_Retrieve data with queries in GraphQL.md

---
title: "Retrieve data with queries in GraphQL"
source: "https://learn.cognite.com/cognite-data-modeling/1876120"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Retrieve data with queries in GraphQL

**GraphQL**を使用してインスタンスをクエリできます。**GraphQL API**インターフェースのクエリ機能は、DMSクエリエンドポイントから利用可能な機能のサブセットを表しています。

## クエリの構成要素

**query**（クエリ）には、以下の主要セクションがあります：

### 1. Query intent（クエリ・インテント）

- `query MyQueryName {`（または単に`{`と省略）

### 2. The query to run（実行するクエリ）

実行するクエリは、4種類のタイプのいずれかになります（オプションでエイリアスを使用可能、例：`myAlias: listActor`）：

- **`list<type>`**：例として`listActor`は、フィルターパラメータに基づいてリストを返します
- **`get<type>ById`**：例として`getActorById`は、フィルターパラメータに基づいてリストを返します
- **`search<type>`**：例として`searchActor`は、クエリパラメータに基づいて検索します
- **`aggregate<type>`**：例として`aggregateActor`は、フィールドに基づいて集約（count、averageなど）を実行します

### 3. Query parameters（クエリパラメータ）

- 例：`filter: {...}`や`limit: 1000`

### 4. Properties to return（返すプロパティ）

- 指定された深さでのプロパティを返します

## Query example

以下はクエリの例です：

```markup
query myQuery {
  listCountry {
    items {
      name
      demographics {
        populationSize
        growthRate
      }
      deaths {
        datapoints(limit: 100) {
          timestamp
          value
        }
      }
    }
    pageInfo {
      endCursor
    }
  }
  listDemographics(
    filter: {
      and: [{ populationSize: { gte: 2 } }, { populationSize: { lte: 10 } }]
    }
  ) {
    items {
      populationSize
      growthRate
      metadata {
        key
        value
      }
    }
  }
}
```

### クエリの説明

この例では：

- **`listCountry`**：国（Country）のリストを取得
- **`listDemographics`**：人口統計（Demographics）のリストを取得し、フィルターを使用して`populationSize`が2以上10以下のものを取得
- **ネストされたプロパティ**：各クエリで複数レベルの深さでプロパティを取得可能（例：`demographics.populationSize`、`deaths.datapoints`）
- **`pageInfo`**：ページネーション情報を取得

<!-- SOURCE_END: 5-2_Retrieve data with queries in GraphQL.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-3_Tools to manage your Data Model.md -->
## File: 5-3_Tools to manage your Data Model.md

---
title: "Tools to manage your Data Model"
source: "https://learn.cognite.com/cognite-data-modeling/1877970"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Tools to manage your Data Model

以下のレッスンでは、**Data Model**（データモデル）を管理するために使用できるツールについて学習します：

## 利用可能なツール

1. **Cognite Data Fusion Toolkit**（コグナイト・データ・フュージョン・ツールキット）
2. **Python SDK**（パイソン・エスディーケー）

これらのツールを使用することで、**Data Model**の作成、管理、操作を効率的に行うことができます。

<!-- SOURCE_END: 5-3_Tools to manage your Data Model.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-4_Cognite Data Fusion Toolkit.md -->
## File: 5-4_Cognite Data Fusion Toolkit.md

---
title: "Cognite Data Fusion Toolkit"
source: "https://learn.cognite.com/cognite-data-modeling/2043264"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Cognite Data Fusion Toolkit

**Cognite Toolkit**（コグナイト・ツールキット）を使用して、**Cognite Data Fusion**プロジェクトの設定、デプロイ、管理を行うことを推奨します。

## 概要

**Cognite Toolkit**は、設定の整合性を保証し、デプロイを自動化します。**command line interface (CLI)**（コマンドラインインターフェース）があり、ローカルコンピューターから操作できるほか、**CI/CD pipeline**（シーアイ/シーディー・パイプライン）の一部としても動作します。

## 重要なポイント

### Modules（モジュール）による構成

CDFプロジェクトを効率的に管理するために、設定は**modules**（モジュール）で整理されています。

- **Official modules**（公式モジュール）：**Cognite Toolkit**には[公式モジュール](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/packages/)が含まれており、プロジェクトに[設定、ビルド、デプロイ](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/usage)できます
- **Custom modules**（カスタムモジュール）：特定のプロジェクトニーズに合わせて[カスタムモジュールを作成](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/modules/custom)することもできます

![[images/95e57709db43c393595929c889e54a73_MD5.png]]

## Getting started

**Cognite Toolkit**を開始するには、[setup](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/setup)記事の手順に従って：

- **Cognite Toolkit**をインストール
- 設定の開始点として使用するシンプルなファイル構造を確立

### 手順

1. **Cognite Toolkit**のインストール
2. 基本的なファイル構造の作成
3. プロジェクト設定の開始

## Upgrading

**Cognite Toolkit CLI**とモジュールを最新バージョンにアップグレードするには、[upgrade](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/upgrade)記事の手順に従います。

### アップグレードの重要性

- 最新の機能とバグ修正を取得
- セキュリティ更新を適用
- パフォーマンスの改善を享受

## Configure, build, and deploy modules

**Cognite Toolkit**を使用してCDFプロジェクトを効率的に管理する方法については、[configure, build, and deploy](https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/usage)記事を参照してください。

### ワークフロー

1. **Configure**（設定）：モジュールの設定を行う
2. **Build**（ビルド）：設定をビルドして検証
3. **Deploy**（デプロイ）：プロジェクトにデプロイ

## Reference information

より詳細な情報については、以下の参照ドキュメントを確認してください：

- [Official modules](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/packages/)（公式モジュール）：利用可能な公式モジュールの一覧と詳細
- [Supported resources](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library)（サポートされているリソース）：サポートされているリソースのライブラリ

### 重要なリソース

- **CLI**コマンドリファレンス
- モジュール構成のベストプラクティス
- トラブルシューティングガイド

<!-- SOURCE_END: 5-4_Cognite Data Fusion Toolkit.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-5_Python SDK.md -->
## File: 5-5_Python SDK.md

---
title: "Python SDK"
source: "https://learn.cognite.com/cognite-data-modeling/2043265"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Python SDK

**data modeling REST API (DMS)**（データモデリング・レスティーエーピーアイ・ディーエムエス）と**Cognite Python SDK**（コグナイト・パイソン・エスディーケー）を組み合わせることで、DML拡張機能よりも強力で柔軟な機能を提供します。これらは、**Cognite Data Fusion (CDF)**（コグナイト・データ・フュージョン・シーディーエフ）で産業知識グラフを管理するための推奨ツールです。

## 概要

### 重要なポイント

- **data modeling REST API (DMS)**と**Cognite Python SDK**の組み合わせは、DML拡張機能よりも強力で柔軟
- 産業知識グラフ（**industrial knowledge graphs**）を管理するための推奨ツール
- より詳細な制御と柔軟性を提供

## 推奨される理由

### パワーと柔軟性

- **DML extension**（ディーエムエル拡張機能）よりも強力な機能
- より詳細な設定とカスタマイズが可能
- 複雑なデータモデリングタスクに対応

### 用途

**Python SDK**を使用することで：

- **Data models**（データモデル）の作成と管理
- **Containers**（コンテナ）と**Views**（ビュー）の操作
- **Instances**（インスタンス）のクエリと操作
- カスタムワークフローの実装

## リソース

### Python SDKの詳細情報

- **Python SDKのドキュメント**：[Python SDK](https://developer.cognite.com/sdks/python/)で詳細を確認
- **データモデリングの例**：[Python SDKでのデータモデリングの管理方法](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html)の例を参照

### 学習リソース

- 公式ドキュメント
- コード例とチュートリアル
- APIリファレンス

## 主要な機能

### データモデリング機能

- **Container**の作成と管理
- **View**の定義と実装
- **Data model**の構築
- **Instance**の作成、更新、クエリ

### クエリ機能

- **GraphQL**クエリの実行
- フィルタリングと集約
- 階層データのクエリ
- パフォーマンス最適化

<!-- SOURCE_END: 5-5_Python SDK.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-6_Explore data using Industrial Tools.md -->
## File: 5-6_Explore data using Industrial Tools.md

---
title: "Explore data using Industrial Tools"
source: "https://learn.cognite.com/cognite-data-modeling/2043266"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Explore data using Industrial Tools

**Cognite Charts**（コグナイト・チャーツ）では、**Cognite core data model**（コグナイト・コア・データモデル）からネイティブに**time series**（タイムシリーズ）と**activity**（アクティビティ）を使用して、強力なノーコードトラブルシューティングと根本原因分析を活用できます。さらに、**Cognite core model**（コグナイト・コアモデル）と**Industrial Canvas**（インダストリアル・キャンバス）の既存モデルの両方で、データを簡単に検索して探索できるようになりました。要約すると、これらの更新により、すべてのCDFアプリケーションでより直感的で統一された検索体験が提供されます。

![[images/45a5f266b4b7007558b72f5835650414_MD5.png]]

## 概要

### 重要なポイント

- **Cognite Charts**でのノーコードトラブルシューティングと根本原因分析
- **Cognite core data model**からネイティブに**time series**と**activity**を使用
- **Industrial Canvas**と**Cognite core model**の両方でデータを検索・探索可能
- すべてのCDFアプリケーションで統一された検索体験

## 主な機能

### Cognite Chartsの機能

- **No-code troubleshooting**（ノーコード・トラブルシューティング）：コーディング不要でトラブルシューティングを実行
- **Root cause analysis**（根本原因分析）：根本原因を分析
- **Time series**と**activity**のネイティブサポート：**Cognite core data model**から直接利用

### データ検索と探索

- **Cognite core model**と**Industrial Canvas**の既存モデルの両方で検索
- 統一された検索体験
- より直感的な検索インターフェース

### メリット

- **統一性**：すべてのCDFアプリケーションで一貫した検索体験
- **効率性**：ノーコードでトラブルシューティングと分析を実行
- **アクセシビリティ**：技術者以外でも簡単にデータを探索可能

## 関連コース

**Charts**と**Canvas**について詳しく学習する場合は、以下のコースを確認してください：

- [Cognite Charts](https://learn.cognite.com/path/domain-expert-basics/cognite-charts)（コグナイト・チャーツ）：チャート機能の基本を学習
- [Industrial Canvas](https://learn.cognite.com/path/domain-expert-basics/industrial-canvas)（インダストリアル・キャンバス）：キャンバス機能の基本を学習

### 学習内容

- トラブルシューティングのベストプラクティス
- 根本原因分析の方法
- データ探索の効率的な方法
- 時間シリーズとアクティビティデータの活用

## ユースケース

### トラブルシューティング

- 設備の異常検出
- パフォーマンス問題の特定
- イベントとデータの関連付け

### データ探索

- 複数のデータソースを横断的に検索
- 時系列データの分析
- アクティビティデータの可視化

### 分析

- 根本原因の特定
- トレンドの分析
- 予測的な分析

<!-- SOURCE_END: 5-6_Explore data using Industrial Tools.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 5-7_Summary.md -->
## File: 5-7_Summary.md

---
title: "Summary"
source: "https://learn.cognite.com/cognite-data-modeling/2043267"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Summary

このレッスンでは、データモデリングの重要な概念とツールについて学習しました。以下にまとめます。

## 1. Populating containers with data

**Containers**（コンテナ）にデータを取り込む方法についてのまとめです。

### 重要なポイント

- **Containers**は、インスタンス（**nodes**と**edges**）のプロパティを保存します
- インスタンスは複数のコンテナにプロパティを持つことができます
- **Cognite transformations**（コグナイト・トランスフォーメーション）を使用して、コンテナにデータを取り込むことができます

### 概要

- **Container**はデータモデルの基本的な構成要素
- インスタンスのプロパティを効率的に管理
- **Transformations**を使用することで、自動的にデータを取り込み可能

## 2. Retrieve data with queries in GraphQL

**GraphQL**を使用してデータを取得する方法についてのまとめです。

### 重要なポイント

- **GraphQL**を使用してインスタンスをクエリできます。これはDMSクエリ機能の簡略化されたサブセットです
- クエリには以下の要素が含まれます：
	- **Query intent**（クエリ・インテント）：`query {`で開始
	- **Query types**（クエリタイプ）：4種類—`list`、`getById`、`search`、`aggregate`
	- **Query parameters**（クエリパラメータ）：フィルターやリミットを使用
	- **Return properties**（返すプロパティ）：返すデータの深さと詳細を指定

### クエリの構成

- **Query intent**：クエリの目的を定義
- **Query types**：データの取得方法を選択
- **Query parameters**：クエリをフィルタリング
- **Return properties**：必要なデータのみを取得

## 3. Tools to manage your data model

データモデルを管理するためのツールについてのまとめです。

### REST API (DMS) and Cognite Python SDK

**REST API (DMS)**と**Cognite Python SDK**の特徴：

- **data modeling REST API (DMS)**と**Cognite Python SDK**は、DML拡張機能よりも強力で柔軟な機能を提供します
- これらは、CDFで**industrial knowledge graphs**（産業知識グラフ）を管理するための推奨ツールです

### Cognite Data Fusion Toolkit

**Cognite Data Fusion Toolkit**（コグナイト・データ・フュージョン・ツールキット）の特徴：

- データモデル設定は`data_models`ディレクトリに、各エンティティタイプに特定のファイルサフィックスで保存されます
- エンティティには**spaces**（スペース）、**containers**（コンテナ）、**views**（ビュー）、**data models**（データモデル）が含まれ、それぞれ独自のファイルを持ちます
- **CDF Toolkit**は、エンティティタイプの依存関係に基づいて設定を適用します：**spaces** → **containers** → **views** → **data models**

### ツールの選択

- **Python SDK**：詳細な制御と柔軟性が必要な場合
- **Cognite Toolkit**：設定とデプロイの自動化が必要な場合

## 4. Explore data using Industrial Tools

**Industrial Tools**（インダストリアル・ツール）を使用してデータを探索する方法についてのまとめです。

### 重要なポイント

- **Cognite Charts**（コグナイト・チャーツ）は、**Cognite core data model**から**time series**（タイムシリーズ）と**activity**（アクティビティ）を使用して、ノーコードトラブルシューティングと根本原因分析を可能にします
- 更新された検索機能により、**Cognite core model**と**Industrial Canvas**（インダストリアル・キャンバス）の両方を横断的に探索できます
- これにより、CDFアプリケーション全体でより直感的で統一された検索体験が提供されます

### 主な機能

- **No-code troubleshooting**：コーディング不要でトラブルシューティング
- **Root cause analysis**：根本原因の分析
- **Unified search**：統一された検索体験
- **Cross-application exploration**：アプリケーションを横断したデータ探索

### メリット

- **効率性**：ノーコードで迅速な分析
- **一貫性**：すべてのCDFアプリケーションで統一された体験
- **アクセシビリティ**：技術者以外でも簡単にデータを探索可能

## まとめ

このレッスンでは、以下の重要な概念を学習しました：

1. **Containers**へのデータ取り込み
2. **GraphQL**を使用したデータクエリ
3. データモデル管理のためのツール
4. **Industrial Tools**を使用したデータ探索

これらの概念とツールを理解することで、Cognite Data Fusionで効率的にデータモデルを管理し、データを探索できるようになります。

<!-- SOURCE_END: 5-7_Summary.md -->

================================================================================

