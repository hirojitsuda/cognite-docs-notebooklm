# Cognite Data Workflows

Generated from: Cognite Academy (Cognite Data Workflows)
Generated at: 2025-12-29 07:55:28

---

================================================================================

<!-- SOURCE_START: 0-1_Welcome.md -->
## File: 0-1_Welcome.md

---
title: "Welcome"
source: "https://learn.cognite.com/cognite-data-workflows/2023788"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Welcome

**Cognite Data Workflows**のコースへようこそ！

## 概要

**Data Workflows**は、**Cognite Data Fusion**内に組み込まれた管理オーケストレーションサービスです。**Orchestration service**（オーケストレーションサービス）は、データエンジニアやサイエンティストが複雑なデータとデータパイプラインを効率化し、管理するのに役立つデータプラットフォームの重要なコンポーネントです。**Data workflows**を使用することで、ユーザーは**transformations**（トランスフォーメーション）、**functions**（ファンクション）、**contextualization jobs**（コンテキスト化ジョブ）などの様々なCDFプロセスをオーケストレートできます。

### 学習目標

このコースを終了すると、以下のことができるようになります：

### 1. Data Workflowsの基本原則とコンポーネントを理解

- **Cognite Data Fusion**における**data workflows**の基本原則を理解
- **Workflow**の主要コンポーネントを識別
- **Orchestration service**の役割を理解

### 2. 様々なTask Typesを識別し、区別する

**Data workflows**で利用可能な様々な**task types**を識別し、区別できます：

- **CDF Transformations**
- **Cognite Functions**
- **CDF requests**
- **Structural tasks**

### 3. 実践的な経験を得る

- **Data workflows**を設計する実践的な経験を獲得
- **Data workflows**を実行する実践的な経験を獲得
- CDF内のデータプロセスを自動化するために**data workflows**をスケジュールする実践的な経験を獲得

### 重要なポイント

- **Data Workflows**は管理オーケストレーションサービス
- 複雑なデータプロセスとデータパイプラインを効率化
- 様々なCDFプロセスをオーケストレート可能
- **Transformations**、**functions**、**contextualization jobs**を統合

<!-- SOURCE_END: 0-1_Welcome.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 0-2_How to succeed with this course.md -->
## File: 0-2_How to succeed with this course.md

---
title: "How to succeed with this course?"
source: "https://learn.cognite.com/cognite-data-workflows/2026033"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# How to succeed with this course?

このコースは、理論的知識と実践的スキルの両方で構成されています。基本概念から始まり、より高度な演習へと進む、相互に構築されるレッスンを進めていきます。各レッスンでは、詳細な説明、例、続いて実践的な演習を提供します。

## 成功するために必要なこと

### Before（開始前）

コースを開始する前に、前提条件を完了してください：

