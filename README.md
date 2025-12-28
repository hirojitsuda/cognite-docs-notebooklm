# notebooklm_output ディレクトリ構成ガイド

このディレクトリには、Cogniteの公式ドキュメントを統合したMarkdownファイルが、用途別にカテゴリごとに整理されています。

## 📁 ディレクトリ構成

```
notebooklm_output/
├── 01_API/                    # API関連ドキュメント
├── 02_SDK/                    # SDK関連ドキュメント
├── 03_コア機能/               # Cogniteのコア機能・製品ドキュメント
├── 04_データモデリング/       # データモデリング・ベストプラクティス
├── 05_Cognite_Hub/            # Cognite Hubのコンテンツ
├── 06_Confluence/             # Confluenceのコンテンツ
├── 07_Google_Drive/           # Google Driveのコンテンツ
└── 08_デモ・テンプレート/     # デモデータ・テンプレート
```

---

## 📚 各カテゴリの詳細

### 01_API

**目的**: Cognite Data Fusion (CDF) APIのリファレンスドキュメント

**含まれる内容**:
- `api-docs_20230101_combined.md` - Cognite APIの完全なリファレンス（OpenAPI仕様から生成）

**使用シーン**:
- APIエンドポイントの詳細を調べる
- リクエスト/レスポンスの仕様を確認
- APIバージョン情報を参照
- 認証方法やエラーハンドリングを理解

**推奨ユーザー**: API開発者、バックエンドエンジニア

---

### 02_SDK

**目的**: Cognite SDK（Software Development Kit）のドキュメント

**含まれる内容**:
- `cognite-sdk-python_combined.md` - Python SDKのドキュメント
- `cognite-sdk-js_combined.md` - JavaScript/TypeScript SDKのドキュメント
- `sdks_combined.md` - SDK全般のドキュメント

**使用シーン**:
- SDKのインストール方法を確認
- SDKの使い方やコード例を参照
- 各SDKの機能比較
- SDKのベストプラクティスを学ぶ

**推奨ユーザー**: アプリケーション開発者、フロントエンド/バックエンドエンジニア

---

### 03_コア機能

**目的**: Cogniteの主要製品・機能のドキュメント

**含まれる内容**:
- `cdf_combined.md` - Cognite Data Fusionのコア機能
- `dev_combined.md` - 開発者向けポータル・ツール
- `infield_combined.md` - InField（フィールドサービス管理）
- `infield_vnext_combined.md` - InField vNext（次世代版）
- `inrobot_combined.md` - InRobot（ロボット・自動化）
- `maintain_combined.md` - Maintain（メンテナンス管理）
- `portal_combined.md` - Portal（ポータル機能）
- `reveal_combined.md` - Reveal（3D可視化）
- `toolkit_combined.md` - Toolkit（開発ツールキット）
- `pygen_combined.md` - PyGen（Pythonコード生成）
- `neat_combined.md` - NEAT（データモデリングツール）
- `library_combined.md` - Library（ライブラリ・コンポーネント）

**使用シーン**:
- 各製品の機能を理解する
- エンドユーザー向けマニュアルを参照
- 管理者向け設定ガイドを確認
- 製品間の連携方法を調べる

**推奨ユーザー**: エンドユーザー、管理者、プロダクトマネージャー、コンサルタント

---

### 04_データモデリング

**目的**: データモデリングのベストプラクティス、例、スキリング資料

**含まれる内容**:
- `data-model-examples_combined.md` - データモデルの実例集
- `cognite_dm_skilling_combined.md` - データモデリングのスキリング資料
- `cognite-function-examples_combined.md` - Cognite Functionsの使用例
- `open-industrial-data_combined.md` - オープンインダストリアルデータ

**使用シーン**:
- データモデルの設計パターンを学ぶ
- 既存のデータモデル例を参考にする
- データモデリングのベストプラクティスを確認
- トレーニング資料として使用

**推奨ユーザー**: データモデラー、データアーキテクト、CDM（Core Data Model）設計者

---

### 05_Cognite_Hub

**目的**: Cognite Hub（コミュニティプラットフォーム）のコンテンツ

**含まれる内容**:
- `cognite-hub_all_combined.md` - Cognite Hubの全コンテンツ
- `cognite-hub_best_practices_(internal)_combined.md` - ベストプラクティス（内部向け）
- `cognite-hub_deployment_packs_library_combined.md` - デプロイメントパックライブラリ

**使用シーン**:
- コミュニティのベストプラクティスを参照
- デプロイメントパックを探す
- 他のユーザーの実装例を学ぶ
- 内部向けのガイドラインを確認

**推奨ユーザー**: すべてのユーザー、特に実装を進める開発者・コンサルタント

---

### 06_Confluence

**目的**: Confluence（社内Wiki）からエクスポートしたドキュメント

**含まれる内容**:
- `confluence_all_combined.md` - Confluenceの全コンテンツ

