# 30_Confluence - All
Generated from: Confluence (30_Confluence)

================================================================================

<!-- SOURCE_START: Annotationの更新のFunction化 - takuma.honma - Confluence.md -->
## File: Annotationの更新のFunction化 - takuma.honma - Confluence.md

---
title: "Annotationの更新のFunction化 - takuma.honma - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/~7120200f1273761bae4b57a70a1815ca711eec/blog/2024/03/12/4323377158/Annotation+Function"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Annotationの更新のFunction化

handler.py のなかに関数handle(client, data)として定義

フォルダに入れてzipにして「Cognite Functionsの使用」からアップロード

（下記コードは さん作成 ）

updateA nno.zip

動かす際は以下のように入力を与える  
`{`  
`    "labels": ["Interactive engineering diagram"],`  
`    "revert_to_suggested": true,`  
`    "file_mapping": [`  
`        [3960241829884355, 8628067657422865]`  
`    ]`  
`}`

labels: 図面の詳細情報にあるラベル　コピペするとついてくる()付きの部分は削除する

revert\_to\_sugggested: trueにするとアノテーションを未承認の状態に戻す

file\_mapping: リンク先のファイルの\[旧ID, 新ID\]　リスト内リストになっているので、複数件Updateしたい場合は\[\[旧ID1, 新ID1\], \[旧ID2, 新ID2\], …\]のように与えられる

実行すると以下のようにログが出る

2024-03-12 05:58 Function started

2024-03-12 05:58: {'labels': \['Interactive engineering diagram'\], 'revert\_to\_suggested': True, 'file\_mapping': \[\[3960241829884355, 8628067657422865\]\]}  
2024-03-12 05:58: {3960241829884355: 8628067657422865}  
2024-03-12 05:58: 3  
2024-03-12 05:58: 3  
2024-03-12 05:58: {'fileRef': {'id': 77623620126311}, 'pageNumber': 1, 'text': 'pipe', 'textRegion': {'xMax': 0.08102407080797015, 'xMin': 0.04301709137079939, 'yMax': 0.6036001198833827, 'yMin': 0.5856614679672736}}  
2024-03-12 05:58: {'id': 77623620126311}  
2024-03-12 05:58: {'fileRef': {'id': 8759014033256025}, 'pageNumber': 1, 'text': 'pipe', 'textRegion': {'xMax': 0.9474040213019079, 'xMin': 0.9130067123659581, 'yMax': 0.45815568404142437, 'yMin': 0.4425005551801877}}  
2024-03-12 05:58: {'id': 8759014033256025}  
2024-03-12 05:58: {'fileRef': {'id': 3960241829884355}, 'pageNumber': 1, 'text': 'PH-ME-P-0156-001', 'textRegion': {'xMax': 0.9764524543322245, 'xMin': 0.9204673843039695, 'yMax': 0.13045976636318596, 'yMin': 0.10362090091284451}}  
2024-03-12 05:58: {'id': 3960241829884355}  
2024-03-12 05:58: #updated to:  
2024-03-12 05:58: {'fileRef': {'id': 8628067657422865}, 'pageNumber': 1, 'text': 'PH-25578-P-4110010-001', 'textRegion': {'xMax': 0.9764524543322245, 'xMin': 0.9204673843039695, 'yMax': 0.13045976636318596, 'yMin': 0.10362090091284451}}  
2024-03-12 05:58: ==3960241829884355==  
2024-03-12 05:58: {'id': {  
2024-03-12 05:58: "update": {  
2024-03-12 05:58: "data": {  
2024-03-12 05:58: "set": {  
2024-03-12 05:58: "fileRef": {  
2024-03-12 05:58: =="id": 8628067657422865==  
2024-03-12 05:58: },  
2024-03-12 05:58: "pageNumber": 1,  
2024-03-12 05:58: =="text": "PH-25578-P-4110010-001"==,  
2024-03-12 05:58: "textRegion": {  
2024-03-12 05:58: "xMax": 0.9764524543322245,  
2024-03-12 05:58: "xMin": 0.9204673843039695,  
2024-03-12 05:58: "yMax": 0.13045976636318596,  
2024-03-12 05:58: "yMin": 0.10362090091284451  
2024-03-12 05:58: }  
2024-03-12 05:58: }  
2024-03-12 05:58: }  
2024-03-12 05:58: },  
2024-03-12 05:58: "id": 8606824470786829  
2024-03-12 05:58: }, 'status': {  
2024-03-12 05:58: "update": {  
2024-03-12 05:58: "status": {  
2024-03-12 05:58: "set": "suggested"  
2024-03-12 05:58: }  
2024-03-12 05:58: },  
2024-03-12 05:58: "id": 8606824470786829  
2024-03-12 05:58: }}

2024-03-12 05:58 Function ended

実行前後の変化

実行前

実行後

タグが承認前の状態に戻り、リンク先のFileとリンク名が書き換わっている

<!-- SOURCE_END: Annotationの更新のFunction化 - takuma.honma - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: CDM Today - Cognite Japan - Confluence.md -->
## File: CDM Today - Cognite Japan - Confluence.md

---
title: "CDM Today - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5363433490/CDM+Today"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## CDM Today

使っているうちにわかったCDMのクセ/活用法等の共有

## 2025/7/31 Cognite Data Model Pattern (presented by Magnus)

- Containerにrequires/indexの設定をしておくのが、読み出し効率上重要

Pump requires Assetと設定されていれば、Pumpコンテナにある行はAssetコンテナにも存在する前提でjoinできるが、requiresフラグが設定されていない場合はPumpビューが必要とするすべてのコンテナを取ってきて共通の行を拾う必要があり、処理が重くなる（という仕組みと推測される）。  
なので、Viewのimplementを設定しつつ新規追加したContainerは、必ずrequiresでimplement元のContainerを指定した方が良い。  
途中からでも設定できるが、なるべくContainerの作成時に設定してあるのが望ましい。(後から設定した場合は、投入済みの行に対してrequiresの関係が成り立っているかは保証されない)