- [Cognite Data Fusion Fundamentals learning path](https://learn.cognite.com/path/cognite-data-fusion-fundamentals)（コグナイト・データ・フュージョン・ファンダメンタル学習パス）を完了
- [Learn to Use the Cognite Python SDK course](https://learn.cognite.com/learn-to-use-the-cognite-python-sdk)（コグナイト・パイソン・エスディーケーを使用する方法を学ぶコース）を完了
- コンピューターに**Python**、**Git**、お好みの**Python IDE**（**Visual Studio Code**または**Jupyter Notebooks**）をインストール
- **pip**または**poetry**を使用して[cognite-sdk](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/)と**pandas**をインストール
- [GitHub](https://github.com/)アカウントを持つ（必須ではありません。GitHubアカウントがなくても、HTTPSメソッド経由でのみコースリポジトリをクローンできます）

### During（コース期間中）

**Concepts**（コンセプト）セクションを完了して、**Data Workflows**の基礎知識を構築します。その後、テストとコースの**Hands-on**（ハンズオン）セクションで知識を証明します。

- このコース専用の[リポジトリをクローン](https://github.com/cognitedata/academy-data-workflows)します
- 手順を読み、クローンしたリポジトリ内の提供されたノートブックでコードを実行します
- 2つの画面があると便利です：1つはデモンストレーションを見るため、もう1つはコードを実行するため
- ヘルプが必要な場合は、Cogniteコミュニティ、[Cognite Hub](https://hub.cognite.com/)、または[Academy Discussions](https://hub.cognite.com/groups/academy-discussions-175)でインストラクターや仲間と連絡してください。コース演習でヘルプが必要な場合は、コースとレッスン名を記載してください。できるだけ早くお手伝いします

![[images/f80eb4293fed5452393a7d67fc1e16d7_MD5.png]]

### 重要なポイント

#### 前提条件の確認

- **Cognite Data Fusion Fundamentals**の完了
- **Cognite Python SDK**の基本的な理解
- **Python**、**Git**、**Python IDE**のインストール
- **cognite-sdk**と**pandas**のインストール

#### 学習アプローチ

- **理論的知識**と**実践的スキル**の両方を学習
- 基本概念から高度な演習へと段階的に進行
- 各レッスンで詳細な説明と実践的な演習を提供

#### リソース

- コース専用のGitHubリポジトリを活用
- 2つの画面を使用して効率的に学習
- CogniteコミュニティやHubでサポートを求める

### 成功のためのヒント

1. **前提条件を確実に完了**する
2. **提供されたノートブックを実際に実行**する
3. **質問がある場合は、コース名とレッスン名を記載**してサポートを求める
4. **コミュニティリソースを活用**する

<!-- SOURCE_END: 0-2_How to succeed with this course.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 0_Cognite Data Workflows.md -->
## File: 0_Cognite Data Workflows.md

---
title: "Cognite Data Workflows"
source: "https://learn.cognite.com/cognite-data-workflows"
created: 2025-11-03
description: "Learn how to use data workflows in Cognite Data Fusion to automate and orchestrate complex data processes."
tags:
  - "CDF"
  - "CogniteDataFusion"
image: "https://cc.sj-cdn.net/instructor/hhi6o3bc6ve-cognite-academy/themes/mb9vgd2babsm/header-logo.1660031198.png"
---

# Cognite Data Workflows

[Curriculum](https://learn.cognite.com/#)

## Course Overview 1.5 hrs (Show All)

---

[About this course](https://learn.cognite.com/#)

## About this course

このコースは、**Cognite Data Fusion (CDF)**（コグナイト・データ・フュージョン・シーディーエフ）内の**data workflows**（データ・ワークフロー）に関する包括的な紹介を提供します。これは強力な管理プロセスオーケストレーションサービスです。参加者は、**CDF Transformations**（シーディーエフ・トランスフォーメーション）、**Cognite Functions**（コグナイト・ファンクション）、**dynamic tasks**（動的タスク）、**API requests**（エーピーアイ・リクエスト）など、複雑で相互依存するプロセスを効果的に調整および自動化する方法を学習します。このコースは、CDF内のデータオペレーションを最適化し、効率化する**data workflows**を作成、実行、管理するために必要なスキルを学習者に提供することを目的としています。

### Learning objectives（学習目標）

このコースを終了すると、以下のことができるようになります：

1. **Describe Core Concepts of Data Workflows**（Data Workflowsの主要概念を説明する）：**Cognite Data Fusion**における**data workflows**の基本原則とコンポーネントを理解します。

2. **Explain Different Task Types**（異なるタスクタイプを説明する）：**CDF Transformations**、**Cognite Functions**、**dynamic tasks**、**API requests**を含む、**data workflows**で利用可能な様々な**task types**（タスクタイプ）を識別し、区別します。

3. **Create, Run, and Schedule Data Workflows**（Data Workflowsを作成、実行、スケジュールする）：CDF内のデータプロセスを自動化するために、**data workflows**を設計、実行、スケジュールする実践的な経験を得ます。

### Who should take this course?（このコースを受講すべき人）

**Data managers**（データマネージャー）、**data engineers**（データエンジニア）、**data scientists**（データサイエンティスト）

### Instructor（インストラクター）

このコースは**Cognite Academy**（コグナイト・アカデミー）によって開発されました。専門家：

![[images/c33eda087134a35617b2de3d6a235422_MD5.png]]

**Jørgen Lund**（ヨルゲン・ルンド）  
Product Manager - Data Workflows（プロダクト・マネージャー - データ・ワークフロー）

![[images/55932af3b380e7f55355e4dc6bf91fed_MD5.png]]

**Bert Verstraete**（ベルト・ベルストラーテ）  
Software engineer - Data Workflows（ソフトウェアエンジニア - データ・ワークフロー）

### コースの構成

このコースでは、以下の重要なトピックをカバーします：

- **Data workflows**の基本概念
- 異なる**task types**の理解
- **Workflow**の作成と実行
- **Triggers**による自動化
- エラー処理と堅牢性の向上

### 期待される成果

このコースを修了することで：

- **Data workflows**の基本原則を理解
- 異なる**task types**を識別し、適切に使用
- **Data workflows**を設計、実行、スケジュールする実践的なスキルを習得
- CDF内のデータプロセスを自動化する能力を獲得

<!-- SOURCE_END: 0_Cognite Data Workflows.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-1_Why data workflows.md -->
## File: 1-1_Why data workflows.md

---
title: "Why data workflows?"
source: "https://learn.cognite.com/cognite-data-workflows/2023796"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Why data workflows?

## Summary

### なぜData Workflowsが必要なのか？

**Data workflows**以前は、ユーザーは複数の相互接続されたプロセスを手動で処理していました。これにより、古いデータや頻繁な失敗などの非効率性が発生していました。**Cognite Data Fusion**の**data workflows**は、データパイプラインを自動化し、オーケストレートすることで、これらの課題に対処します。タスクが正しい順序でトリガーされ、データが常に最新であることを保証します。この**orchestration service**（オーケストレーションサービス）により、モジュール化された再利用可能なプロセスが可能になり、パイプラインの堅牢性が向上し、手動作業が最小化され、全体的な可観測性とパフォーマンスが向上します。

## 重要なポイント

### Data Workflows以前の問題

- **手動処理**：複数の相互接続されたプロセスを手動で処理
- **非効率性**：
  - 古いデータ（**outdated data**）
  - 頻繁な失敗（**frequent failures**）

### Data Workflowsの利点

- **自動化とオーケストレーション**：データパイプラインを自動化し、オーケストレート
- **正しい順序でのタスク実行**：タスクが正しい順序でトリガーされることを保証
- **常に最新のデータ**：データが常に最新であることを保証
- **モジュール化と再利用性**：モジュール化された再利用可能なプロセスを可能にする
- **堅牢性の向上**：パイプラインの堅牢性が向上
- **手動作業の最小化**：手動作業が最小化される
- **可観測性とパフォーマンスの向上**：全体的な可観測性とパフォーマンスが向上

### 解決される課題

1. **Outdated data**（古いデータ）：データを常に最新に保つ
2. **Frequent failures**（頻繁な失敗）：堅牢性の向上により失敗を減らす
3. **Manual intervention**（手動介入）：自動化により手動作業を最小化
4. **Complex interdependencies**（複雑な相互依存関係）：適切な順序でタスクを実行

### ユースケース

- **Data pipelines**（データパイプライン）の自動化
- **Complex processes**（複雑なプロセス）のオーケストレーション
- **Data freshness**（データの鮮度）の確保
- **Process reliability**（プロセスの信頼性）の向上

## 動画コンテンツ

[[images/7f21a3aecfcce47d871c06895e25aac6_MD5.mp4|Open: 1-1_Why data workflows日本語訳.mp4]]
![[images/7f21a3aecfcce47d871c06895e25aac6_MD5.mp4]]

<!-- SOURCE_END: 1-1_Why data workflows.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-2_Core concepts of data workflows.md -->
## File: 1-2_Core concepts of data workflows.md

---
title: "Core concepts of data workflows"
source: "https://learn.cognite.com/cognite-data-workflows/2023797"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Core concepts of data workflows

## Summary

### Core concepts of data workflows

**Cognite Data Fusion (CDF)**の**data workflows**は、オーケストラの指揮者のように、相互依存するデータプロセスを管理する構造化システムを提供します。様々なタスク間で調和した動作を確保します。これらの**workflows**は、データ変換、関数実行、**API requests**などのタスクの実行順序と依存関係を管理する上で重要です。主要なコンポーネントには以下が含まれます：

## 主要コンポーネント

### Workflow Definitions

- **Schematic blueprints**（スキーマティック・ブループリント）：タスクのシーケンスと依存関係を詳述する設計図
- **Task sequence and dependencies**（タスクシーケンスと依存関係）を定義

### Workflow Version

- **Iterative updates**（反復的な更新）：**workflow**を中断することなく反復的な更新を促進
- **Change management**（変更管理）：ユーザーが変更の重要性に基づいて新しいバージョンが必要かを決定
- **Version control**（バージョン管理）：異なるバージョンで異なる反復を処理

### Tasks

- **Essential unit**（必須単位）：各**task**は特定のプロセスをトリガーする必須単位
- **Task types**：**transformations**、**function executions**、**API requests**など

### Workflow Executions

- **Documentation and monitoring**（ドキュメント化と監視）：すべての実行を記録し、監視
- **Process improvement**（プロセス改善）：プロセスを改善し、最適化するためのデータを提供

## 重要なポイント

### オーケストレーションの比喩

**Data workflows**は、オーケストラの指揮者のように機能します：
- **Harmonious operation**：様々なタスク間で調和した動作を確保
- **Coordination**：相互依存するプロセスの調整
- **Timing**：適切なタイミングでタスクを実行

### 管理されるプロセス

- **Data transformations**（データ変換）
- **Function executions**（関数実行）
- **API requests**（エーピーアイ・リクエスト）

### システムの利点

- **Structured management**（構造化された管理）：相互依存するプロセスの構造化された管理
- **Execution order control**（実行順序の制御）：タスクの実行順序を制御
- **Dependency management**（依存関係の管理）：依存関係を効果的に管理
- **Monitoring and optimization**（監視と最適化）：実行を監視し、最適化

## 動画コンテンツ

[[images/6216a6302088af18d51ec213cfcd6a5c_MD5.mp4|Open: Core concepts of data workflows日本語訳.mp4]]
![[images/6216a6302088af18d51ec213cfcd6a5c_MD5.mp4]]

## 追加リソース

詳細については、[Cognite Documentation](https://docs.cognite.com/cdf/data_workflows/)をご確認ください。

<!-- SOURCE_END: 1-2_Core concepts of data workflows.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-3_Defining a simple workflow.md -->
## File: 1-3_Defining a simple workflow.md

---
title: "Defining a simple workflow"
source: "https://learn.cognite.com/cognite-data-workflows/2023798"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Defining a simple workflow

## Summary

### Defining a simple workflow

**Tasks**（タスク）は**workflow**の基本コンポーネントであり、それぞれが特定のステップを表します。**Cognite Data Fusion**には、3つの主要な**task types**（タスクタイプ）があります：

## Task Types

### 1. Cognite Transformation tasks

- **Cognite Transformation tasks**（コグナイト・トランスフォーメーション・タスク）：CDF内の**transformation jobs**を実行

### 2. Cognite Function tasks

- **Cognite Function tasks**（コグナイト・ファンクション・タスク）：**Cognite Functions**を実行し、完了を待機

### 3. Cognite Data Fusion (CDF) request tasks

- **CDF request tasks**（シーディーエフ・リクエスト・タスク）：**API requests**を実行

## Task Configuration

**Tasks**は、実行を定義する**externalId**、**type**、**parameters**などのプロパティで設定されます。さらに、**tasks**間に**dependencies**（依存関係）を確立でき、依存関係が完了した後にのみ**task**が実行されることを保証します。これらの依存関係と並列実行を活用することで、**workflows**を効率化し、時間を節約できます。

### 重要なポイント

- **External ID**：タスクの一意の識別子
- **Type**：タスクのタイプを定義
- **Parameters**：タスクの実行パラメータ
- **Dependencies**：タスク間の依存関係を設定
- **Parallel execution**：並列実行により効率を向上

### 依存関係の利点

- **Sequential execution**（順次実行）：依存関係に基づいて順次実行
- **Parallel optimization**（並列最適化）：依存関係を効果的に活用して並列実行を最適化
- **Time savings**（時間節約）：効率的な実行により時間を節約
- **Reliability**（信頼性）：依存関係が満たされた後にのみ実行

## 動画コンテンツ

[[images/f2da1fee2d7530621adf0f07d1643988_MD5.mp4|Open: Defining a simple workflow日本語訳.mp4]]
![[images/f2da1fee2d7530621adf0f07d1643988_MD5.mp4]]

<!-- SOURCE_END: 1-3_Defining a simple workflow.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-4_Task and workflow robustness.md -->
## File: 1-4_Task and workflow robustness.md

---
title: "Task and workflow robustness"
source: "https://learn.cognite.com/cognite-data-workflows/2023800"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Task and workflow robustness

## Summary

### Task and workflow robustness

**Data workflows**は、**timeout**、**retries**、**onFailure**パラメータなどの機能を通じてエラーと失敗を処理し、実行中の堅牢性を確保します。**Timeout**パラメータは、タスク完了の最大継続時間を設定し、制限を超えたタスクを終了します。**Retries**パラメータは、失敗の場合にタスクの再実行を可能にし、デフォルトで3回の再試行があります。**onFailure**パラメータは、タスクの重要性に応じて**workflow**を中止または継続することを可能にし、エラーの柔軟な処理を可能にします。さらに、**workflows**には24時間の実行制限があり、未完了のタスクがキャンセルされ、効率と信頼性が維持されます。

## 堅牢性機能

### 1. Timeout

- **Maximum duration**（最大継続時間）：タスク完了の最大継続時間を設定
- **Task termination**（タスク終了）：制限を超えたタスクを終了
- **Default value**：デフォルトは1時間
- **Maximum value**：最大12時間まで設定可能

### 2. Retries

- **Task re-execution**（タスク再実行）：失敗の場合にタスクの再実行を可能
- **Default retries**：デフォルトで3回の再試行
- **Maximum retries**：最大10回まで設定可能
- **Failure handling**：失敗時の自動再試行

### 3. onFailure

- **Flexible error handling**（柔軟なエラー処理）：タスクの重要性に応じて柔軟にエラーを処理
- **Abort workflow**：タスクが重要である場合、**workflow**を中止
- **Skip task**：タスクが重要でない場合、タスクをスキップして継続
- **Task importance**（タスクの重要性）：タスクの重要性に基づいて処理方法を決定

### 4. 24-hour execution limit

- **Workflow level robustness**（ワークフローレベルの堅牢性）：**workflow**レベルでの堅牢性を確保
- **Task cancellation**（タスクキャンセル）：24時間制限に達すると、未完了のタスクがキャンセル
- **Efficiency and reliability**（効率と信頼性）：効率と信頼性を維持

## 重要なポイント

### エラー処理の利点

- **Automatic retry**（自動再試行）：失敗時に自動的に再試行
- **Timeout protection**（タイムアウト保護）：長時間実行されるタスクを保護
- **Flexible handling**（柔軟な処理）：タスクの重要性に応じて柔軟に処理
- **Resource management**（リソース管理）：リソースを効率的に管理

### 堅牢性の向上

- **Error resilience**（エラー回復力）：エラーに対してより回復力が高い
- **Process reliability**（プロセス信頼性）：プロセスの信頼性が向上
- **Efficient resource usage**（効率的なリソース使用）：リソースを効率的に使用

## 動画コンテンツ

[[images/9dbc197deeea3e8b10329fbb78984250_MD5.mp4|Open: Task and workflow robustness日本語訳.mp4]]
![[images/9dbc197deeea3e8b10329fbb78984250_MD5.mp4]]

<!-- SOURCE_END: 1-4_Task and workflow robustness.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-5_Structural tasks.md -->
## File: 1-5_Structural tasks.md

---
title: "Structural tasks"
source: "https://learn.cognite.com/cognite-data-workflows/2023801"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Structural tasks

## Summary

### Structural tasks

**Data workflows**には、3種類の**structural tasks**（構造的タスク）があります。

## Structural Task Types

### 1. Dynamic tasks

- **Runtime definition**（実行時定義）：実行時に定義されるタスクを実行
- **Adaptable workflows**（適応可能なワークフロー）：適応性とスケーラブルな**workflows**を可能にする
- **Dynamic values**（動的値）：以前のステップからの動的な値を参照
- **Varying data sets**（変動するデータセット）：様々なデータセットを処理するのに有用

### 2. Subworkflow tasks

- **Task grouping**（タスクグループ化）：共有依存関係を持つタスクをグループ化
- **Simplified definitions**（簡素化された定義）：**workflow definitions**を簡素化
- **Static definition**（静的定義）：**workflow definition**に静的に定義

### 3. Asynchronous completion of tasks

- **External events**（外部イベント）：外部アクションを待つ
- **Manual intervention**（手動介入）：手動介入を待つ
- **External processing**（外部処理）：外部システムでのデータ処理を待つ
- **In-progress status**（進行中ステータス）：外部アクションが完了するまで進行中のまま

## 重要なポイント

### Dynamic tasksの用途

- **Real-time data processing**（リアルタイムデータ処理）：リアルタイムのデータ入力に基づいてタスクを定義
- **Variable data sets**（変動するデータセット）：更新されたデータを持つ**assets**のKPIを計算するなど、様々なデータセットを処理
- **Scalability**（スケーラビリティ）：データ量に応じてスケール

### Subworkflow tasksの利点

- **Simplified workflow definitions**（簡素化されたワークフロー定義）：複雑な**workflow definitions**を簡素化
- **Shared dependencies**（共有依存関係）：共通の依存関係を持つタスクをグループ化
- **Maintainability**（保守性）：**workflow definitions**をより保守しやすくする

### Asynchronous completionの利点

- **External integration**（外部統合）：外部システムとの統合を可能にする
- **Human in the loop**（人間の介入）：手動介入を**workflow**に組み込む
- **Flexible processing**（柔軟な処理）：長時間実行されるジョブを処理

## 動画コンテンツ

[[images/eb511e85244b1a30d3b2e429fe91217f_MD5.mp4|Open: Structural tasks日本語訳.mp4]]
![[images/eb511e85244b1a30d3b2e429fe91217f_MD5.mp4]]

<!-- SOURCE_END: 1-5_Structural tasks.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-6_Triggers in Data Workflows.md -->
## File: 1-6_Triggers in Data Workflows.md

---
title: "Triggers in Data Workflows"
source: "https://learn.cognite.com/cognite-data-workflows/2106127"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Triggers in Data Workflows

## Summary

### Triggers in Data Workflows

**Cognite Data Workflows**の**Triggers**機能により、ユーザーは**data workflows**の実行を自動化し、運用効率と信頼性を向上させることができます。**Trigger**タイプは2つあります：

## Trigger Types

### 1. Schedule-based triggers

- **Scheduled execution**（スケジュール実行）：指定された間隔で**workflows**を実行
- **Cron expressions**（クロン表現）：**cron expressions**を使用してスケジュールを定義
- **Regular tasks**（定期的なタスク）：データバックアップなど、定期的で予測可能なタスクに理想的
- **Automated scheduling**（自動スケジュール）：手動介入なしで自動的にスケジュール

### 2. Event-based triggers

- **Event-driven execution**（イベント駆動実行）：特定のデータ変更に応答して**workflows**を開始
- **Responsive tasks**（応答性の高いタスク）：データ取り込みなど、応答性の高いタスクに適している
- **Data change detection**（データ変更検出）：データ変更を検出して自動的に実行
- **Real-time processing**（リアルタイム処理）：リアルタイムで変更を処理

## Trigger Properties

**Triggers**は、**trigger rules**、**targets**、**input data**、**run history monitoring**などのプロパティでカスタマイズでき、**workflow**自動化の詳細な制御を提供します。

### 重要なポイント

- **Trigger rules**（トリガールール）：**workflow**を開始する条件を指定
- **Trigger targets**（トリガーターゲット）：自動化する**workflow**を指定
- **Input data**（入力データ）：**workflow**実行に渡されるデータ
- **Run history monitoring**（実行履歴監視）：**trigger**アクティビティを監視

### 利点

- **Automation**（自動化）：繰り返しタスクを効率的に処理
- **Quick response**（迅速な対応）：データ基盤の変更に迅速に対応
- **Operational efficiency**（運用効率）：運用効率を向上
- **Reliability**（信頼性）：信頼性を向上

## 動画コンテンツ

[[images/c0b26140b2d0177aa20ac39ae7cafb7b_MD5.mp4|Open: Triggers in Data Workflows日本語訳.mp4]]
![[images/c0b26140b2d0177aa20ac39ae7cafb7b_MD5.mp4]]

<!-- SOURCE_END: 1-6_Triggers in Data Workflows.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-7_Get ready for hands-on.md -->
## File: 1-7_Get ready for hands-on.md

---
title: "Get ready for hands-on"
source: "https://learn.cognite.com/cognite-data-workflows/2023802"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Get ready for hands-on

## 実践演習を成功させるために

### 必要な準備

実践演習を成功させるために、以下を準備してください：

### 1. ソフトウェアのインストール

- **Python**、**Git**、お好みの**Python IDE**（**Visual Studio Code**または**Jupyter Notebooks**）をコンピューターにインストール
- **pip**または**poetry**を使用して[cognite-sdk](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/)と**pandas**をインストール

### 2. GitHubアカウント

- [GitHub](https://github.com/)アカウントを持つ
  - **注**：必須ではありません。GitHubアカウントがなくても、HTTPSメソッド経由でのみコースリポジトリをクローンできます

### 3. リポジトリのクローン

- このコース専用の[リポジトリをクローン](https://github.com/cognitedata/academy-data-workflows)
- 手順を読み、クローンしたリポジトリ内の提供されたノートブックでコードを実行

## 重要な注意事項

### Important!（重要！）

**このコースで使用するプロジェクトはトレーニング環境にあり、コースの他の学習者も使用します。そのため、作成するworkflowsとtasksを区別するために、"FirstnameBirthyear"（例：madina1997）プレフィックスを使用してください。FirstnameBirthyearの命名規則が言及されている場合は、FirstnameBirthyearを一意の名前と生年に置き換える必要があります。**

### 重要なポイント

- **トレーニング環境**：共有環境であることに注意
- **一意のプレフィックス**：**"FirstnameBirthyear"**プレフィックスを使用
- **命名規則**：すべての**workflows**と**tasks**に一意のプレフィックスを適用
- **例**：madina1997、john1985など

### 準備チェックリスト

- [ ] **Python**がインストールされている
- [ ] **Git**がインストールされている
- [ ] **Python IDE**（Visual Studio CodeまたはJupyter Notebooks）がインストールされている
- [ ] **cognite-sdk**と**pandas**がインストールされている
- [ ] GitHubリポジトリがクローンされている
- [ ] **FirstnameBirthyear**プレフィックスを理解している

<!-- SOURCE_END: 1-7_Get ready for hands-on.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 1-8_Key takeaways.md -->
## File: 1-8_Key takeaways.md

---
title: "Key takeaways"
source: "https://learn.cognite.com/cognite-data-workflows/2023804"
created: 2025-11-03
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---

# Key takeaways

**Cognite Data Fusion**の**data workflows**は、データパイプラインを管理するための堅牢で、スケーラブルで、柔軟な方法を提供します。このサービスは、可観測性とガバナンスを向上させ、エラーを最小化し、**dynamic tasks**、**subworkflows**、**asynchronous completion**、エラー処理などの高度な機能により、データタスクの効率的なオーケストレーションを保証します。

## 1. Introduction to Data Workflows

### 重要なポイント

- **Data workflows**は、**Cognite Data Fusion (CDF)**内の管理オーケストレーションサービスであり、複雑なデータと機械学習パイプラインを効率化します
- タスクを効率的に同期し、プロセス間の相互依存関係を管理することで、データが常に最新であることを保証し、堅牢性とパフォーマンスを向上させます

### 主な機能

- **Streamlining**（効率化）：複雑なデータと機械学習パイプラインを効率化
- **Synchronization**（同期）：タスクを効率的に同期
- **Dependency management**（依存関係管理）：プロセス間の相互依存関係を管理
- **Data freshness**（データの鮮度）：データが常に最新であることを保証

## 2. Workflow Structure

### 重要なポイント

- **Workflow**は一意の**external ID**によって識別され、異なる反復を処理するために複数の**versions**を持つことができます
- **Workflow definitions**はタスクの設計図として機能し、そのシーケンスと依存関係を示します
- **Tasks**は**workflow**内の作業の基本単位であり、**transformations**、**API requests**などを含むことができます
- **Workflow executions**は各実行を記録し、トラブルシューティングと将来の実行の最適化に役立ちます

### 構造の要素

- **Workflow**：一意の**external ID**で識別
- **Versions**：異なる反復を処理
- **Workflow definitions**：タスクの設計図
- **Tasks**：作業の基本単位
- **Workflow executions**：実行の記録と監視

## 3. Simple Task Types (currently supported)

### 現在サポートされているタスクタイプ

- **Cognite Transformation Task**（コグナイト・トランスフォーメーション・タスク）：CDF内の**transformation job**をトリガー
- **Cognite Function Task**（コグナイト・ファンクション・タスク）：**Cognite Function**を実行し、完了を待機
- **CDF Request Task**（シーディーエフ・リクエスト・タスク）：データ取得や更新などのアクションのために、CDF APIに対して**GET**または**POST**リクエストを実行

### タスクタイプの特徴

- **Transformation tasks**：データ変換を実行
- **Function tasks**：関数を実行
- **Request tasks**：APIリクエストを実行

## 4. Task Dependencies and Optimization

### 重要なポイント

- **Tasks**間に**dependencies**を設定でき、前のタスクが正常に完了した場合にのみタスクが実行されることを保証します
- 依存関係を効果的に活用することで、**workflows**は並列タスクを実行でき、時間を節約し、効率を向上させます

### 最適化の利点

- **Sequential execution**（順次実行）：依存関係に基づいて順次実行
- **Parallel execution**（並列実行）：依存関係を活用して並列実行
- **Time savings**（時間節約）：時間を節約
- **Efficiency**（効率）：効率を向上

## 5. Handling Task Failures

### エラー処理機能

- **Timeouts**（タイムアウト）：タスクが実行できる最大継続時間を設定。デフォルトは1時間
- **Retries**（再試行）：タスクが失敗した場合、最大10回まで再試行可能
- **onFailure**（失敗時の処理）：タスクの完了が重要かどうかに基づいて、**workflow**を中止するか、失敗したタスクをスキップするように設定

### 堅牢性の向上

- **Error handling**（エラー処理）：エラーを効果的に処理
- **Automatic retry**（自動再試行）：失敗時に自動的に再試行
- **Flexible failure handling**（柔軟な失敗処理）：タスクの重要性に応じて柔軟に処理

## 6. References and Dynamic Input

### 重要なポイント

- **References**（参照）を使用して、タスク間で動的にデータを渡します。これらの参照は、**workflow inputs**（ワークフロー入力）または他のタスクの出力を指すことができ、柔軟性と適応性を向上させます
- 静的および動的入力がサポートされており、**workflows**が事前定義されたデータとリアルタイムデータの両方を処理できるようになります

### 入力タイプ

- **Static inputs**（静的入力）：事前定義されたデータ
- **Dynamic inputs**（動的入力）：リアルタイムデータ
- **References**：タスク間でデータを渡す

## 7. Structural Task Types

### 構造的タスクタイプ

- **Dynamic Tasks**（動的タスク）：実行時に決定される複数のタスクを実行し、リアルタイムのデータ入力と出力に適応可能な**workflows**を可能にします
- **Subworkflow Tasks**（サブワークフロータスク）：静的に定義され、共通の依存関係を共有する並列タスクで複雑な**workflows**を簡素化します
- **Asynchronous Task Completion**（非同期タスク完了）：**isAsyncComplete**パラメータを使用して、外部イベントや手動介入を待つようにタスクを設定でき、外部コールバックがステータスを更新するまでタスクを進行中のままにします

### 構造的タスクの利点

- **Adaptability**（適応性）：リアルタイムデータに適応
- **Scalability**（スケーラビリティ）：スケーラブルな**workflows**を可能にする
- **Simplification**（簡素化）：複雑な**workflows**を簡素化
- **External integration**（外部統合）：外部イベントや手動介入を統合

## まとめ

**Cognite Data Fusion**の**data workflows**は、以下の利点を提供します：

- **Robustness**（堅牢性）：堅牢なデータパイプライン管理
- **Scalability**（スケーラビリティ）：スケーラブルなソリューション
- **Flexibility**（柔軟性）：柔軟なデータプロセス管理
- **Observability**（可観測性）：可観測性とガバナンスの向上
- **Error minimization**（エラー最小化）：エラーを最小化
- **Efficient orchestration**（効率的なオーケストレーション）：データタスクの効率的なオーケストレーション

<!-- SOURCE_END: 1-8_Key takeaways.md -->

================================================================================