**使用シーン**:
- 社内のナレッジベースを参照
- プロジェクト固有のドキュメントを確認
- チーム内の情報共有

**推奨ユーザー**: 社内ユーザー、プロジェクトメンバー

---

### 07_Google_Drive

**目的**: Google Driveから取得したドキュメント

**含まれる内容**:
- `google-drive_関連リンク_combined.md` - Google Driveの関連リンク集

**使用シーン**:
- 外部ドキュメントへのリンクを参照
- 関連リソースを探す

**推奨ユーザー**: すべてのユーザー

---

### 08_デモ・テンプレート

**目的**: デモデータ、テンプレート、サンプルプロジェクト

**含まれる内容**:
- `uat-demo-data-jp_combined.md` - UATデモデータ（日本語版）

**使用シーン**:
- デモ環境のセットアップ
- サンプルデータの参照
- テンプレートとして使用
- テストデータの準備

**推奨ユーザー**: 開発者、テストエンジニア、デモ担当者

---

## 🎯 使用シーン別の推奨カテゴリ

### API開発を行う場合
- **必須**: `01_API/`, `02_SDK/`
- **推奨**: `03_コア機能/dev_combined.md`, `05_Cognite_Hub/`

### データモデリングを行う場合
- **必須**: `04_データモデリング/`
- **推奨**: `05_Cognite_Hub/cognite-hub_best_practices_*.md`

### アプリケーション開発を行う場合
- **必須**: `02_SDK/`, `03_コア機能/`
- **推奨**: `01_API/`, `05_Cognite_Hub/`

### エンドユーザー向けマニュアルが必要な場合
- **必須**: `03_コア機能/`（該当製品のファイル）
- **推奨**: `05_Cognite_Hub/`

### ベストプラクティスを学ぶ場合
- **必須**: `04_データモデリング/`, `05_Cognite_Hub/`
- **推奨**: `06_Confluence/`, `07_Google_Drive/`

---

## 📤 NotebookLM / Claudeプロジェクトナレッジへのアップロード

### 全ファイルをアップロードする場合
すべてのカテゴリのファイルをアップロードすることで、包括的なナレッジベースを構築できます。

### カテゴリ別に選択的にアップロードする場合
用途に応じて必要なカテゴリのみをアップロードすることで、より焦点を絞ったナレッジベースを構築できます。

**例**:
- **API開発プロジェクト**: `01_API/` + `02_SDK/`
- **データモデリングプロジェクト**: `04_データモデリング/` + `05_Cognite_Hub/`
- **製品利用プロジェクト**: `03_コア機能/`（該当製品のみ）

### ファイル形式について
- **NotebookLM**: Markdownファイル（.md）を直接アップロード可能
- **Claudeプロジェクトナレッジ**: Markdownファイルは直接サポートされていないため、.txt形式に変換が必要
  - 変換スクリプト: `convert_md_to_txt.py` を使用

---

## 🔄 ファイルの更新

これらのファイルは、`merge_cognite_docs.py`スクリプトを実行することで更新されます。

### 更新手順
1. 各リポジトリを最新化（`git pull`）
2. `merge_cognite_docs.py`を実行
3. 必要に応じて`organize_output_by_category.py`を再実行して整理

---

## 📊 ファイルサイズ情報

すべてのファイルは30MB以内に収まっており、NotebookLMやClaudeプロジェクトナレッジの制限内です。

- **合計サイズ**: 約8.32 MB
- **合計行数**: 約217,403行
- **最大ファイル**: `cdf_combined.md` (3.00 MB)

詳細なファイルサイズ情報は、`analyze_output.py`を実行して確認できます。

---

## 🛠️ 関連スクリプト

- `merge_cognite_docs.py` - ドキュメントを統合して生成
- `organize_output_by_category.py` - ファイルをカテゴリごとに整理
- `analyze_output.py` - ファイルサイズと行数を分析
- `convert_md_to_txt.py` - MarkdownをTXT形式に変換（Claude用）

---

## 📝 注意事項

1. **ファイルの移動**: カテゴリフォルダ内のファイルは、`organize_output_by_category.py`で自動的に整理されます。手動で移動した場合は、次回実行時に再整理される可能性があります。

2. **新しいファイルの追加**: 新しいファイルを追加した場合は、適切なカテゴリに手動で移動するか、`organize_output_by_category.py`のカテゴリ定義を更新してください。

3. **ファイル名の変更**: ファイル名を変更した場合、カテゴリ分類のパターンマッチングが機能しない可能性があります。

---

## 🔗 関連ドキュメント

- [利用ガイド](../利用ガイド.md) - ドキュメント統合の詳細な手順
- [Claudeプロジェクトナレッジ向け最適化ガイド](../Claudeプロジェクトナレッジ向け最適化ガイド.md) - Claude向けの最適化方法