[https://docs.cognite.com/ja/cdf/dm/dm\_concepts/dm\_containers\_views\_datamodels/#container-changes](https://docs.cognite.com/ja/cdf/dm/dm_concepts/dm_containers_views_datamodels/#container-changes)

DirectRelationを設定したpropertyには、なるべくb-tree indexを設定する（reverseで辿る処理が軽くなる）。Containerあたり10 propertyまでindexが設定できるので、その範囲内でやりくりする

[https://docs.cognite.com/ja/cdf/dm/dm\_guides/dm\_performance\_considerations](https://docs.cognite.com/ja/cdf/dm/dm_guides/dm_performance_considerations)

- Solution Data ModelではContainerを新規作成せず、Source data Model側ですでに作成されたContainerを参照しに行く形にすることで、データを再投入せずに必要なpropertyだけ読み出せる

## 2025/6/02(予定)

メンバー内での質疑やり取りがあったら、ここに書き足してください〜

・一つのWidget(“アセット“)の中に複数タブ(“path“, “root“等)で表示されるのはどのようなケース？  
→Value Typeが共通のpropertyが複数あるケース。上の例だと、path, rootのValue Typeとして指定されているViewが同じなので、一つのWidget内にまとまる。  
逆にいうと、pump, valveというpropertyを用意してそれぞれに異なるValue Type(“Pump“viewと”Valve”view)を割り当てた場合、それらは別々のWidgetに分かれて表示される。

・SearchのViewの並び順は？  
→おそらくView ExternalIdの文字コード順。A-Za-zのように並ぶ。

・spaceはどの単位で区分するべき？  
→「このデータを見せたくない関係者がいるか」に依存。いる場合は、そのspaceを独立させる必要がある（権限付与対象として切り出してコントロールするため）。  
（例：Eneosの図面系は故障状況の報告書と一緒にしてよいか？）  
基本的には部署(or事業所)×Source/Resourceのマトリックスで考えて、細分化不要なマス同士は結合して一つのspaceにまとめる、といったプロセスで設計すればよいのではないか。

## 2025/5/19

先週受けた質問

・MyTaskに登録したはずなのに、MyWorkとMyTask両方のViewで表示されてしまう。。。

→ MyTaskの定義が実質的にMyWorkの拡張になっていた(MyWorkに必要なプロパティを全て持っていた)ため、同一インスタンスがどちらのViewでも取得できてしまった(Polymorphismの良い例)

▷対処法：プロパティのうち最低１つはContainerを分けておく

今日のトピック：implement先のバージョン指定

結論：propertyのValue Typeをimplement元の指定のViewやバージョンから変える場合は明示が必要

実例

cdf\_idm:CogniteMaintenanceOrder(version=v1)を継承してConstructionを定義した

(追加プロパティだけ定義すればいいかしら…?)

この状態だと、定義し直していない継承元のCogniteAssetやCogniteTimeSeriesがタブに登場

Assetsから飛ぶと、JNCAssetでなく継承元のCogniteAssetのViewで開いてしまう。これだと、開いた先のページからは点検や工事などのビューに飛べない

正しくはこう定義すべきだった(修正後)

継承元からある変数もValue typeを変更

(本当はEquipmentも自身で定義したものにするべき)

修正するとこのような画面になる

工事から飛んだ先のViewが自身の定義したViewになっているので、そこから点検・工事等のViewに回遊できる。

Tips: 現在のdatamodelをdumpする前に、編集対象のViewのプロパティのnameを編集しておく  
（もっと良い方法があるといいな。。。）

<!-- SOURCE_END: CDM Today - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: CDMベースでのプロジェクトに必要な機能が有効か確認 - Cognite Japan - Confluence.md -->
## File: CDMベースでのプロジェクトに必要な機能が有効か確認 - Cognite Japan - Confluence.md

---
title: "CDMベースでのプロジェクトに必要な機能が有効か確認 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/4929257509/CDM"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## CDMベースでのプロジェクトに必要な機能が有効か確認

![CDM.jpg](https://cognitedata.atlassian.net/5091a9d8-30e4-4881-a4be-a89e7845e665#media-blob-url=true&id=2eebdc9b-3a73-4b50-9cf8-afcbb8791a74&collection=contentId-4929257509&contextId=4929257509&width=960&height=540&alt=CDM.jpg)

View Aのプロパティを持つように投入されたinstanceも、View Bに必須のpropertyさえ適切な型で保持していれば、View Bで取得できる  
という意味で、それぞれのviewはDBのテーブルのような、常に値を保持していて更新の対象となるものではない

## 参考リンク

- Kristianのトレーニング資料

[https://docs.google.com/presentation/d/19gJ8yKdYRJHQcVX6K6jyfVnahNtzD1fbRz-wdotGJLM/edit#slide=id.g3155f31a48a\_0\_2338](https://docs.google.com/presentation/d/19gJ8yKdYRJHQcVX6K6jyfVnahNtzD1fbRz-wdotGJLM/edit#slide=id.g3155f31a48a_0_2338)

- よく使うinstance系のメソッド [https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data\_modeling.html#list-instances](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html#list-instances)
- 3D関連のInput

[https://cognitedata.slack.com/archives/C8A0QGMLN/p1732043126965059?thread\_ts=1731451577.548209&cid=C8A0QGMLN](https://cognitedata.slack.com/archives/C8A0QGMLN/p1732043126965059?thread_ts=1731451577.548209&cid=C8A0QGMLN)

[https://cognitedata.slack.com/archives/C8A0QGMLN/p1733166562349109?thread\_ts=1732116094.787149&cid=C8A0QGMLN](https://cognitedata.slack.com/archives/C8A0QGMLN/p1733166562349109?thread_ts=1732116094.787149&cid=C8A0QGMLN)

## サンプルスクリプト

自分で拡張したビューのインスタンスに対して、値を更新する

`from cognite.client.data_classes.data_modeling import ViewId view_list = client.data_modeling.views.list(space="sp_extended_model", limit=10)target_view = ViewId(space="sp_extended_model", external_id=view_list[5].external_id, version=view_list[5].version)# 本間の実行時はview_list[5]がMyFileでしたupdates =[]for k,v in map_from_NodeId_to_new_property_value.items():# ファイルのinstance_idごとに更新予定の値を格納した辞書を用意しておいた    file_meta = client.data_modeling.instances.retrieve_nodes(k, sources=target_view)    new_meta =list(file_meta.properties.values())[0]    new_meta['property_name']= v     view_info =list(file_meta.properties.keys())[0]    file_meta.properties.update({view_info: new_meta})    updates.append(file_meta.as_apply())client.data_modeling.instances.apply(updates)`

特定のファイルについたアノテーションを取得する

`from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteDiagramAnnotation from cognite.client.data_classes.filters import Equals target_node = DirectRelationReference("space", "file_externalId") exist_annotations = client.data_modeling.instances.list(limit=-1, space="space", instance_type=CogniteDiagramAnnotation, filter=Equals(['edge', 'startNode'], target_node))`

## Locationの有効化

[https://unleash-apps.cogniteapp.com/projects/default/features/LOCATION\_FILTERS\_DEV](https://unleash-apps.cogniteapp.com/projects/default/features/LOCATION_FILTERS_DEV) から追加  
1\. Gradual rolloutの右側のペンアイコン(“Edit strategy“)をクリック

1. projectNameの枠の右側のペンアイコン(“Edit constraint“)をクリック
1. Valuesの欄に追加対象のproject名を入力しAdd valuesをクリック。末尾に対象projectが追加されたことを確認し、Doneをクリック
1. 末尾までスクロールし、Save strategyをクリックして変更を適用
2. 対象のprojectにログインし「管理者」ワークスペースでlocationfilters:read/write権限を自分に追加すると、「プロジェクトの設定」からlocationの設定ができるようになる。画面中央のConfigure → Create a locationボタンを順次クリックすると、locationの編集画面が表示される

## Extractor

- PIMS
	- OPC DA
		- CDM対応していない。ToDo: 対応方針を決定 Trello/JNCで相談をした件です。 [https://docs.google.com/presentation/d/19gJ8yKdYRJHQcVX6K6jyfVnahNtzD1fbRz-wdotGJLM/edit#slide=id.g3155f31a48a\_0\_2338](https://docs.google.com/presentation/d/19gJ8yKdYRJHQcVX6K6jyfVnahNtzD1fbRz-wdotGJLM/edit#slide=id.g3155f31a48a_0_2338) によるとProduct ownerに相談する手段もあるかなと思いました。
- File Extractor
	- External id・DatasetはConfigに指定しても無視される
	- Labelをconfigに書くとCDMのtagに自動的に格納してくれる
	- Config で data\_model が新しく必要になる。
	- metadataは、Configとは関係なくCogniteExtractorExtensionsのデータモデルにjsonで格納される。
	- SharePointでの実例
		- Extractor Config（抜粋）
		- `sp2Files` スペースにおけるインスタンスの例。ファイルメタデータはCogniteExtractorFileのextractedDataに入る。CogniteExtractorFileはCogniteFileを拡張している。
		- 要は初期状態としてはこんな感じ
		- extractedDataをフラットにして、カスタムビューのプロパティとして定義したい場合は、Transformationsなどで対応が必要
- DB Extractor
	- CDMでは最初はRAWに展開するので今までと変わらないので検証不要

## Annotation

#### 図面

サンプルコードはこちら⇩(1/28 updated)

Add\_CogniteDiagramAnnotation s.ipynb

留意点：  
後から手動でアノテーションを削除する等の管理UIが、現状ではかなり貧弱なため、このスクリプトで作成したアノテーションは当面instance idを指定してスクリプトから消去するしか無い見込み。

旧UIから管理をできるよう、旧アノテーションの承認statusを反映してCogniteDiagramAnnotationに転記/削除するFunctionを2月中旬頃目処で作成予定。

またアノテーションが作成されても旧UIと異なり、紐づいたCore Asset等の「紐づいたファイル一覧」のところには、アノテーションで紐づいたファイルは追加されない。別途ファイルのDirectRelationに追加すれば表示される。

`from cognite.client.data_classes.data_modeling import DirectRelationReference from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteFileApply res = op_client.data_modeling.instances.apply(     CogniteFileApply(         space=file_sp,         external_id=file_xid,         assets=[DirectRelationReference(asset_sp, asset_xid)]         )     )`

データモデルの拡張により、アノテーションで紐づいたファイル一覧のタブを追加できるかは、これから検証。  
(memo: 以下を応用できるか試す)

ただしannotationはedgeのため常例のnodeとは書き方が異なるor方法がないかも…

発見した怪しい挙動

- DMアノテーションのstatusをRejectedにした場合に、ボックスの表示が消えない（アセットへのリンクは消える）
- Canvasに追加した際、  
	旧ファイルとして選択すると旧アノテーションのみが、  
	DMファイルとして選択するとDMアノテーションのみが表示される  
	（おそらくアノテーションの自動移植等は働いておらず、プレビュー画面のみ両方をオーバーレイしている模様）
- DMアノテーションの手動登録UIが なさそう 貧弱  
	：データ管理>「エンジニアリング図のコンテキスト化」からDiagram parsingをかけた場合には、UIから承認/削除/手動作成ができる。APIで叩けるdiagram parsingのエンドポイントもあるが、ドキュメントは調査中  
	→ 旧アノテーションがApprovedステータスで追加されたら新アノテーションに格納するFunctionを作成見込み

アノテーションボックスの作成

- `"diagrams.AssetLink"`: ボックスの表示(ピンク)およびダイレクトリンクの作成を確認
- `"diagrams.FileLink"`: ボックスの表示(橙)およびダイレクトリンクの作成を確認
- `"images.AssetLink"`:
- `"images.FileLink"`:

アノテーションのretrieve

`from cognite.client.data_classes.data_modeling import EdgeId from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteAnnotation, CogniteDiagramAnnotation client.data_modeling.instances.retrieve_edges(     edges=EdgeId("site_1", "my_annotation"), edge_cls=CogniteDiagramAnnotation )`

アノテーションのリスト

`from cognite.client.data_classes.data_modeling import EdgeId from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteAnnotation, CogniteDiagramAnnotation from cognite.client.data_classes.filters import And, Equals, In, Exists flt = Exists(("cdf_cdm", "CogniteDiagramAnnotation", "startNodePageNumber")) #, [1,2,3]) # ViewDifinitionの画面での表記 InやEqualsの場合は第2引数を取る instance_list = client.data_modeling.instances.list(space="site_1", instance_type="edge", filter=flt) # 特定のファイルについたアノテーションを取得するにはこちら target_node = DirectRelationReference("space", "file_externalId") exist_annotations = client.data_modeling.instances.list(limit=-1, space="space", instance_type=CogniteDiagramAnnotation, filter=Equals(['edge', 'startNode'], target_node))`

アノテーションの削除

`from cognite.client.data_classes.data_modeling import NodeId, EdgeId client.data_modeling.instances.delete(EdgeId("site_1", "my_annotation"))`

statusプロパティで絞り込めない？

diagrams.detectにjobを投げる際はFileReferenceでないといけない  
⇨jobを投げる際は旧File(`client.files.list`)で投げる

`config = DiagramDetectConfig() jobs = [] files = client.files.list(mime_type="application/pdf", limit=-1) for f in files:     detect_job = client.diagrams.detect(         entities=entities_for_test,         file_references=FileReference(file_id=f.id),         configuration=config,         min_tokens=1         )     jobs.append(detect_job)     detect_job.wait_for_completion()`

`jobs[0].result["items"][0]["fileInstanceId"]` に下記のようにファイルのNodeIdが含まれているので、これを基にCogniteFileへのアノテーションを作成する  
`{'externalId': 'test1128_CW.pdf', 'space': 'site_1'}`

#### パノラマ内のcubemap画像

旧中の `"images.InstanceLink"` でCDMの各種instanceにリンクできる  
（はずなので現在確認中）

#### 参考：ダイレクトリンクは問題ない

サンプル：時系列からアセットへのリンク　基本的に、Asset以外のviewからAssetへのリンクを張る

`from cognite.client.data_classes.data_modeling import DirectRelationReference from cognite.client.data_classes.data_modeling.cdm.v1 import CogniteTimeSeriesApply res = op_client.data_modeling.instances.apply(     CogniteTimeSeriesApply(         space=ts_sp,         external_id=ts_xid,         assets=[DirectRelationReference(asset_sp, asset_xid)]         )     )`

## Detailed Diagram Parsing

[https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/4104519778](https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/4104519778)

[Diagram Parsingのアーキテクチャ](https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/4132175945 "https://cognitedata.atlassian.net/wiki/spaces/Contextual/pages/4132175945") と

## 3D

閲覧には、　各Sceneごとにどこかのlocationへ追加する設定が必要  
[https://cognitedata.atlassian.net/wiki/spaces/PD/pages/4587913352](https://cognitedata.atlassian.net/wiki/spaces/PD/pages/4587913352)

[https://cognitedata.atlassian.net/wiki/spaces/3CB/pages/4169794156](https://cognitedata.atlassian.net/wiki/spaces/3CB/pages/4169794156)

Memo: Locationが登録されていれば、作成したSceneをどのLocationに紐付けるかはUIから設定できる  
[https://cognitedata.slack.com/archives/C8A0QGMLN/p1732043126965059?thread\_ts=1731451577.548209&cid=C8A0QGMLN](https://cognitedata.slack.com/archives/C8A0QGMLN/p1732043126965059?thread_ts=1731451577.548209&cid=C8A0QGMLN)  
がJNC-dev環境ではLocationのボックスが表示されない

点群の表示もコンテキスト化も機能としては既にリリースされている（調査中）  
[https://cognitedata.slack.com/archives/C8A0QGMLN/p1733166562349109?thread\_ts=1732116094.787149&cid=C8A0QGMLN](https://cognitedata.slack.com/archives/C8A0QGMLN/p1733166562349109?thread_ts=1732116094.787149&cid=C8A0QGMLN)

ただしコンテキスト化について、旧FDMベースのものと新CDMベースのものの混在は避ける必要がある  
[https://cognitedata.slack.com/archives/C05C9GJ50S2/p1733066355505179?thread\_ts=1732876200.632089&cid=C05C9GJ50S2](https://cognitedata.slack.com/archives/C05C9GJ50S2/p1733066355505179?thread_ts=1732876200.632089&cid=C05C9GJ50S2)

3DのData Modelingサポート状況

[https://cognitedata.atlassian.net/wiki/spaces/PD/pages/5052629038](https://cognitedata.atlassian.net/wiki/spaces/PD/pages/5052629038)

360度画像については、  
・Sceneへのマイグレーションスクリプトは [こちらのスレッド](https://cognitedata.slack.com/archives/C032Z39NVHA/p1739171018414979 "https://cognitedata.slack.com/archives/C032Z39NVHA/p1739171018414979") 参照

・CDMベースのアノテーションを作成するには、以下の手順が必要  
１）cubemap画像をCogniteFileとして投入し、CoreDM内の360Imageのインスタンスに登録  
　（migration用のスクリプトはあり）  
２）アノテーション対象のアセットについて、3dObjectがまだ作成されていない場合には作成する  
(スクリプトあり：取扱注意)  
３）画像内のテキストをdetectして球面座標で出力する新endpoint(要3Dチームに問い合わせ)を利用してアノテーションの情報を作成  
４）アノテーションの手動作成UIをネットワークタブで覗き見して、リクエストの送り方を真似してAnnotationを作成

## Event, Activity

今はEventはMigrationのスコープに入っていない。「Streams and Records (previously ILA)」で対応できるかも？from Kristian

## データモデルの拡張

下記環境の”MyProcessIndustriesExtensions”のデータモデルの定義を自分の新規データモデルのところにコピペしてきて、適当に編集すると矛盾せず作成できた  
**Organization:** cog-quickstart  
**Project:** quickstart  
[Direct link](https://cog-quickstart.fusion.cognite.com/quickstart?cluster=aws-dub-dev.cognitedata.com&workspace=industrial-tools "https://cog-quickstart.fusion.cognite.com/quickstart?cluster=aws-dub-dev.cognitedata.com&workspace=industrial-tools")

定義の一部(拡張元として参照しているviewも, interfaceとしてデータモデル内に記述していないといけない: [関連記事](https://cognitedata.atlassian.net/wiki/spaces/SAFE/pages/4534632467 "https://cognitedata.atlassian.net/wiki/spaces/SAFE/pages/4534632467"))  
追記：上記のように引用しないとデータモデルUIからは正常にPublishできないが、toolkitからは  
上記プロジェクト内の”MyProcessIndustriesSlimExtensions”のように拡張元のviewの記載を省略できる。  
このSlimデータモデルをLocationFilterに設定することで、Searchの表示画面には拡張元のAssetなどの不要なビューを表示しないようにできる。インスタンスの再投入は不要(別のviewを満たすように投入されたinstanceも、データ取得に使うviewに必要なpropertyさえ持っていれば取得用viewで表示できる)

`"Company specific extensions for the CogniteAsset view" """ @name 機能場所 """ type MyAsset implements CogniteAsset & CogniteVisualizable & CogniteDescribable & CogniteSourceable {   "Direct relation to an Object3D instance representing the 3D resource"   object3D: Cognite3DObject   "Name of the instance"   name: String   "Description of the instance"   description: String   "Text based labels for generic use, limited to 1000"   tags: [String]   "Alternative names for the node"   aliases: [String]   "Identifier from the source system"   sourceId: String   "Context of the source id. For systems where the sourceId is globally unique, the sourceContext is expected to not be set."   sourceContext: String   "Direct relation to a source system"   source: CogniteSourceSystem   "When the instance was created in source system (if available)"   sourceCreatedTime: Timestamp   "When the instance was last updated in the source system (if available)"   sourceUpdatedTime: Timestamp   "User identifier from the source system on who created the source data. This identifier is not guaranteed to match the user identifiers in CDF"   sourceCreatedUser: String   "User identifier from the source system on who last updated the source data. This identifier is not guaranteed to match the user identifiers in CDF"   sourceUpdatedUser: String   """   @name Parent   """   parent: MyAsset   """   @name Root   """   root: MyAsset   """   @name Path   """   path: [MyAsset]   """   The last time the path was updated for this asset.   @name Path last updated time   """   pathLastUpdatedTime: Timestamp   """   Specifies the class of the asset. It's a direct relation to CogniteAssetClass.   @name Asset class   """   assetClass: CogniteAssetClass   """   Specifies the type of the asset. It's a direct relation to CogniteAssetType.   @name Asset type   """   type: CogniteAssetType   """   @name Files   """   files: [MyFile]   """   @name Children   """   children: [MyAsset]   """   @name Equipment   """   equipment: [MyEquipment]   """   @name Activities   """   activities: [MyMaintenanceOrder]   """   @name Time series   """   timeSeries: [MyTimeSeries]   """   @name WBS element   """   wbsElement: String   """   @name Cost center   """   costCenter: String   """   @name Owner   """   owner: Owner   """   @name Specifications   """   specs: JSONObject } つづく……`

WBS elementから後ろが拡張部分。

<2025/2/5追記> 注意：jsonのkeyは”.”を含まないよう必要に応じてrenameすること。sync処理中にエラーが起きる例が [報告](https://cognitedata.slack.com/archives/C03G11UNHBJ/p1738715772798109?thread_ts=1738656423.838179&cid=C03G11UNHBJ "https://cognitedata.slack.com/archives/C03G11UNHBJ/p1738715772798109?thread_ts=1738656423.838179&cid=C03G11UNHBJ") されている。

Transformationで `select <他の項目>, to_json(to_metadata(*)) as specs` のようにSpecificationの内容を登録したところ、Searchの画面では以下のように表示された。

Search画面の右上に表示されるフィルターの候補には、jsonの内容として登録したキーは表示されなかった。絞り込みに使うような項目であれば、WBS elementやCost centerのように他のデータ型で独立項目としてモデル定義に含める必要がある。

@nameの部分で、直後のviewやpropertyの表記名の指定ができ、日本語化も可能。@nameの部分の記載がないpropertyはSearchの画面に表示されない。  
また同じ変数名を使っていれば、@nameの部分を変更後データモデルをpublishすることで(データを入れた後でも)表記が変更可能。このとき新バージョンでpublishした場合、ユーザが閲覧するデータモデルはLocationの設定画面で規定されているため、設定を更新するまでユーザには旧バージョンの表記が表示される。

定義したviewについてPythonSDKからデータを書き込むにはMyAssetApplyのようなクラスをimportする必要がある。これには [ドキュメント](https://cognite-pygen.readthedocs-hosted.com/en/latest/cli.html "https://cognite-pygen.readthedocs-hosted.com/en/latest/cli.html") に記載のコマンドを実行する。  
`!pygen generate --top-level-package working_dir --output-dir working_dir/client_name ...`

のようにoptionをつけると、

`from working_dir.client_name.data_classes import ChecklistItemApply`

のようにクラスをimportできる。  
  

#### instanceを入れた後にデータモデルのバージョンを上げると？

実験：MyTimeSeriesにデータポイントをinsertしたのち、プロパティを追加してデータモデルの新バージョンを保存。新しいバージョンのMyTimeSeriesから以前入れておいたデータポイントが見えるか  
→ 同一のinstance\_id(space, external id両方一致)については以前入れたデータは保存される

### 日本語名称の利用

## Search

全角・半角文字は区別される。部分一致の判定ルールは複雑な模様(記号数字混じりのときなど)。  
2/17追記：一般のpropertyでは前方一致・完全一致のみ有効とのこと  
  
以下では検索対象列を確認した。  
プリセットの文字列型の項目のほか、データモデルの拡張で追加された属性や日付も対象になっていた。  
　　　　　　(↑Name, External id, description)　　　　　　(設備重要度↑)　　(↑Path last updated time)

半角のﾀﾝｸはヒットしない

ファイルの場合

ハイフンなし”X1027” → name, external idでヒット

半角小文字を追加 → 半角大文字もヒット

ハイフンあり”X-1027” → aliasesに登録しているが、一致判定のハイライトはされない

<!-- SOURCE_END: CDMベースでのプロジェクトに必要な機能が有効か確認 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Data Modeling周りのツール as of 2025.07.30 - Cognite Japan - Confluence.md -->
## File: Data Modeling周りのツール as of 2025.07.30 - Cognite Japan - Confluence.md

---
title: "Data Modeling周りのツール as of 2025.07.30 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Abstract

- GSSも2025年から本格的にAsset Centric(以下AC)からData Model(以下DM)でのDeliveryに移行
- 伴ってData Model周りのデータ操作やアクセスがACに比べ低下
- しかしNeat・Pygen・Toolkitを使うことでそれなりに作業もしやすくなるため、導入として本勉強会にて紹介

## Agenda

1. [Links](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#1.-Links "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#1.-Links")
2. [Japan Delivery Nowadays](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#2.-Japan-Delivery-Nowadays "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#2.-Japan-Delivery-Nowadays")
3. [Issues on devrivery on Data Model](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#3.-Issues-on-devrivery-on-Data-Model "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#3.-Issues-on-devrivery-on-Data-Model")
4. [Neat](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#4.-Neat "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#4.-Neat")
5. [Pygen](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#5.-Pygen "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#5.-Pygen")
6. [Toolkit](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#6.-Toolkit "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5495095373/Data+Modeling+as+of+2025.07.30#6.-Toolkit")

## 1\. Links

<table><colgroup><col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>category</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><figure></figure></div></th></tr></tbody></table>

<table><colgroup><col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>category</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><figure></figure></div></th></tr><tr><td rowspan="1" colspan="1"><p>Data Modeling</p></td><td rowspan="1" colspan="1"><p>What is Data modeling: <span><span><span><span><a href="https://docs.cognite.com/cdf/dm/dm_concepts/"><span><span>Data modeling concepts - Cognite Docs</span></span></a></span></span></span></span></p><p>Container, View: <span><span><span><span><a href="https://docs.cognite.com/cdf/dm/dm_concepts/dm_containers_views_datamodels"><span><span>Containers, views, polymorphism, data models - Cognite Docs</span></span></a></span></span></span></span></p><p>Instance(Node, Edge): <span><span><span><span><a href="https://docs.cognite.com/cdf/dm/dm_concepts/dm_spaces_instances"><span><span>Spaces, instances, direct relations - Cognite Docs</span></span></a></span></span></span></span></p><p>GraphQL: <span><span><span><span><a href="https://docs.cognite.com/cdf/dm/dm_graphql/dm_graphql_querying/"><span><span>GraphQL queries - Cognite Docs</span></span></a></span></span></span></span></p><p>Python SDK: <span><span><span><span><a href="https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html#"><span><span>Data Modeling — cognite-sdk 7.90.1 documentation</span></span></a></span></span></span></span></p><p>Transformation: <span><span><span><span><a href="https://docs.cognite.com/cdf/integration/guides/transformation/write_sql_queries#cdf_data_models"><span><span>Write SQL queries - Cognite Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C03G11UNHBJ"><span>https://cognitedata.slack.com/archives/C03G11UNHBJ</span></a></span></span></span></span></span></span></p></td></tr><tr><td rowspan="1" colspan="1"><p>Neat</p></td><td rowspan="1" colspan="1"><p>Installation: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/gettingstarted/installation.html">https://cognite-neat.readthedocs-hosted.com/en/latest/gettingstarted/installation.html</a></span></p><p>Intro: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html">https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html</a></span></p><p>Reference: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/reference/NeatSession/base.html">https://cognite-neat.readthedocs-hosted.com/en/latest/reference/NeatSession/base.html</a></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C049R6DG3HR"><span>https://cognitedata.slack.com/archives/C049R6DG3HR</span></a></span></span></span></span></span></span></p></td></tr><tr><td rowspan="1" colspan="1"><p>Pygen</p></td><td rowspan="1" colspan="1"><p>Installation: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/installation.html"><span><span>Installation - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (notebook ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/quickstart/cdf_notebook.html"><span><span>Exploration CDF Notebook - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (sdk gen ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/usage/generation.html"><span><span>Generation of SDK - Pygen Docs</span></span></a></span></span></span></span></p><p>Reference: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/api/api.html"><span><span>Pygen - Pygen Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C050CEHKNB1"><span>https://cognitedata.slack.com/archives/C050CEHKNB1</span></a></span></span></span></span></span></span></p></td></tr><tr><td rowspan="1" colspan="1"><p>Toolkit</p></td><td rowspan="1" colspan="1"><p>Installation &amp; Intro: <span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/setup"><span><span>Setting up - Cognite Docs</span></span></a></span></span></span></span></p><p>Plugins:</p><ul><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/cdf_toml"><span><span>cdf.toml - Cognite Docs</span></span></a></span></span></span></span></p></li><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/plugins/dump_plugin"><span><span>Using the Dump plugin - Cognite Docs</span></span></a></span></span></span></span></p></li><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/alpha_flags"><span><span>Alpha flags - Cognite Docs</span></span></a></span></span></span></span></p></li></ul><p>Reference: <span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library"><span><span>YAML reference library - Cognite Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C05RW13C6TX"><span>https://cognitedata.slack.com/archives/C05RW13C6TX</span></a></span></span></span></span></span></span></p></td></tr></tbody></table>

## 2\. Japan Delivery Nowadays

slide: [Japan Delivery Process](https://docs.google.com/presentation/d/18i07Im03nQRkqNttBtazYuFuGRV8gCffxVzwZTy2dO0/edit?usp=sharing)

spread sheet: [Japan Delivery Process](https://docs.google.com/spreadsheets/d/1qbPaLFaRIToQpMpPQ92FemB0iYH8K0Wqi_KfVBena7w/edit?usp=drive_link)

## 3\. Issues on devrivery on Data Model

(CDMを念頭に…)データモデル内でアセットを中心としたデータ基盤構築をする上での諸課題として・・

- UI上・SDKでの大規模データモデル設計・構築が困難
- データモデル含めUI・SDKのみだとバージョン管理が困難
- API・SDKを用いてのデータモデル関連機能へのアクセスの学習コストが高い

など、慣れて使いこなすまでの学習コストはそれなり高い印象。

[Data Modeling — cognite-sdk 7.90.1 documentation](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html#)

![[36d0be5decc7f64cd91e34ca139b7c4d_MD5.png]]

上記課題は Neat、Pygen、Toolkit である程度の緩和が可能。

[https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html](https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html) [Exploration CDF Notebook - Pygen Docs](https://cognite-pygen.readthedocs-hosted.com/en/latest/quickstart/cdf_notebook.html) [YAML reference library - Cognite Docs](https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library/)

![[2fa035160cb68829338ba72b75f6ab1c_MD5.png]]

## 4\. Neat

#### 概要

- データモデルの設計・拡張を容易にするツール
- Neat自体は非常に使い勝手が良く学習コストも低いツールではあるが、Data Modelやデータ検索ツールのSearchとの相性を考慮しながらの拡張が腕の見せどころ

#### 利用シーン

- (主にCDMを念頭に)データモデルの拡張時がメイン
	- CDMの標準Viewは設定項目が厳密に設定されているため、ユーザー利用システム固有のプロパティを格納するには拡張が必要 例)設計圧力、製造メーカーなど
- CDMをExcelにダンプし、フォーマットに沿って拡張したいプロパティを設定し、新規DMとしてデプロイするというのが一般的
- (一方で本番反映時は開発で固めたDMをToolkitにダンプし、Toolkit経由で本番反映というのが理想)

#### 困ったときは・・・

さん さん さん がかなり格闘してくれていたため、ナレッジが豊富。

[CDMベースでのプロジェクトに必要な機能が有効か確認](https://cognitedata.atlassian.net/wiki/x/JYDOJQE)

[CDM Today](https://cognitedata.atlassian.net/wiki/x/EoCvPwE)

その他リンク

<table><colgroup><col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Neat</p></td><td rowspan="1" colspan="1"><p>Installation: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/gettingstarted/installation.html">https://cognite-neat.readthedocs-hosted.com/en/latest/gettingstarted/installation.html</a></span></p><p>Intro: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html">https://cognite-neat.readthedocs-hosted.com/en/latest/tutorials/introduction/introduction.html</a></span></p><p>Reference: <span><a href="https://cognite-neat.readthedocs-hosted.com/en/latest/reference/NeatSession/base.html">https://cognite-neat.readthedocs-hosted.com/en/latest/reference/NeatSession/base.html</a></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C049R6DG3HR"><span>https://cognitedata.slack.com/archives/C049R6DG3HR</span></a></span></span></span></span></span></span></p></td></tr></tbody></table>

## 5\. Pygen

#### 概要

- データモデルのラッパーSDKを生成するツール
- client.data\_modeling.instances…とせずとも pygen\_client.extendet\_asset.list()とか直感的な構文でアクセスができる
- データの書き込みにはNotebookインストールではなくSDK生成によるインストールが必要なため利用シーン毎に適宜使い分けることをおすすめ
- 学習コストも低い

#### 利用シーン

- 特にノードのデータ参照・更新で威力発揮、AssetづくりやFileのメタデータ作成、複雑な紐づけロジックの適用、リレーションの作成など、複雑になればなるほどPygenを使ったほうが楽なはず
- 一方でバージョンごと、データモデルごとにライブラリ生成が必要なため、その点は要注意

#### 困ったときは・・・

さん さん あたりはそれなりに経験があるはず

<table><colgroup><col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Installation: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/installation.html"><span><span>Installation - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (notebook ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/quickstart/cdf_notebook.html"><span><span>Exploration CDF Notebook - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (sdk gen ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/usage/generation.html"><span><span>Generation of SDK - Pygen Docs</span></span></a></span></span></span></span></p><p>Reference: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/api/api.html"><span><span>Pygen - Pygen Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C050CEHKNB1"><span>https://cognitedata.slack.com/archives/C050CEHKNB1</span></a></span></span></span></span></span></span></p></td></tr></tbody></table>

## 6\. Toolkit

#### 概要

- CDFプロジェクトの設定情報をダンプし、Gitと組み合わせてバージョン管理ができるCLIツール、かつCDFへのデプロイも可能
- CDFに特化したCI/CDツールと言って差し支え無し
- 昨今dumpできる対象も非常に充実してきており、実用度も非常に高まっている
- 自由度が高くかつ作法もある程度クセがあり慣れるまでは難しい、その意味ではそれなりに学習コストあり(中程度？)

#### 利用シーン

- 開発環境での開発・本番への反映というのが主な思想、例えば…
	- Transformationは変更履歴が基本残らないが・・・ある程度動くようになったらdumpしておき、更新版を本番へデプロイ
	- Streamlitも同様、バージョン管理が難しいかつUIでの開発していると自動保存がきかなくて消えちゃったり・・・なので手元のリポジトリ+Streamlit libで開発し、本番にデプロイ、動作確認とできると良い
	- Functionは言わずもがな
	- Data Modelingは上述の通りNeatで設計を固めたものをdumpして本番反映
	- Atlas Agentsもdump可能、これが地味に大変なので是非使ってみてほしい
	- Locationも同様
- 他にも別プロジェクトで設定した内容をベースに他プロジェクトで使いまわすことも想定されている、例えば…
	- アクセスグループなどは(特に開発初期段階は)概ね同じはず、開発用グループにほとんど全ての権限をUIから一個一個付与するのは大変、Tookitで開始時に一気に作ってしまえるといくらかストレスも減る
	- 近い内にDeliveryでよく使うGlobal共通の標準物(P&IDにアノテーションをつけるためのパイプライン、ソースシステム固有のデータモデル、業界標準のデータモデルなど)がパッケージされたものがインストールできるようになる
	- Globalとは切り離してJapan用の標準ツール(ダッシュボード用のアノテーションされたアセットのダイレクトリンクを作るなどなど)を管理しても良いかも

#### 困ったときは・・・

さん さん はそれなりに触っている印象

<table><colgroup><col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Toolkit</p></td><td rowspan="1" colspan="1"><p>Installation &amp; Intro: <span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/setup"><span><span>Setting up - Cognite Docs</span></span></a></span></span></span></span></p><p>Plugins:</p><ul><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/cdf_toml"><span><span>cdf.toml - Cognite Docs</span></span></a></span></span></span></span></p></li><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/guides/plugins/dump_plugin"><span><span>Using the Dump plugin - Cognite Docs</span></span></a></span></span></span></span></p></li><li><p><span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/api/alpha_flags"><span><span>Alpha flags - Cognite Docs</span></span></a></span></span></span></span></p></li></ul><p>Reference: <span><span><span><span><a href="https://docs.cognite.com/cdf/deploy/cdf_toolkit/references/resource_library"><span><span>YAML reference library - Cognite Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C05RW13C6TX"><span>https://cognitedata.slack.com/archives/C05RW13C6TX</span></a></span></span></span></span></span></span></p></td></tr></tbody></table>

<!-- SOURCE_END: Data Modeling周りのツール as of 2025.07.30 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Diagram parsing for data modeling（20256時点) - Cognite Japan - Confluence.md -->
## File: Diagram parsing for data modeling（20256時点) - Cognite Japan - Confluence.md

---
title: "Diagram parsing for data modeling（2025/6時点) - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5410553883/Diagram+parsing+for+data+modeling+2025+6"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
データモデル用Diagram parsingを触ってみたので、使い方や注意点などをざっくばらんに書いていきます。

\*Beta版かつ理解足りていない可能性があるので、 **ドキュメントを優先して参照してください。**

## ドキュメント

- CDFドキュメント:[Diagram parsing for data modeling - Cognite Docs](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing)
- APIドキュメント:

## できること

- データモデルにおけるPDFの① **アセットのマッピング** と② **シンボル同士のConnection登録**

## 事前準備

[Diagram parsing for data modeling - Cognite Docs](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing#before-you-start)

- 解析用ファイルがデータモデル内にあること。
	- CDFの権限など設定

## ライブラリ登録からparsingまでの大まかな手順

1. ライブラリにシンボルを登録： [https://docs.cognite.com/cdf/integration/guides/contextualization/diagram\_parsing#manage-symbol-libraries](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing#manage-symbol-libraries)
	1. デフォルトのライブラリがあるので、コピーして使うこともできる（画面右上）
	2. 新しいライブラリを作成（コピー）し、図面を開きシンボルの登録を行う。ここで多くの種類のシンボル登録を行うと、parsingされやすくなる。（色々なシンボルかつ、同じシンボルでも内部で微妙に異なるので同じに見えるシンボルでも複数個登録しておくと良い）
2. parsingの実行： [https://docs.cognite.com/cdf/integration/guides/contextualization/diagram\_parsing#run-diagram-parsing](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing#run-diagram-parsing)
	1. ファイルとライブラリを指定し、run parsingを押す
3. 結果の表示： [https://docs.cognite.com/cdf/integration/guides/contextualization/diagram\_parsing#view-the-parsed-output](https://docs.cognite.com/cdf/integration/guides/contextualization/diagram_parsing#view-the-parsed-output)
	1. Symbolタブではライブラリに登録したシンボルがそれぞれ何個検出されたかを確認・修正できる
		1. 検出の漏れているアセットやパイプも登録しておくこと
	2. MappingタブではPDF上でマッピングされたアセットの確認・修正ができる
	3. Connectionタブでは隣接しているシンボル（パイプやアセット）との繋がりの確認・修正ができる。Symbolが検出されていないと繋がりのparsing/修正ができないので注意。

## 注意点・コメント

- アイソレーション支援を目的とした観点
	- **2025年6月上旬現在では、登録したコネクションをCDF上で使うことはできないとのこと**
		- 技術的にはAPIでデータを取得＆加工することで隣接した設備を取得することは可能とのこと（クリスチャン談）
		- parsing結果は以下のAPIを使用することで取得できる：
			- 例
				- **List diagrams** でdiagram parsingを実行したpdf一覧を取得
				- pdfのexternalIdで絞り、diagramのexternalId(diagramId)を取得
				- **Get an extended diagram by external id** を使用し、diagramIdで図面を指定することで対象のentity一覧を取得
- ライブラリで登録したシンボルは全て検出される想定。  
	だが現状は同じように見えるシンボルでも内部では微妙に差があり、同じシンボルとして検出されないことがあるため、現状手作業での修正が多い。
- PDFファイルはベクトル化されているファイルが主な対象。図面をPDF化したものだとOCRで図面とアセットのマッピングが行われ、シンボル認識は行われない。

<!-- SOURCE_END: Diagram parsing for data modeling（20256時点) - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: GitHub Copilot - Update for new gpt-4o-mini completions model and additional Copilot chat models - Security - Confluence.md -->
## File: GitHub Copilot - Update for new gpt-4o-mini completions model and additional Copilot chat models - Security - Confluence.md

---
title: "GitHub Copilot - Update for new gpt-4o-mini completions model and additional Copilot chat models - Security - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/SEC/pages/5266014211/GitHub+Copilot+-+Update+for+new+gpt-4o-mini+completions+model+and+additional+Copilot+chat+models"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
<table><colgroup><col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Owner</p></td><td rowspan="1" colspan="1"><p>, original version <a href="https://cognitedata.atlassian.net/wiki/spaces/SEC/pages/3979509765">here</a> from</p></td></tr><tr><td rowspan="1" colspan="1"><p>Team</p></td><td rowspan="1" colspan="1"><p>Atlas AI</p></td></tr><tr><td rowspan="1" colspan="1"><p>Status</p></td><td rowspan="1" colspan="1"><p><span><span><span><span>In Progress</span></span></span></span></p></td></tr></tbody></table>

Additions from the previous version added in green highlight

## Description

GitHub Copilot is a GitHub service that offers autocomplete-style code suggestions OpenAI Codex, an AI system created by OpenAI. It is a descendant of OpenAI’s GPT model, fine-tuned for use in programming applications. It parses natural language, code and code comments, and generates code in response.

To use GitHub Copilot, a developer installs an IDE extension/plug-in. As code is written in the IDE, GitHub Copilot analyzes the context in the file being edited, as well as related files, and offers suggestions from within the text editor.

GitHub Copilot is trained on all languages that appear in public repositories. For each language, the quality of suggestions you receive may depend on the volume and diversity of training data for that language.

Recently there is a [a new completion model for code completions available in GitHub Copilot](https://github.blog/changelog/2025-02-18-new-gpt-4o-copilot-code-completion-model-now-available-in-public-preview-for-copilot-in-vs-code/ "https://github.blog/changelog/2025-02-18-new-gpt-4o-copilot-code-completion-model-now-available-in-public-preview-for-copilot-in-vs-code/"): `GPT-4o Copilot`. It is based It is based on OpenAI's `gpt-4o-mini` model as opposed to the quite outdated OpenAI Vertex model that is based on `gpt-3.5-turbo`. This new model is much more advanced and will provide better code completions. If we want to benefit from the latest AI-powered coding productivity enhancements, we should make use of the latest model families.

To be able to use it, an administrator would need to enable this model for our organization by opting in to `Editor preview features` in the [Copilot policy settings](https://docs.github.com/en/enterprise-cloud@latest/copilot/managing-copilot/managing-github-copilot-in-your-organization/managing-policies-for-copilot-in-your-organization#enabling-copilot-features-in-your-organization "https://docs.github.com/en/enterprise-cloud@latest/copilot/managing-copilot/managing-github-copilot-in-your-organization/managing-policies-for-copilot-in-your-organization#enabling-copilot-features-in-your-organization") on GitHub (currently disabled).

Additionally, models from other providers are now available that could be enabled as well for the chat feature (not completions). In a lot of cases, those might provide better performance than the gpt-4o-models

When serving those models, GitHub might use infrastructure from AWS Bedrock or GCP. However, “input prompts and output completions continue to run through GitHub Copilot's content filters for public code matching, when applied, along with those for harmful, offensive, or off-topic content.”, see here [Learn more about how GitHub Copilot serves Claude 3.5 Sonnet.](https://docs.github.com/copilot/using-github-copilot/using-claude-sonnet-in-github-copilot "https://docs.github.com/copilot/using-github-copilot/using-claude-sonnet-in-github-copilot") and [Learn more about the public preview of Gemini 2.0 Flash.](https://docs.github.com/copilot/using-github-copilot/ai-models/using-gemini-flash-in-github-copilot "https://docs.github.com/copilot/using-github-copilot/ai-models/using-gemini-flash-in-github-copilot")  
  

From a developer productivity point of view, the recommendation would be to enable the new models as soon as possible and be early adopters of AI driven productivity improvements.

## Valuable assets

- Cognite IP — source code.
- Customer data — exceptionally, and in limited contexts, developers will have customer data in their machines for testing purposes.
- Secrets — developers have secrets such as GCP and Azure credentials, GitHub tokens, and other types of authenticators used in testing of their code.

## Threat model

<table><colgroup><col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>Risk/Impact</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>Mitigation/Control</strong></p><figure></figure></div></th></tr></tbody></table>

<table><colgroup><col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>Risk/Impact</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>Mitigation/Control</strong></p><figure></figure></div></th></tr><tr><td rowspan="1" colspan="1"><p>Developer uses and commits low quality code suggested by Copilot, because “it works”.</p></td><td rowspan="1" colspan="1"><ul><li><p>Cognite change management flow includes peer review of code changes. Peer review is one of the main quality control gates we have and it will continue to be required for code changes to be accepted and deployed. Change authors and reviewers must understand the code, how it works and its implications, and assess its overall quality, including readability, maintainability, robustness, and security.</p></li><li><p>Cognite must provide guidance and education on how to use code suggested by AI.</p></li></ul></td></tr><tr><td rowspan="1" colspan="1"><p>Copilot suggests insecure code.</p></td><td rowspan="1" colspan="1"><ul><li><p>Pull Requests with peer review and approval.</p></li><li><p>Code scanning with CodeQL and other language-specific tools, e.g. Clippy for Rust and the.NET Analyzers for C#.</p></li><li><p>GitHub made <a href="https://github.blog/2023-02-14-github-copilot-now-has-a-better-ai-model-and-new-capabilities/#filtering-out-security-vulnerabilities-with-a-new-ai-system">improvements to Copilot to block basic insecure code patterns</a>.</p></li></ul></td></tr><tr><td rowspan="1" colspan="1"><p>Copilot suggests use of malicious libraries.</p></td><td rowspan="1" colspan="1"><ul><li><p>Introducing a new library is a change that must go through PR review and approval.</p></li><li><p><a href="https://docs.github.com/en/code-security/dependabot/dependabot-alerts/about-dependabot-alerts">Dependabot generates alerts when it detects malware libraries</a> (currently in beta) in a repository. Dependabot alerts must be enabled in all repositories and teams must process and act on alerts.</p></li><li><p>New libraries, including those suggested by Copilot, need to be assessed by developers on their trustworthiness and utility. Cognite must provide more guidance on how to assess new dependencies, both for code authors and reviewers.</p></li></ul></td></tr><tr><td rowspan="1" colspan="1"><p>Copilot suggests licensed code that prohibits or imposes conditions on its inclusion in other code bases.</p></td><td rowspan="1" colspan="1"><ul><li><p>Copilot for Business has policies that can be enabled to filter out code suggestions that match public code on GitHub.</p></li><li><p><span>Those filters still apply for the new </span><code>gpt-4o-mini</code> <span>model or the additional models such as </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-gemini-flash-in-github-copilot"><span>Gemini 2.0 Flash</span></a> <span>or </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-claude-sonnet-in-github-copilot"><span>Claude 3.5</span></a><span>.</span></p></li></ul></td></tr><tr><td rowspan="1" colspan="1"><p>Copilot suggests code that is intellectual property of others, putting Cognite at risk of infringement.</p></td><td rowspan="1" colspan="1"><ul><li><p>Copilot for Business has policies that can be enabled to filter out code suggestions that match public code on GitHub.</p></li><li><p><span>Those filters still apply for the new </span><code>gpt-4o-mini</code> <span>model or the additional models such as </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-gemini-flash-in-github-copilot"><span>Gemini 2.0 Flash</span></a> <span>or </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-claude-sonnet-in-github-copilot"><span>Claude 3.5</span></a><span>.</span></p></li></ul></td></tr><tr><td rowspan="1" colspan="1"><p>Copilot trains its model on Cognite’s code, making it possible that Copilot will suggest Cognite IP to other Copilot users.</p></td><td rowspan="1" colspan="1"><ul><li><p>Copilot for Business <a href="https://docs.github.com/en/site-policy/privacy-policies/github-copilot-for-business-privacy-statement#code-snippets-data">Privacy Statement</a> states that “Copilot for Business does not retain any Code Snippets Data” and such “data is only transmitted in real-time to return Suggestions, and is discarded once a Suggestion is returned”.</p></li><li><p><span>This statement is still true for the new models </span><code>gpt-4o-mini</code> <span>as well as the additional models such as </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-gemini-flash-in-github-copilot"><span>Gemini 2.0 Flash</span></a> <span>or </span><a href="https://docs.github.com/en/copilot/using-github-copilot/ai-models/using-claude-sonnet-in-github-copilot"><span>Claude 3.5</span></a><span>.</span></p></li></ul></td></tr></tbody></table>

## Conclusion

Observations:

- Copilot for Business offers more privacy guarantees and more mitigations for IP infringement risk. As Copilot for Individuals lacks those mitigations and guarantees, Copilot for Individuals should not be used. Instead, all developers should be offered access to Copilot for Business.
- Cognite should provide guidance to developers on how to incorporate Copilot suggestions, considering:
	- code quality and maintainability aspects
	- introduction of new libraries

Pending: Conclusion on risks of the `gpt-4o-mini` completion model build into the preview version of GitHub Copilot

Pending: Conclusion on risks for the additional models for Copilot chat

In Progress

<!-- SOURCE_END: GitHub Copilot - Update for new gpt-4o-mini completions model and additional Copilot chat models - Security - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Naming Convention and Toolkit Structure - Cognite Japan - Confluence.md -->
## File: Naming Convention and Toolkit Structure - Cognite Japan - Confluence.md

---
title: "Naming Convention and Toolkit Structure - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Abstract

- TokuyamaのプロジェクトのCase Study＆Discussion

## Agenda

- [背景: Tokuyamaプロジェクトを始めるにあたって](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#%E8%83%8C%E6%99%AF%3A-Tokuyama%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E5%A7%8B%E3%82%81%E3%82%8B%E3%81%AB%E3%81%82%E3%81%9F%E3%81%A3%E3%81%A6 "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#%E8%83%8C%E6%99%AF%3A-Tokuyama%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E3%82%92%E5%A7%8B%E3%82%81%E3%82%8B%E3%81%AB%E3%81%82%E3%81%9F%E3%81%A3%E3%81%A6")
- [Naming Convention(Rule): Case Tokuyama](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#Naming-Convention\(Rule\)%3A-Case-Tokuyama "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#Naming-Convention(Rule)%3A-Case-Tokuyama")
- [Toolkitの構造: Case Tokuyama](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#Toolkit%E3%81%AE%E6%A7%8B%E9%80%A0%3A-Case-Tokuyama "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5744459803/Naming+Convention+and+Toolkit+Structure#Toolkit%E3%81%AE%E6%A7%8B%E9%80%A0%3A-Case-Tokuyama")

## 背景: Tokuyamaプロジェクトを始めるにあたって

### なぜNaming Ruleを整理しようとしたか

- 自身にとって初(C)DMプロジェクトだったこともあり、アクセス権やプロジェクトの構造等見直しが必要
- Kristian、Chrstian、Magnus等OsloのDeliveryメンバーからDMプロジェクトではNaming Conventionを作っているという話も
- 上記2点からやるなら根本的なところから、という気持ちでNaming Conventionに着手

### なぜToolkitを使い始めたか

- Cosmo等では開発環境 - 本番環境の同期が取りづらい(本番先行)という課題
	- そもそもプロジェクト開始時は本番環境のみで「開発環境」の提供が一般的ではなかった
	- 途中から本番同等の開発環境を再構築するのはそれなりに工数がかかるため困難
- 上記をきれいに解消できるのがToolkit > Devの設定/開発物をCleanにProdにデプロイできる
- また散らばりがちな開発物(主にnotebook)も同じRepoで共有できるだろいうという狙いも

今振り返るとNaming Conventionの整理をするとToolkitがとても使いやすくなる

## Naming Convention(Rule): Case Tokuyama

#### Links

- Best Practice: [https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734](https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734 "https://hub.cognite.com/best-practices-internal-461/best-practices-naming-conventions-5734")
- Celanese: [https://docs.google.com/spreadsheets/d/1jesrTDQJ1m1fyf3t2AuuMfKT1YH3kmBG/edit?usp=sharing&ouid=104733110143945950217&rtpof=true&sd=true](https://docs.google.com/spreadsheets/d/1jesrTDQJ1m1fyf3t2AuuMfKT1YH3kmBG/edit?usp=sharing&ouid=104733110143945950217&rtpof=true&sd=true)
- Tokuyama: [https://docs.google.com/spreadsheets/d/1GiuLgqLWXdN9xcqJfbbjkyGjpR0eTZvuG\_GBcGLnGc8/edit?usp=sharing](https://docs.google.com/spreadsheets/d/1GiuLgqLWXdN9xcqJfbbjkyGjpR0eTZvuG_GBcGLnGc8/edit?usp=sharing)

#### 概要

- Celaneseや他プロジェクトのNaming Conventionを参考にしつつ、フォーマットはやや変更
- CelaneseとBest Practiceでは少し体型が違う様子 > TokuyamaはCelanese寄りに簡素化

#### 考え方

- 基本的には ソースシステム/データ × 部署 × 補足情報 という考え方
- Spaceを例に・・・{SystemCode|CogAppCode}-{siteCode}-{UnitCode}-{SpaceTypeCode} という体型で管理
	- SystemCode: ソースシステムのコード 例)EQL, PI, SP
	- CogAppCode: Cogniteのアプリや機能コード 例)ANN, INF
	- siteCode: 拠点コード 例)TOK, KSM
	- UnitCode: 部門コード 例) CEMENT, CHEM1

#### 要改善点

- スペース末尾に付与するITR, IRF, DDM等がややわかりにくい > ITRN, IREF, DDOM等4文字でも良かったかも
- 共通系コードがわかりにくい SSA > Source System All, STA > Site Allなど、これらをつなげると SSA-STA-DPA-DDM みたいになる。(例はDomain Data Model用のスペース)

## Toolkitの構造: Case Tokuyama

#### Links

- Tokuyama: [https://github.com/cognitedata/tokuyama/tree/dev/toolkit/modules](https://github.com/cognitedata/tokuyama/tree/dev/toolkit/modules)
- Quick Start modules by Darren: [https://github.com/cognitedata/quickstart-module-foundation/tree/main](https://github.com/cognitedata/quickstart-module-foundation/tree/main)

#### 概要

- ソースシステムごとに分類、開発物をワンストップで管理できるリポジトリとしても意識して作成
- ユーザーに渡して運用で用いてもらうことまでは意図した作りには出来てない

#### 考え方

- フォルダ構造
	- トップ: 0.全体共通 1.ソースシステム 2.アプリ/UC 3.その他
	- 2階層目: 基本的には部署単位
	- 3階層目: 各リソース(transformation、functionなど) + 「data」「python\_scripts」
		- data、python\_scriptsは主に開発(function化前のモジュール開発や簡単なスクリプトの開発)時に利用
			- python\_scriptsは必然的に ipynb が集まってくる
		- data、python\_scripts はtoolkitビルド&デプロイ時に無視される
		- dataは要.gitignore /.cursorignore
- Deploy
	- dev→mainブランチマージによる本番Deployの仕組みを構築中(仕掛中)
		- 注意点としてはmainマージでdeployとなると、deploy --dry-runで違和感がある変化を見つけたときにdeployを止めづらい
		- そのためdev→mainマージは deploy --dry-run --verbose だけ実行される用に設計中、本deployのactionは別途設計中

<!-- SOURCE_END: Naming Convention and Toolkit Structure - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: P&IDのContext化 Diagram Detectのパラメーター - Cognite Japan - Confluence.md -->
## File: P&IDのContext化 Diagram Detectのパラメーター - Cognite Japan - Confluence.md

---
title: "P&IDのContext化: Diagram Detectのパラメーター - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5340037121/P+ID+Context+Diagram+Detect"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## 参照ドキュメント

[Contextualization — cognite-sdk 7.90.1 documentation](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/contextualization.html#cognite.client._api.diagrams.DiagramsAPI.detect)

## 図面Context化の流れ

### 25年5月時点での図面Context化の課題

- 現時点でCore Data Model内でContext化作業を完結することができない

### 図面Context化の流れ

[Context化の流れ](https://docs.google.com/presentation/d/1719N97G6hduWvVPD272JFPvL17YBCsO1BBQ8YH20YQs/edit?usp=sharing)

![image-20250508-072112.png](https://cognitedata.atlassian.net/ac24d2fa-9006-4b8e-a25c-4e857533be25#media-blob-url=true&id=5f603107-f1c0-4d72-9db6-1e42b5f0dd97&collection=contentId-5340037121&contextId=5340037121&width=1308&height=736&alt=image-20250508-072112.png)

紐づけの精度を上げるうえでは特にDiagramDetectのパラメーター設定が重要…

## 各種パラメーターの説明

<table><colgroup><col> <col> <col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>パラメーター名</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>説明</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設定値サンプル</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><figure></figure></div></th></tr></tbody></table>

<table><colgroup><col> <col> <col> <col></colgroup><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>パラメーター名</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>説明</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設定値サンプル</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><figure></figure></div></th></tr><tr><td rowspan="1" colspan="1"><p><strong>file_ids</strong></p><p><strong>file_external_ids</strong></p><p><strong>file_instance_ids</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"><p><strong>file_references</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"><p><strong>entities</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"><p><strong>partial_match</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"><p><strong>min_tokens</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"><p><strong>pattern_mode</strong></p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr><tr><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td></tr></tbody></table>

<!-- SOURCE_END: P&IDのContext化 Diagram Detectのパラメーター - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Pygen入門 - Cognite Japan - Confluence.md -->
## File: Pygen入門 - Cognite Japan - Confluence.md

---
title: "Pygen入門 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Pygen入門

## Abstract

- data modelingプロジェクトでのDeliveryで必須のAPIによるアクセスは構文が複雑、それを解消するツールとしてPygenが登場
- 基本的な利用方法を記載し、Data Modelへのアクセスのハードルを下げ、Deliveryの効率化を目指す

## Agenda

1. [Links](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5655003330#Links "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5655003330#Links")
2. [Python SDKでData Modelingを利用する上でのちょっとした課題](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Data-Modeling%E3%82%92%E5%88%A9%E7%94%A8%E3%81%99%E3%82%8B%E4%B8%8A%E3%81%A7%E3%81%AE%E3%81%A1%E3%82%87%E3%81%A3%E3%81%A8%E3%81%97%E3%81%9F%E8%AA%B2%E9%A1%8C "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Data-Modeling%E3%82%92%E5%88%A9%E7%94%A8%E3%81%99%E3%82%8B%E4%B8%8A%E3%81%A7%E3%81%AE%E3%81%A1%E3%82%87%E3%81%A3%E3%81%A8%E3%81%97%E3%81%9F%E8%AA%B2%E9%A1%8C")
3. [Pygenのinstall方法](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Pygen%E3%81%AEinstall%E6%96%B9%E6%B3%95 "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Pygen%E3%81%AEinstall%E6%96%B9%E6%B3%95")
4. [Pygenのコードサンプル](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Pygen%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%89%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5655003330/Pygen#Pygen%E3%81%AE%E3%82%B3%E3%83%BC%E3%83%89%E3%82%B5%E3%83%B3%E3%83%97%E3%83%AB")

## Links

<table><colgroup><col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>Installation: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/installation.html"><span><span>Installation - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (notebook ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/quickstart/cdf_notebook.html"><span><span>Exploration CDF Notebook - Pygen Docs</span></span></a></span></span></span></span></p><p>Intro (sdk gen ver): <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/usage/generation.html"><span><span>Generation of SDK - Pygen Docs</span></span></a></span></span></span></span></p><p>Reference: <span><span><span><span><a href="https://cognite-pygen.readthedocs-hosted.com/en/latest/api/api.html"><span><span>Pygen - Pygen Docs</span></span></a></span></span></span></span></p><p>Slack: <span><span><span><span><span><span><a href="https://cognitedata.slack.com/archives/C050CEHKNB1"><span>https://cognitedata.slack.com/archives/C050CEHKNB1</span></a></span></span></span></span></span></span></p></td></tr></tbody></table>

## Data Modelingを利用する上でのちょっとした課題

データモデルにPythonでアクセスをする際、下記のようなコードを書いているはず。参考: [SDKページ](https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html#list-instances "https://cognite-sdk-python.readthedocs-hosted.com/en/latest/data_modeling.html#list-instances")

`client.data_modeling.instances.list(    space='EQL-TOK-CEMENT-IRF',    instance_type="node",    sources=[        ViewId(space="SSA-TJP-DPA-DMD", external_id="TokuyamaAsset", version="v1")# propertyを参照したい場合、sourcesにて適用するviewの情報を指定する(この場合はCogniteAssetとしてのpropertyを見たい)],# filter={#         "containsAny": {#             "property": ["cdf_cdm", "CogniteAsset/v1", "tags"],#             "values": ['第7階層']#         }#     },    limit=-1)`

慣れてしまうとなんてことはないが、個人的には **パラメーターが多い** のと **データモデルの構造をある程度理解していないと使いづらい** のが難点。(ちょっとデータを確認したいみたいな時は特に。)

一方PygenはData Modelを指定し、そのModel内のViewに対するアクセッサーをライブラリとしてInstallできるため、直感的なアクセスが可能。上の例だと1行で済みます。

`pclient.tokuyama_asset.instances.list(limit=-1)`

## Pygenのinstall方法

本体のInstallは下記の通り。

`pip install cognite-pygen`

## Pygenのコードサンプル

## 1\. SDKの生成

Genericライブラリ生成のためのインストールは下記の通り。例えば・・・

cdmの例:

`from cognite.pygen import generate_sdk_notebook pclient = generate_sdk_notebook(    model_id =("cdf_cdm","CogniteCore","v1"),    client = client,    top_level_package ="cdm")# from cdm.data_classes import CogniteFileWrite, CogniteAssetWrite....`

カスタムのData Modelの例:

`from cognite.pygen import generate_sdk_notebook pclient = generate_sdk_notebook(    model_id =("SSA-TJP-DPA-DMD","TokuyamaCore","v1"),    client = client,    top_level_package ="tokuyama")# from tokuyama.data_classes import TokuyamaAssetWrite, CogniteFileWrite, CogniteAssetWrite...`

## 2.データへのアクセス

以下cdmを例に記載。

データの参照・検索など。Viewが持っている項目に対してデフォルトのフィルターあり。

`## stringは<column>_prefixが、数値やdatetimeだとrange等も使えるpclient.cognite_asset.list(space="XX", limit=-1, external_id_prefix="XX")pclient.cognite_asset.search(space="XX", name="XXX")`

データのUpsert/Delete

`from cdm.data_classes import CogniteAssetWrite from cognite.client.dataclass.data_modeing import NodeId pclient.upsert(CogniteAssetWrite(space="XX", external_id="XX", name="XX",...))pclient.delete(NodeId(space="XX", external_id="XX"))`

<!-- SOURCE_END: Pygen入門 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: SpaceとLocationの設計 - Cognite Japan - Confluence.md -->
## File: SpaceとLocationの設計 - Cognite Japan - Confluence.md

---
title: "SpaceとLocationの設計 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5077204993/Space+Location"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## SpaceとLocationの設計

※注意：以下の記事はCDFのLocationについてであり、InFieldおよびInRobotそれぞれのLocationは別物であることに留意。  
※注意2：spaceへのアクセス権限自体は、datamodelinstances権限のscopeで指定される。  
  

Locationは階層構造をとることができ、各Locationごとに参照するspaceやデータモデルを独立に指定できる。例えば、子階層にsample\_spaceへの参照を設定していても、親階層に設定していなければ、親階層のLocationからはsample\_space内のインスタンスはUI上で見ることができない。（Search, DiagramParser）

こうした自由度があるため、  
Location同士のデータ参照範囲は重ならずに、包含関係(親子)または分離(同階層)になるように、管理者側で整理することが望ましい、とのこと。

社内参考記事： [Best practice: Location configuration and consumption](https://cognitedata.atlassian.net/wiki/x/iAB2EQE)

So for the best practice, we would like to have:

- Data defined in the child locations to be also fully part of parent location
- Data distinguished between the child locations
![スクリーンショット 2025-01-29 10.27.13.png](https://cognitedata.atlassian.net/a642e7bd-1a2d-4a51-ba25-837ae1bed9f2#media-blob-url=true&id=59b6fad8-ae9c-42df-ad80-4a5a5570d579&collection=contentId-5077204993&contextId=5077204993&width=1234&height=848&alt=%E3%82%B9%E3%82%AF%E3%83%AA%E3%83%BC%E3%83%B3%E3%82%B7%E3%83%A7%E3%83%83%E3%83%88%202025-01-29%2010.27.13.png)

例:  
設備Aと設備Bが共用している設備があるとする。searchでのデータ表示範囲を

<table><tbody><tr><th rowspan="1" colspan="1"><div><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設備A</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設備B</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>共用設備</strong></p><figure></figure></div></th></tr></tbody></table>

<table><tbody><tr><th rowspan="1" colspan="1"><div><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設備A</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>設備B</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>共用設備</strong></p><figure></figure></div></th></tr><tr><td rowspan="1" colspan="1"><p>A担当者</p></td><td rowspan="1" colspan="1"><p>○</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>○</p></td></tr><tr><td rowspan="1" colspan="1"><p>B担当者</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>○</p></td><td rowspan="1" colspan="1"><p>○</p></td></tr></tbody></table>

のように指定するには、Locationを設備A、設備B、共用設備の3つに分ける。

各アクセスグループは複数のLocationの参照を許可できるので、

A担当者のEntraグループを紐付けた権限グループには設備Aと共用設備へのread権限を、  
B担当者のEntraグループを紐付けた権限グループには設備Bと共用設備へのread権限を許可すればよい。

許可された複数のLocationから、表示されるデータを絞り込むことはユーザ側で可能。

==また全てのLocationについてSceneを紐づけることは必須ではない。たとえば工場-区画-建屋の３階層のLocationを設計し、Sceneは区画の階層だけに紐付け、各建屋はそれぞれのアクセスできるspaceに置かれた点群ファイルを参照するようにする、といった実現例が考えられる。==  
（追記：閲覧不可能なSceneを作らないために要請されるのは∀Scene->∃Locationであって、∀Location->∃Sceneは要請されない）

<!-- SOURCE_END: SpaceとLocationの設計 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Streamlit データエンジニアリングツール - Cognite Japan - Confluence.md -->
## File: Streamlit データエンジニアリングツール - Cognite Japan - Confluence.md

---
title: "Streamlit データエンジニアリングツール - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5730566148/Streamlit"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Streamlit データエンジニアリングツール

**録画**

[東ソー向けに作ったツールを教えてください - 2025/11/17 14:01 JST～Recording](https://drive.google.com/file/d/16Z2_75M-oy3opKP2WrkSQXAKIvWge5Y5/view)

### 背景

- アセットセントリックモデルからCore Data Modelに移行する際、ビルトインのUI機能だけではクライアントの要件を満たせず、データエンジニアがカスタマイズしたスクリプトを作成・保守しています。
- クライアントによっては、カスタムスクリプトの保守・運用スキルを持つメンバーをアサインするのが難しい場合があります。

### 目的

- データエンジニアが行うコンテキスト化のロジックをパターン化し、反復作業をユーザー自身が実行できるようにします。
- 保守運用の知識移転を容易にします。
- 複数のプロジェクトで利用可能な汎用的な仕組みを構築し、製品チームへのフィードバックや要望を反映させます。

### 基本的な構成

コンテキスト化作業をパイプラインとして実行します。

1. パターン定義とプレビュー機能（Streamlit）
2. パターン定義（yaml）
3. バッチ（function, data workflow）

### ツール一覧

<table><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>ツール名</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>機能</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>CDFの課題</strong></p><figure></figure></div></th></tr></tbody></table>

<table><tbody><tr><th rowspan="1" colspan="1"><div><p><strong>ツール名</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>機能</strong></p><figure></figure></div></th><th rowspan="1" colspan="1"><div><p><strong>CDFの課題</strong></p><figure></figure></div></th></tr><tr><td rowspan="1" colspan="1"><p><a href="https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5730566148#%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%89">ファイルアップロード</a></p></td><td rowspan="1" colspan="1"><p>File Extractor等で連携したファイルの内容をバリデーション、クレンジングした上でCDFに取り込む</p></td><td rowspan="1" colspan="1"><p>File Extractor経由でアップロードされるデータの取り込みに制限があり、個別にスクリプトが必要</p></td></tr><tr><td rowspan="1" colspan="1"><p><a href="https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5730566148#%E3%82%BF%E3%82%B0%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E5%8C%96">タグコンテキスト化</a></p></td><td rowspan="1" colspan="1"><p>タグ名の分割、読み替え等のパターンを定義してマッピングを行う。</p></td><td rowspan="1" colspan="1"><p>Entity MappingはAIによるトークン分割を行うが、大半のルールは個別にマッピングが必要</p></td></tr><tr><td rowspan="1" colspan="1"><p><a href="https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5730566148#%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E5%8C%96">ファイルコンテキスト化</a></p></td><td rowspan="1" colspan="1"><p>ファイルのディレクトリ名やコンテンツからパターンを作り、タグを抽出する</p></td><td rowspan="1" colspan="1"><p>ファイルをコンテキスト化するツールがないため、要件ごとに対応が必要</p></td></tr><tr><td rowspan="1" colspan="1"><p><a href="https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5730566148#%E3%83%87%E3%83%BC%E3%82%BF%E5%93%81%E8%B3%AA%E7%AE%A1%E7%90%86%E3%83%80%E3%83%83%E3%82%B7%E3%83%A5%E3%83%9C%E3%83%BC%E3%83%89">データ品質ダッシュボード</a></p></td><td rowspan="1" colspan="1"><p>アセットから各データ種類へのコンテキスト化の状態と、各データ種類からアセットへのコンテキスト化を可視化する</p></td><td rowspan="1" colspan="1"><p>標準のツールがない</p></td></tr><tr><td rowspan="1" colspan="1"><p><a href="https://cognitedata.atlassian.net/wiki/spaces/JP/pages/edit-v2/5730566148#%E5%9B%B3%E9%9D%A2%E3%82%B3%E3%83%B3%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E5%8C%96">図面コンテキスト化</a></p></td><td rowspan="1" colspan="1"><p>図面のコンテキスト化のバッチ処理をCDMに対して行い、図面コンテキスト化の状態やレビュー進捗を可視化する</p></td><td rowspan="1" colspan="1"><p>(2025/11時点) CDM対応のDiagram Parsing(New)はLocationベースで、クライアントの要件と合わせるのが難しい。多くのケースでアセットセントリックモデル側にデータを移動して作業しているため、複雑になっている。</p></td></tr></tbody></table>

#### ツール場所 (整理中)

toolkitの形で共有をする。

- toolkit - `eneos-toolkit`
	- [https://github.com/cognitedata/eneos-toolkit/tree/main/eneos/modules/21\_diagram\_contextualization\_tool](https://github.com/cognitedata/eneos-toolkit/tree/main/eneos/modules/21_diagram_contextualization_tool)
- toolkit - `agc-chem`
	- [https://github.com/cognitedata/agc-chem/tree/feature/dev/toolkit/modules/01\_diagram\_contextualization](https://github.com/cognitedata/agc-chem/tree/feature/dev/toolkit/modules/01_diagram_contextualization "https://github.com/cognitedata/agc-chem/tree/feature/dev/toolkit/modules/01_diagram_contextualization")
- toolkit - `tosoh`
	- [https://github.com/cognitedata/tosoh/tree/main/cdf\_toolkit\_v2/modules/00\_common](https://github.com/cognitedata/tosoh/tree/main/cdf_toolkit_v2/modules/00_common)

#### ファイルアップロード

- Excelやcsvで提供されるデータが不揃いである（ヘッダーの位置、必須列の欠如など）のをデータ種別ごとに必須列、抽出列バリデーション・補正した上してRawに投入するためのもの。
- データの不整合により後続のTransformationでエラーになることを防ぐ。
- プレビューの上に直接投入をする
- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### タグコンテキスト化

- 異なる命名体系のノードの紐付けを行うためのツール。標準機能のEntity Matchingは要素の並び替えによるマッチングしか対応しないが、実際はコードの読み替えなどさまざまなパターンがあるのに対応する。
- 上記スクリーンショットはPIタグとして使っているが、設定により他のView, プロパティに対しても使用可能。
- マッチング元のプロパティの文字列を正規表現で分割し、それぞれの要素を読み替え、組み合わせなどをパターンして設定することでマッチングをする。
- プレビューの上に直接DMを更新が可能
- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### ファイルコンテキスト化

- ファイルのパスとOCR内容からキーワードをタグとして抽出し、アセットとの紐付けを行う。
- 略語や同義語などの揺らぎにも対応する。
- プレビューの上に直接DMを更新が可能
- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### データ品質管理ダッシュボード

- アセットからみた各データ項目に対するコンテキスト化率を表示する。
- 任意の項目でドリルダウンして、コンテキスト化の傾向を確認し、重点的に対応が必要な項目を把握できる

#### 図面コンテキスト化

##### エイリアス一括設定

##### コンテキスト化実行

##### アノテーション一括更新

##### ステータスダッシュボード

- コンテキスト化の進捗をユーザー自身が確認をできるようにすることで、ユーザー自身がゴールの設定と自走をできるようにする。
- スクリーンショットはP&IDコンテキスト化の進捗だが、同様に他のデータの状態も可視化をすることでデータ品質管理という位置付け。

<!-- SOURCE_END: Streamlit データエンジニアリングツール - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: Streamlit ユースケース集 - Cognite Japan - Confluence.md -->
## File: Streamlit ユースケース集 - Cognite Japan - Confluence.md

---
title: "Streamlit ユースケース集 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5505253958/Streamlit"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## Streamlit ユースケース集

フォーマット

---

- **プロジェクト名**
- 画面のスクショ
- 製作者
- 説明

---

例)

## 関電水力

## セキュリティイベント通報フォーム (デモ用)

![image-20250804-040417.png](https://cognitedata.atlassian.net/e2dc089b-649e-4826-882e-4363d215044e#media-blob-url=true&id=e711af8f-4e5e-471f-8de0-96c3e3f934ce&collection=contentId-5505253958&contextId=5505253958&width=1396&height=1233&alt=image-20250804-040417.png)
- 製作者：岡田、山田
- 製作時期: 2025年7月
- 説明：
	- StreamlitとCanvasを用いて、セキュリティ事故が起きた際の対応マニュアルや過去事例の検索の簡易化と、対応内容を蓄積。
	- １つ目のボタンでは、判断基準ファイルをまとめたCanvasに遷移。
	- ２つ目のボタンでは、事故内容をCanvasに出力し、どのような対応をしたかをまとめる。
	- デモ用に作ったもので、実際の運用レベルまで作りこんではいない。このフォームで事故の報告をすると、関連するマニュアルや機器の情報が貼り付けられたCanvasが開く、というようなイメージ。

## リンク編集ツール

- 製作者：岡田
- 製作時期: 2025年9月
- 説明：
	- ノード間のリンクを編集するツール。
		- データモデルとビューを選択し、レコードを検索して選択する。
		- 関連するビューを選択し、レコードを検索して選択する。
		- 2つのレコード間のリンクを解除または設定できる。
	- データモデルとビューを選んでレコードを検索し、さらに関連するレコードを検索して表示して確認するという用途にも使えそう。

## 出光興産

## P&ID差分更新ツール

- 製作者：猪ノ口
- 説明：
	- P&IDの改訂があった場合に、図面（PDF）の縮尺も考慮して元のアノテーションをコピーできるようにしたもの
	- まとまった数を対応するので、作業進捗や一括承認作業、旧版のアーカイブ、新版のダッシュボードへの公開なども内包

## 手動紐付けツール

- 製作者：猪ノ口
- 説明：自動化し切れないコンテキスト化を手動でできるようにしたもの（ファイル、時系列で用意）

## 手動メタ付けツール

- 製作者：猪ノ口
- 説明：ファイルに手動でメタ付けできるようにしたもの

## JNC

## アノテーション一括承認

- 製作者：倉島
- 説明：CDMのアノテーションについて、StatusをSuggested->Approvedに変更。同時にファイルのtags（ダッシュボードへの公開管理フラグ）を編集してユーザーに公開

## ファイルのメタ情報編集

## バッチ間時系列比較(顧客作成)

## ラボデータ比較, 試薬管理(顧客作成, 今のところCDFのデータは使っていなそう)

補足：NMRは化学構造の推定に用いられる分析手法の一つで、反応の進行度や不純物の混入などの確認にも用いられる  
[https://www.webnta.com/images/light\_hydrogen\_solvent\_NMR\_reaction\_trackingNMRdata\_fig3.png](https://www.webnta.com/images/light_hydrogen_solvent_NMR_reaction_trackingNMRdata_fig3.png "https://www.webnta.com/images/light_hydrogen_solvent_NMR_reaction_trackingNMRdata_fig3.png")

## JPower

- 製作者：倉島
- 説明：
	- ユーザーがアセットのメタデータを編集できるように作成したツール
	- aliasesなどプロパティを編集してもらうことを想定

## カネカ

- 製作者：宮﨑
- 説明：
	- バッチIDや工程、品種などand条件でフィルタリング可能かつグラフ可視化、excelダウンロードなどが可能なバッチデータビューア
	- 現状はexcelサンプルデータ、実際には各カラムにtimeseriesデータが入ってくる
	- 別途SQLサーバーで定義されているバッチロジックをeventで取り込み、実装が必要
	- 現場オペレータが異常があった時等に過去のバッチと比較する利用を想定

## 東ソー

### データ管理ツール

- 概要：　データ投入、コンテキスト化を行うにあたり、ルールをユーザー自身がメンテナンスできるツール群を用意。
- 目的
	- コンテキスト化作業において、ユーザー自身が自走できるような仕組みとする。
	- 保守運用の知識移転を容易にする。
	- 複数のプロジェクトで利用可能な仕組みとして汎用化する。また、製品チームへのフィードバック、要望とする。

#### 設備情報アップロード

- 製作者：河野
- 目的：
	- Excelやcsvで提供されるデータが不揃いである（ヘッダーの位置、必須列の欠如など）のをデータ種別ごとに必須列、抽出列バリデーション・補正した上してRawに投入するためのもの。
	- データの不整合により後続のTransformationでエラーになることを防ぐ。
	- プレビューの上に直接投入をする
	- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### PIタグコンテキスト化

- 製作者：河野
- 目的：
	- 異なる命名体系のノードの紐付けを行うためのツール。標準機能のEntity Matchingは要素の並び替えによるマッチングしか対応しないが、実際はコードの読み替えなどさまざまなパターンがあるのに対応する。
	- 上記スクリーンショットはPIタグとして使っているが、設定により他のView, プロパティに対しても使用可能。
	- マッチング元のプロパティの文字列を正規表現で分割し、それぞれの要素を読み替え、組み合わせなどをパターンして設定することでマッチングをする。
	- プレビューの上に直接DMを更新が可能
	- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### ファイルコンテキスト化

- 製作者：河野
- 目的：
	- ファイルのパスとOCR内容からキーワードをタグとして抽出し、アセットとの紐付けを行う。
	- 略語や同義語などの揺らぎにも対応する。
	- プレビューの上に直接DMを更新が可能
	- 設定内容はyamlとして保存されており、DataflowのFunctionのバッチ処理にて設定した内容に基づき紐付きを更新を行う

#### コンテキスト化進捗確認

- 製作者：小森
- 目的：
	- コンテキスト化の進捗をユーザー自身が確認をできるようにすることで、ユーザー自身がゴールの設定と自走をできるようにする。
	- スクリーンショットはP&IDコンテキスト化の進捗だが、同様に他のデータの状態も可視化をすることでデータ品質管理という位置付け。

## コスモ石油

## アセットリンク編集ツール

- 製作者: 岡田
- 製作月: 2025年8月
- 説明:
	- ユーザーがCDFのアセットとファイルおよび時系列データとのリンクを編集できる。
		- アセットとファイルのリンクの追加/削除
		- アセットにとってファイルが主要図面であるかどうかの設定 (ダッシュボード上のハイライト表示用)
		- ファイルのファイル種別の設定 (リンク時に設定可能。ダッシュボード上の表示分類用)
		- アセットと時系列データのリンクの追加/削除
	- アセットセントリック用です。DM対応で同様のアプリを鋭意開発中。

<!-- SOURCE_END: Streamlit ユースケース集 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: モデル拡張の判断と設計のノウハウ - Cognite Japan - Confluence.md -->
## File: モデル拡張の判断と設計のノウハウ - Cognite Japan - Confluence.md

---
title: "モデル拡張の判断と設計のノウハウ - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5064228977"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## モデル拡張の判断と設計のノウハウ

この記事でやりたいこと:  
データの格納先の選択肢がAsset-centricの時と比べて格段に増えたため、お客さんのマスタにあるデータをどうCDFに保存・表示すると検索の動線がスムーズになるか、考えることが増えました。  
皆さんが手を動かす中で、気付いたノウハウを集約したいです。

記事の後ろのほうに事例を追加して行って、Discussionの中で「他のプロジェクトでも教訓になりそう」となった指針を記事の先頭にまとめる、という編集方式でいかがでしょうか。

Discussion:

## Core DMで完結させても十分なケース

Asset, Equipmentの

descriptionに登録 ← 対象の設備が絞り込み結果に含まれていれば、余分な結果が含まれてもよい場合  
例）

tags ← その属性が登録されている設備だけに絞り込み、件数を数えるのにも使いたい場合  
例）

Classを作成しTypeとして各インスタンスにリンク ← 〜〜〜〜〜場合  
例）

## Core DMから拡張した方が良いケース

  
  

## Cognite NEAT: spreadsheetからのデータモデルimport

工事中

[https://cognite-neat.readthedocs-hosted.com/en/latest/excel\_data\_modeling/rules.html](https://cognite-neat.readthedocs-hosted.com/en/latest/excel_data_modeling/rules.html)

[https://cognitedata.slack.com/archives/C03G11UNHBJ/p1737717031194989?thread\_ts=1737638819.904489&cid=C03G11UNHBJ](https://cognitedata.slack.com/archives/C03G11UNHBJ/p1737717031194989?thread_ts=1737638819.904489&cid=C03G11UNHBJ)

## 事例集

JNC

<得られた教訓の概要>

[関西電力火力](https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5108793350/JNC#%E9%96%A2%E8%A5%BF%E9%9B%BB%E5%8A%9B\(%E7%81%AB%E5%8A%9B\) "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5108793350/JNC#%E9%96%A2%E8%A5%BF%E9%9B%BB%E5%8A%9B(%E7%81%AB%E5%8A%9B)")

<得られた教訓の概要>

<!-- SOURCE_END: モデル拡張の判断と設計のノウハウ - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md -->
## File: 設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md

---
title: "設備階層/機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5346295921"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## 設備階層/機器仕様関連のデータモデリングの課題

データモデリングへの入門の手始めとして、設備階層と機器仕様をどうモデリングするか、について検討しています。まだ解決できていませんが、検討の過程を共有しながら皆さんと解決していきたいと思っています。(2025/5/12現在。by 岡田)

## 設備階層

## 一般的な実装例

`cdf_cdm` では設備階層(SAPでいう機能場所、Maximoでいうロケーション、CFIHOSでいうTag)は `CogniteAsset` で、実体のある設備や機器(SAPでいう設備、Maximoでいう資産、CFIHOSでいうEquipment)は `CogniteEquipment` で実装するイメージとなっている。

下記はSAP PMを運用する客を想定した実装例。 `CogniteAsset` を継承した `Floc` というクラスでparent / children で階層を作り、最下層の `Floc` に `CogniteEquipment` を継承した `Equip` というクラスを割り当てる。

![image-20250512-105930.png](https://cognitedata.atlassian.net/6cc12488-e329-41d7-8286-c789faf9a7b7#media-blob-url=true&id=9f76f524-47d1-4b2a-833a-b56d602adbb7&collection=contentId-5346295921&contextId=5346295921&width=1146&height=485&alt=image-20250512-105930.png) ![image-20250512-122855.png](https://cognitedata.atlassian.net/231984c2-9beb-499f-8509-533b1eb6a692#media-blob-url=true&id=2c60cef8-5eea-40f7-9ff6-5095dc971417&collection=contentId-5346295921&contextId=5346295921&width=1735&height=595&alt=image-20250512-122855.png)

`Floc` には `Hierarchy Level` のようなPropertyを設定し、階層を指定するのがよさそう。検索時にFiltersで指定できるようになる。

## 厳密な実装例 (うまくいかない)

以下はうまくいかなかった例。一般に会社、工場、エリア、装置、設備、というような階層が明確に作られるため、そのようにクラスを配置する方がAtlas AIによる検索対象を指定しやすいと考えたが、 `CogniteAsset` を継承したひとつのクラスだけで階層を作らないと、Searchの画面でTreeがうまく表示されないなどの不具合が出た。(このあたり、いい例があれば教えてほしい。)

(2025/10/20追記)

異なるViewのインスタンス間におけるパスや階層の表示はサポートしていないとのこと。開発として、実装する予定だが、（2025年5月の時点で）早くて26Q1らしい、最新情報要確認。

参考リンク：

[https://cognitedata.atlassian.net/browse/UX-4228](https://cognitedata.atlassian.net/browse/UX-4228)

## クラス / タイプ

## クラスの概要

設備は一般に機種で分類(SAPやCFIHOSでいうクラス、Maximoでいう分類)され、与えられた機種に応じて詳細な仕様(SAPでいう特性値、Maximoでいう仕様、CFIHOSでいうProperty)を入れられたり、故障部位の候補を指定できたりする。クラスは設備階層(機能場所、ロケーション、Tag)側にも、設備(設備、資産、Equipment)側にも設定することができ、仕様の場合、階層側のクラスにはプロセス設計上の要求仕様が、設備側には設置された機器の購入仕様が入力される。

クラスの分類の仕方はCFIHOSでTag ClassおよびEquipment Classが階層的に定義されており、Tag Classに対応するEquipment Classも決まっている。

参考 (CFIHOS 2.0のRDLを見やすく加工したもの): [https://docs.google.com/spreadsheets/d/1mmTLivBHrEmfJHSN7yMoVgxI96yFU2TB/edit?usp=sharing&ouid=105941753345609856745&rtpof=true&sd=true](https://docs.google.com/spreadsheets/d/1mmTLivBHrEmfJHSN7yMoVgxI96yFU2TB/edit?usp=sharing&ouid=105941753345609856745&rtpof=true&sd=true)

## クラスの使い分け

Tag ClassとEquipment Classを使い分けるのは面倒なので、Equipment Classだけを使用する客も多いと思われる。分けて運用する場合は、Pump, Compressor, Vessel, Heat Exchangerといった中分類をTag Classで、Centrifugal Pump, Rotary Pump, Reciprocating Pumpといったより詳細な小分類をEquipment Classで設定するのがよさそう。

CFIHOSで仕様(Property)はTag ClassおよびEquipment Classそれぞれについて詳細に定義されている(ただし必ずしもこれに従う必要はなく、実際に従っている客もあまりいなさそう)。EAM/CMMSでは設備にクラスを割り当てると、クラスに応じた詳細仕様を入力できるようになる。

## アセットクラスの実装

`cdf_cdm` には `CogniteAssetClass`, `CogniteAssetType`, `CogniteEquipmentType` の3つが用意されている。これらの使い分けの意図はよく分からないが、とりあえず `CogniteAssetClass` と `CogniteEquipmentType` を実装してみた。

`CogniteAsset` を継承した `Floc` として `test3` という階層を作り、 `CogniteAssetClass` を継承した `FlocClass` としてCompressorのPropertyを入力してダイレクトリンクする。

すると、 `FlocClass` を割り当てられた `Floc` には、 `FlocClass` に設定されたPropertyが含まれて表示される。

ちなみに `FlocClass` には `Floc` からのダイレクトリンクだけでなく、reverseのリンクをPropertyとして設定する必要がある。すると `FlocClass` に `Floc` へのリンクのタブが表示される。

なお `FlocClass` を割り当てたとしても、クラス名などは `Floc` に表示されない。また設備を検索する際にクラス名でフィルターをかけたいところだが、 `FlocClass` のPropertyで 絞り込むことはできない。よって `Floc` にPropertyとして `Functional Location Class Name` や、その上位にあたる `Functional Location Category` (静機器、回転機、電気、計装といった大分類) などの項目を設定するのがよさそう。

## アセットクラスの継承による細分化 (したいけどうまくいかない)

前述のようにアセットクラスはクラス(機種)に応じた詳細仕様を設定できるようにしたい。 `FlocClass` をさらに継承した `FlocClassCompressor` というクラスを作り、これを割り当てると圧縮機用の仕様が入力でき、 `FlocClassPump` というクラスを割り当てればポンプ用の仕様が入力できる、としたいところ。しかし残念ながら `FlocClassCompressor` を `Floc` に割り当てても `FlocClassCompressor` のPropertyは `Floc` に表示されなかった。つまり、機種ごとで定義されるさまざまなPropertyについて、すべてを `CogniteAssetClass` を継承した `FlocClass` というひとつのクラスに設定する必要がある？ これは大きな課題と思われる。

## 設備クラスの実装 (アセットクラスのようにはいかない)

同様に `CogniteEquipment` を継承した `Equip` に対して `CogniteEquipmentType` を継承した `EquipType` を割り当てても、 `EquipType` に入力したPropertyは `Equip` では表示されなかった。なぜだろう。

<!-- SOURCE_END: 設備階層機器仕様関連のデータモデリングの課題 - Cognite Japan - Confluence.md -->

================================================================================

================================================================================

<!-- SOURCE_START: 開発環境の構築 - Cognite Japan - Confluence.md -->
## File: 開発環境の構築 - Cognite Japan - Confluence.md

---
title: "開発環境の構築 - Cognite Japan - Confluence"
source: "https://cognitedata.atlassian.net/wiki/spaces/JP/pages/5044734298"
created: 2025-12-28
description:
tags:
  - "CDF"
  - "CogniteDataFusion"
image:
---
## 開発環境の構築

## 概要

Windows上でPython環境を構築するにはいくつかの方法があるが、同じPythonのライブラリーをインストールしてもLinux版、Mac版とWindows版で実装が異なる場合があり、本番(多くの場合Linux)とは異なるエラーが発生する場合がある。WSL上でUbuntu環境を作ってインストールし、Windows上のVS Codeで編集する方がよさそう。

当社ではPoetryを使ってライブラリーのバージョン管理を行う。プロジェクト毎でPythonのバージョンが異なる可能性があるため、複数のPythonのバージョンを切り替えられるような環境構築が必要となる。

**Windows**

- Python公式インストーラー: 複数のバージョンをインストールし、pyコマンドや環境変数のPATHで切り替えられるが、設定が面倒。
- Miniconda: 複数の環境を切り分けられる。ライブラリーのバージョン管理もできるが、インストールが永遠に終わらない不具合もよく起きるのでお薦めしない。環境の切り分けだけに使い、pipでインストールする方がよさそう。Anacondaは企業利用では有償になったので使用せず、Minicondaを利用すべき。
- pyenv-win: Poetryとの組み合わせではこれが一番よく使われる。
- uv: Pythonのバージョン管理とPoetryと互換のあるライブラリーのバージョン管理ができる。Rustで構築されているため速い。当社ではPoetryから uv に移行しつつあるらしい。インストールがとんでもなく速い。  
	[uv](https://cognitedata.atlassian.net/wiki/spaces/~712020ca800712abdb4f3cb65d23b3063cd49e/pages/5192318982)
- asdf: 現時点でWindowsには対応していない。

**WSL (Linux / Macも同じ？)**

- Python公式インストーラー: Linux版をインストールする。
- Miniconda: Windowsに同じ。
- pyenv: Windowsに同じ。
- uv: Windowsに同じ。
- asdf: PythonだけでなくPoetryやNode.jsのインストールおよびバージョン管理を行えるので、今後はこれが一番よいのでは？.tool-versionsというテキストファイルに設定が書き込まれるので、Gitで同期されればasdf installのコマンド一発で必要なバージョンのPythonやPoetryがインストールされる。
	- Poetryを使うなら、asdf + Poetry という組み合わせはよさそう。
	- uvを使うなら、asdfは使わずuvだけでPython環境とパッケージ管理を行う方がよい。
	- ダッシュボードのソースコードを触ることがあるならば、nodeやyarnのインストールが必要になるため、asdfでインストールした方がいいかも。（nodeやyarnをグローバルインストールするメリットがないため）
- Dockerを用いたコンテナ構築: PaaSとの親和性は一番高いが、設定は難しい。WindowsやMacのDocker Desktopは企業利用では有償なので要注意。WindowsならWSL上でDocker Engine (Community Edition)をインストールする。

詳細はGemini Deep Researchで作成したこちらのレポートおよび下記の表を参照。

[https://docs.google.com/document/d/1PmI1JzEwxiJ78Eyj\_rqOddfJjx-1GIk0ctFwLnxk91c/edit?usp=sharing](https://docs.google.com/document/d/1PmI1JzEwxiJ78Eyj_rqOddfJjx-1GIk0ctFwLnxk91c/edit?usp=sharing)

<table><colgroup><col> <col> <col> <col> <col> <col> <col></colgroup><tbody><tr><td rowspan="1" colspan="1"><p>スコープ</p></td><td rowspan="1" colspan="1"><p>Python公式</p></td><td rowspan="1" colspan="1"><p>Miniconda</p></td><td rowspan="1" colspan="1"><p>pyenv</p></td><td rowspan="1" colspan="1"><p>poetry</p></td><td rowspan="1" colspan="1"><p>uv</p></td><td rowspan="1" colspan="1"><p>asdf</p></td></tr><tr><td rowspan="1" colspan="1"><p>Pythonのバージョン管理</p></td><td rowspan="1" colspan="1"><p>△ (py)</p></td><td rowspan="1" colspan="1"><p>〇 (conda)</p></td><td rowspan="1" colspan="1"><p>〇 (venv)</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>〇 (venv)</p></td><td rowspan="1" colspan="1"><p>〇 (venv)</p></td></tr><tr><td rowspan="1" colspan="1"><p>Pythonパッケージのバージョン管理</p></td><td rowspan="1" colspan="1"><p>△ (pip)</p></td><td rowspan="1" colspan="1"><p>△ (conda)</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>〇</p></td><td rowspan="1" colspan="1"><p>〇</p></td><td rowspan="1" colspan="1"><p>-</p></td></tr><tr><td rowspan="1" colspan="1"><p>Poetry, uv, Node.jsなどのバージョン管理</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>〇</p></td></tr><tr><td rowspan="1" colspan="1"><p>ローカルで使用する設定ファイル</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>-</p></td><td rowspan="1" colspan="1"><p>.python-version</p></td><td rowspan="1" colspan="1"><p>pyproject.toml</p></td><td rowspan="1" colspan="1"><p>.python-version, pyproject.toml</p></td><td rowspan="1" colspan="1"><p>.tool-versions</p></td></tr><tr><td rowspan="1" colspan="1"><p>プラットフォームの注記</p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"><p>Windowsはpyenv-win</p></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"></td><td rowspan="1" colspan="1"><p>Windows未対応</p></td></tr></tbody></table>

## asdfのインストール

以下にasdfのインストール方法、またasdfを使用した言語、ツールのインストール方法を記載する。

0.15のインストール手順は、Windows上でPython, Poetryをasdf経由でインストールする方法になる。

0.16のインストール手順は、MacOS上でnode, yarnをasdf経由でインストールする方法になる。

asdfの操作はOSに依存しないので、必要に応じて（例えばWindowsで0.16を使用する場合など）それぞれの手順を参考にすること。

## asdf（0.15）によるPython, Poetryのインストール

[asdf](https://asdf-vm.com/ "https://asdf-vm.com/") を用いて、WSLのUbuntu上で設定する手順：

(注) バージョン0.16で大きく変わったらしい。本メモは下記の旧バージョンのインストール手順に基づく。

[はじめよう | asdf](https://asdf-vm.com/ja-jp/guide/getting-started-legacy.html)

### asdfのインストールと使い方 (Ubuntu)

#### インストール

以下のコマンドを実行。(0.16から大きく変わるので、0.15をインストール)

`git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.15.0 echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc source ~/.bashrc`

下記コマンドでバージョンが表示されればOK。

`asdf version`

Python, Poetry のPluginをインストール

`asdf plugin-add python asdf plugin-add poetry`

Pythonに必要な依存関係をインストール

`sudo apt update; sudo apt install build-essential libssl-dev zlib1g-dev \ libbz2-dev libreadline-dev libsqlite3-dev curl \ libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev \ libffi-dev liblzma-dev`

#### Globalでの環境設定

以下のような設定を行うと、フォルダーによらず指定した Python / Poetry を利用可能になる。

`asdf install python 3.12.8 asdf global python 3.12.8`

`asdf install poetry 2.0.1 asdf global poetry 2.0.1`

#### 特定のフォルダー下での環境設定

特定のプロジェクトのフォルダー下で以下のようなコマンドを実行すると、指定したバージョンのPython / Poetryを利用可能になる。

`asdf install python 3.11.7 asdf local python 3.11.7`

`asdf install poetry 1.8.3 asdf local poetry 1.8.3`

これらのコマンドを実行すると、 `.tool-versions ` というテキストファイルに設定した環境が記述される。

先に`.tool-versions` に記述してからインストールのコマンドを実行してもよい。

`asdf install`

## asdf（0.16）によるnode, yarnのインストール

asdfを使ってダッシュボード用にnode, yarnをインストールする方法。0.16.x系を使用したので、その場合も参考可能。

1. [Homebrew](https://brew.sh/ja/ "https://brew.sh/ja/") でasdfのインストール（Macの場合。Windowsの場合は↑の手順を参考）

`brew install asdf`

1. シェルの設定に以下を追加

`echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ${ZDOTDIR:-~}/.zshrc`

1. asdfのnodejsプラグインをインストール

`asdf plugin add nodejs`

1. 任意のnodejsのバージョンをインストール

`asdf install nodejs 23.9.0`

1. グローバルバージョンを設定（ `asdf global` はasdf==0.16からdeprecated）

`asdf set --home nodejs 23.9.0`

1. asdfのyarnプラグインをインストール

`asdf install yarn 1.22.22`

※ここで `Missing one or more of the following dependencies: tar, gpg` というエラーが出たら、以下のコマンドを順に実行

`brew install gnupg brew install gnu-tar export PATH="$(brew --prefix)/opt/gnu-tar/libexec/gnubin:$PATH" source ~/.zshrc`

1. グローバルバージョンを設定

`asdf set --home yarn 1.22.22`

1. パスを確認

`which node # .asdfを含むパスが出てくればOK which npm # .asdfを含むパスが出てくればOK which yarn # .asdfを含むパスが出てくればOK`

### 既にnodejsやyarnをグローバルインストールしてしまっている場合

1. 現在のバージョンを確認

`node -v  npm -v yarn -v`

1. 既存のグローバルパッケージを新しい環境に移行

`# 現在のグローバルパッケージリストを取得 npm list -g --depth=0 > global_npm_packages.txt # asdfで管理されるnpmでパッケージを再インストール cat global_npm_packages.txt | grep -v npm | awk -F ' ' '{print $2}' | awk -F '@' '{print $1}' | xargs npm -g install`

1. グローバルインストールの削除

`brew uninstall --ignore-dependencies node brew uninstall --ignore-dependencies yarn`

1. パスを確認

`which node # .asdfを含むパスが出てくればOK which npm # .asdfを含むパスが出てくればOK which yarn # .asdfを含むパスが出てくればOK`

## Poetryの使い方

PoetryもVersion 2.0で `pyproject.toml` の書式が変わった。PEP 621に準拠した\[project\]セクションのサポートが追加され、uvなど他のツールとも互換性がありそう。

- 当面は1.8系を使う方がよさそう。
- 2.0系で使うなら、uvに移行することも検討。

### 初期化して利用する場合

`pyproject.toml` がなく、初期化してPoetryを利用する場合、初期化コマンドを実行する。これにより `pyproject.toml` が作成される。

`poetry init`

![image-20250225-142251.png](https://cognitedata.atlassian.net/41b8567a-397d-4a0a-b5a2-8e13e7d4f03a#media-blob-url=true&id=f0ce85bf-cc94-4cc1-931b-d3e8142049ed&collection=contentId-5044734298&contextId=5044734298&width=890&height=737&alt=image-20250225-142251.png)

### プロジェクト毎に環境を構築するよう設定

毎回設定が必要かよく分からず。

`poetry config virtualenvs.create true poetry config virtualenvs.in-project true poetry config installer.parallel true`

### ライブラリーの追加 / 削除 / 更新

`poetry add ライブラリー名 # 追加 poetry remove ライブラリー名 # 削除 poetry update # pyproject.tomlに記載されているバージョン指定条件に応じて各ライブラリを更新`

![image-20250225-143637.png](https://cognitedata.atlassian.net/5937c65a-62ec-494c-a98b-c695c578f9e0#media-blob-url=true&id=799f876d-74d9-437d-9ebb-c70709b20319&collection=contentId-5044734298&contextId=5044734298&width=688&height=275&alt=image-20250225-143637.png)

### Shellの実行 / 終了

`poetry shell`

`exit`

![image-20250225-143055.png](https://cognitedata.atlassian.net/821a7c26-0760-4fba-a742-1fc1bafe4d90#media-blob-url=true&id=730079bc-fc64-4a0e-b9a3-3957e5e9e586&collection=contentId-5044734298&contextId=5044734298&width=740&height=227&alt=image-20250225-143055.png)

Poetry 2.0系ではShellがなくなった。次のコマンドを使う。

`poetry env activate`

またはプラグインをインストールすれば `poetry shell` が引き続き使える。

`poetry self add poetry-plugin-shell`

### Pythonコードの実行

`poetry run python xxx.py`

## uv のインストールと使い方

## uv のインストール

[公式](https://docs.astral.sh/uv/getting-started/installation/#standalone-installer "https://docs.astral.sh/uv/getting-started/installation/#standalone-installer") に従ってインストール。

- Mac/Linux:
	`curl -LsSf https://astral.sh/uv/install.sh |sh`
- Windows:

`powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`

## uvの更新

uv のコマンドで自身を更新可能。

`uv self update`

## uv の使い方

### Pythonのインストールとバージョン指定

利用するバージョンのPythonをインストールする。

`uv python install 3.11 3.12`

特定ディレクトリーで利用するPythonのバージョンを指定。

`uv python pin 3.12`

### プロジェクト作成

新しくプロジェクトのディレクトリーを作成する場合：

`uv init myproject`

uv用の `pyproject.toml` があるディレクトリーでuvの利用を始める場合、そのディレクトリーで：

`uv init`

poetry用の `pyproject.toml` があるディレクトリーで、poetry から uv に移行する場合、そのディレクトリーで：

`uvx migrate-to-uv`

### パッケージの追加・削除

パッケージを追加

`uv add numpy`

バージョン指定で追加する場合

 ` uv add numpy==2.2.4`

パッケージを削除

`uv remove numpy`

requirements.txt から追加

`uv add -r requirements.txt`

### パッケージの同期

`pyproject.toml` をもとにパッケージをインストールする。( `poetry install` に相当 )

`uv sync`

`pyproject.toml` をもとに仮想環境を構築

`uv venv`

インストールされたパッケージの一覧を確認

`uv pip list`

### 仮想環境のシェルの起動

仮想環境のシェルを起動。( `poetry shell` に相当)

`. .venv/bin/activate`

### 実行

Pythonスクリプトの実行。

`uv run main.py`

### Jupyter Lab/NotebookやVSCodeでのカーネル利用

仮想環境をカーネルとして利用するためには、 `ipykernel` のインストールが必要。

`uv add ipykernel`

さらに他の環境で実行されたJupyter Lab/Notebookでカーネルとして選べるようにするためには、カーネル名を指定して設定が必要。

`uv run ipython kernel install --user --name=kernel-name`

カーネルが設定されると下図の例のように選べるようになる。

![image-20250603-131256.png](https://cognitedata.atlassian.net/d0ff1d7e-9d21-45fd-812c-bf07a6061964#media-blob-url=true&id=7009ad71-f760-4f58-b296-d8c4c386b94c&collection=contentId-5044734298&contextId=5044734298&width=937&height=684&alt=image-20250603-131256.png)

<!-- SOURCE_END: 開発環境の構築 - Cognite Japan - Confluence.md -->

================================================================================

