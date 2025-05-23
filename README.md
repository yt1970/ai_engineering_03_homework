# -*- AIエンジニアリング講座DAY3任意提出宿題用README-*-

---
# **概要**

このリポジトリは、「AIエンジニアリング講座」DAY３任意宿題用RAG検証結果のリポジトリです。

LLMモデルは、gemma-2-2b-jpn-itを使用しています。

参照文書は防衛省令和７年度予算の概要（令和７年４月２日）のPDF版を使用しました。

[https://www.mod.go.jp/j/budget/yosan_gaiyo/fy2025/yosan_20250402.pdf](https://www.mod.go.jp/j/budget/yosan_gaiyo/fy2025/yosan_20250402.pdf)

文書のベクトル化においては、「sarashina-embedding-v1-1b」を使用しました。

評価については、独自の評価指標で、GemnAPIを使用した５段階評価を行っています。

---
# **リポジトリのフォルダ構成**

**data** フォルダ   ・・・参照文書PDF、参照文書のテキストファイル、参照文書のチャンク分割ファイル（整形なし、整形あり）、質問と模範回答ファイル、質問ファイルなどが入っています。

**result** フォルダ   ・・・LLMが出力したレスポンスファイル、出力を評価したファイル、評価結果のサマリーファイル（どれもLLMのみ、整形なしRAG、整形ありRAGがあります。）が入っています。

**ai_engineering_03_任意宿題T4（提出用）.ipynb**   ・・・LLMの回答生成、評価、RAGの構成などを行ったノートブックです。

**PDFtoChank.ipynb**   ・・・参照文書のテキスト化、整形、チャンク分割を行ったノートブックです。

**README.md** ・・・このファイルです。

**HOMEWORK.md** ・・・宿題用ファイル

---

# **＜宿題の要件事項＞**

## **I  質問設計の観点と意図**

 ### **A  参照文書の選定について**

  1. 宿題提出までの期限を考慮すると、質問の設計を行う前提として、質問に対する模範解答が自分にとって容易に判別できることが必要であると考え、自己の業務ドメインの範囲内の事項を参照文書で扱うこととした。
  
  2. また、自己の業務ドメインに関する事項のうち公表資料を参照文書とすることとした。
  
  3. 加えて、RAGの効果を見るために、最近、具体的には令和７年４月に公表された資料を参照文書とすることとした。
  
  4. 求められた質問数やRAG構築などの条件からある程度のボリューム（紙面にして５０ページ以上）のある資料を参照文書した。
  
  5. これらを考慮し、参照文書として防衛省ホームページに公表されている防衛省令和７年度予算の概要（令和７年４月２日）のPDF版を使用することとした。
 
 ### **B 質問設計の観点と意図**

  1. 質問の設計にあたっては、課題のヒントとアドバイスに従って、①単純な事実を問う質問、②LLMでは答えられない事実を問う質問、③複数の事実を組み合わせないと回答できない質問の３点を考慮し設計した。加えて、参照資料からでは回答できない質問も１問設計した。
  
  2. 質問自体は、参照文書をLLMモデルであるGeminiに投入し、1.の条件を付して生成させた。合わせて模範回答も生成させ、評価に用いることとした。生成された質問と模範回答には、一部はルシネーションも見られたため、確認の上、正しい質問、正しい模範回答に改めて、使用した。

  3. 設計の意図としては、参照文書を引用しなければ回答できない質問を作成することで、RAGの検索精度についても検証できることを意図した。


## **II RAGの実装方法と工夫点**

 1. RAGの実装の際、文書のベクトル化に用いたのは、Paper&Hucks Vol.32」で井伊講師がご紹介していた日本語センテンストランスフォーマーである「sarashina-embedding-v1-1b」（SB Intuitions開発）を使用した。その理由は、日本語のベクトル化精度を期待したことによる。
 2. また、文書のチャンクはPDFのページ単位とし、ある程度文脈を保持した形でチャンクが構成されることを意図した。
 3. データのノイズなどの影響を見るため、「PyPDF2」がテキスト化したままの文書とテキストの整形（無駄な空白や改行の除去、必要な改行の追加）を自動で行った整形済み文書の２つを検証した。

## **III 結果の分析と考察**
1.   RAGなし

    **　合計点数:3　**
    **　平均値：0.60　**
    
2.   RAGあり（テキスト整形なし）

    ** 合計点数:3 **
    ** 平均値:0.60 **

3.    RAGあり（テキスト整形なし）

    ** 合計点数:7 **
    ** 平均値:1.40 **

　テキスト整形によるノイズの除去のおかげか、RAGあり（テキスト整形あり）において、７年度予算の総額やGDPの記載のないことなど一部は正確に回答できるようなった。


　一方で、RAGを行っても回答の精度が大幅に向上しなかったのは、参照文献自体の構成の問題とも解釈できる。防衛省のみならず、政府機関が公表する資料には表やグラフが多用され、PyPDF2による単純なテキスト化では、表やグラフの文脈を表現しきれなかったものと考察する。

　事実、質問とのコサイン類似度のトップ３のチャンク参照文書には、回答を含む文書が選ばれないケースがあった。一例を言えば、１番目の防衛予算の総額と内訳を問う質問に対し、総額を含む文書が選ばれましたが、内訳に関する文書は正しく選ばれなかった。これは、元文書においては、内訳の部分が表や円グラフで示されており、この部分が適切にテキスト化されなかったことによるものと推察する。

## **IV 発展的な改善案(任意)**
#### 考察１　表やグラフの読み取り精度を上げる
  　3で考察した通り、表やグラフを文脈にそってテキスト化することがRAGの精度を改善させるために必要である。これは、講義の中で触れられていた「データの質を上げる」というアプローチの１つであり、表がグラフのある文書をRAGの参照文書とする際には避けては通れない事項であると考察する。

  　Geminiに参照文書を読ませて質問、模範回答を生成させた際、グラフや表に含まれる内容も正確に回答していた。マルチモーダルなLLMなどでテキスト化すれば精度の高い表やグラフの読み取りさせるのも、一つの手段となりうると考える。

#### 考察２　RAGに用いる文書の選定
  　一方で、テキスト化により文脈が失われる表やグラフを多用した文書ではなく、テキスト中心の文書をRAGの参照文書とするというアプローチもあると考える。

#### 考察３　チャンクの分割の工夫
  　今回は期限が短いこともあり、ページによるチャンク分割を行ったが、目次の項目ごとにチャンクを分けることにより、必要なチャンクをLLMに渡すことができ精度が向上するものと考えられる。
  　その検証として、目次から抽出した目次の項目ごとにチャンクを分割することを自動化し、その上でRAGの参考文書にすることを試みたが、チャンク分割が自動でうまく分けられずに断念した。
  　精度を見る上では手動で印をつけてチャンク分けすることも可能であるので、機会があればこの方法も試したい。



# 宿題実施の方針

1. **ベースラインモデルの評価**  
   素のモデルで回答を生成し、最新の情報に対しては正しく回答できないことを確認します。

2. **参照文書のテキスト化**  
   参照文書をPyPDF2でテキスト化します。

3. **文書整形なしでのページごとのチャンク化**  
   文書整形なしでぺーじごとにチャンク化し、回答を生成させます。

4. **文書整形ありでのページごとのチャンク化**  
   無駄な空白や改行の削除、意味のある改行の追加などにより回答の精度向上を図ります。

5. **回答精度の測定方法**
    googleGeminiのAPIを利用して、Geminiに評価指標を与え、点数で評価します。

## 質問事項

  宿題の要件に従い、参照文書に関するテーマから５つを質問事項として設け、模範解答も合わせて設定しました。

  なお、５つの質問のうち、１つは参照文書をみても回答ができない事項であり、その旨をきちんと回答できるかどうかも評価できるようにしました。


```
[
  {
    "質問": "令和7年度の防衛費の予算総額はいくらですか？また、その主要な内訳を具体的に説明してください。",
    "模範解答": "令和7年度の防衛費の予算総額は**7兆9,170億円**です。\n\nその主要な内訳は以下の通りです。\n\n* **装備品費等:** 2兆8,354億円（新たな装備品の取得、既存装備品の改修などに係る経費）\n* **隊員人件・糧食費:** 2兆2,682億円（自衛隊員の給与、食料、被服などに係る経費）\n* **施設整備・維持費等:** 7,305億円（基地・駐屯地の整備、維持管理、借料などに係る経費）\n* **教育訓練等:** 6,845億円（隊員の教育訓練、演習などに係る経費）\n* **研究開発費:** 3,812億円（将来の装備品や技術に関する研究開発に係る経費）\n* **その他:** 1兆2,172億円（上記の主要な経費に含まれないその他の経費）",
    "出典": "1ページ「令和７年度予算の概要」内「１．歳出」の表"
  },
  {
    "質問": "防衛力整備計画で重視されている7つの分野は何ですか？ それぞれの分野における具体的な取り組みの例を挙げてください。",
    "模範解答": "防衛力整備計画で重視されている7つの分野は以下の通りです。\n\n1.  **スタンド・オフ防衛能力の強化:**\n    * **具体的な取り組み例:** 島嶼防衛用高速滑空弾の取得、長射程巡航ミサイルの開発・取得\n2.  **統合防空ミサイル防衛能力の強化:**\n    * **具体的な取り組み例:** イージス・システム搭載艦の建造、地対空誘導弾パトリオット（PAC-3）の能力向上型への換装\n3.  **無人アセット防衛能力の強化:**\n    * **具体的な取り組み例:** 各種無人航空機（UAV）の導入、無人水中航走体（UUV）の研究開発\n4.  **宇宙・サイバー・電磁波領域における能力の強化:**\n    * **具体的な取り組み例:** 衛星コンステレーションの構築、サイバー攻撃対処能力の向上、電磁波領域における優勢確保のための装備開発\n5.  **情報機能の強化:**\n    * **具体的な取り組み例:** 各種センサーの情報収集能力向上、収集した情報の分析・共有基盤の強化\n6.  **輸送能力の強化:**\n    * **具体的な取り組み例:** 大型輸送機の取得、輸送艦の建造\n7.  **持続性・強靭性の強化:**\n    * **具体的な取り組み例:** 弾薬・燃料等の備蓄拡充、重要施設の強靭化",
    "出典": "2ページ「２．主要経費」内「（１）重点的な整備・強化を行う主要分野」の箇条書き"
  },
  {
    "質問": "過去5年間の日本の防衛費の推移を説明してください。特に、GDP比での変化に着目してください。",
    "模範解答": "資料には令和7年度の予算概要のみが記載されており、過去5年間の防衛費の推移に関する具体的な数値データは含まれていません。そのため、この資料のみで過去5年間の防衛費の推移とGDP比での変化を説明することはできません。",
    "出典": "資料全体（過去のデータに関する記載なし）"
  },
  {
    "質問": "令和7年度の防衛予算における研究開発費の割合はどのくらいですか？ また、研究開発費は具体的にどのような分野に投資されていますか？",
    "模範解答": "令和7年度の防衛予算総額は7兆9,170億円であり、研究開発費は3,812億円です。したがって、研究開発費の割合は、\n\n$$\\frac{3,812 \\text{億円}}{79,170 \\text{億円}} \\times 100 \\approx 4.81 \%$$\n\n研究開発費は、将来の装備品や技術に関する広範な分野に投資されています。具体的には、以下の分野などが挙げられています。\n\n* **スタンド・オフ防衛能力:** 島嶼防衛用高速滑空弾、長射程巡航ミサイル関連技術\n* **統合防空ミサイル防衛能力:** 新型レーダー、迎撃ミサイル関連技術\n* **無人アセット防衛能力:** 無人航空機（UAV）、無人水中航走体（UUV）関連技術\n* **宇宙・サイバー・電磁波領域における能力:** 衛星システム、サイバーセキュリティ技術、電磁波兵器関連技術\n* **その他基盤的な研究:** 将来の装備体系を見据えた先進的な技術",
    "出典": "1ページ「令和７年度予算の概要」内「１．歳出」の表（予算総額と研究開発費）、2ページ「２．主要経費」内「（１）重点的な整備・強化を行う主要分野」の研究開発に関する記述"
  },
  {
    "質問": "令和7年度の防衛予算において、装備品費等は総額の何パーセントを占めていますか？",
    "模範解答": "令和7年度の防衛予算総額は7兆9,170億円であり、装備品費等は2兆8,354億円です。したがって、装備品費等の割合は、\n\n$$\\frac{28,354 \\text{億円}}{79,170 \\text{億円}} \\times 100 \\approx 35.81 \%$$\n",
    "出典": "1ページ「令和７年度予算の概要」内「１．歳出」の表（予算総額と装備品費等）"
  }
]
```

## 扱うモデル

「google/gemma-2-2b-jpn-it」を使用します。


### 1.2 回答内容の評価方法

- 数値的な評価を見てみます。演習で講師が用いたRagasのAnswer Accuracy的な評価指標を以下のように作成しました。これを利用して数値的な評価を行います。
評価指標の作成にあたっては、Ggeminiに壁打ちを致しました。


```
評価スケール：
    0: 全く不正確。回答の全てまたは主要な部分が誤っており、質問に対して全く的外れな情報を提供している。
    1: ほぼ不正確。回答の大部分が不正確であるか、誤った情報に基づいている。わずかに正しい情報が含まれている程度。
    2: 部分的に正確。回答には正確な情報も含まれているが、重要な誤りや不正確な点、または誤解を招く可能性のある記述が含まれている。
    3: ほぼ正確。回答の大部分は正確であり、質問に対して適切に答えている。しかし、細部にわずかな不正確さ、曖昧さ、または重要でない情報の欠落が見られる場合がある。
    4: 完全に正確。回答は質問に対して適切かつ網羅的に答えており、提示された情報に誤りや不確かな点は一切なく、事実に基づいている。コンテキストとも完全に一致している。
```



- 無料で利用できるので、今回はGeminiAPIを利用してgemini-2.0-flashで評価しました。

### 1.4 ベースモデルの結果
**合計点数:3**

**平均値：0.60**

０点で良さそうなところを１〜２点と評価していてやや甘めですが、許容内の評価だと思います。

ベースモデルではほぼ回答できてないことが確認できました。

# 2 参照文書データの活用

## 2.1 参照文書をテキスト化してそのまま活用 （文書整形なし）

モデルの回答の事実性を向上させるためにRetrieval Augmented Generation (RAG)技術を導入します：

* **知識ソース**: 参照文書をPyPDF2でテキスト化した文書
* **目的**: モデルに参照文書に関する正確な知識と文脈を提供し、事実に基づいた回答を促す

まずは、初期のRAGの実装（ベーシックアプローチ）として参照文書をPyPDF2で出力したテキストをそのまま使用します。文書の分割方法が以下の通りです。

**初期RAG実装（ベーシックアプローチ）**:
* **ドキュメント処理**: 参照文書をPyPDF2で抽出した生テキストをそのまま使用
* **分割方法**: ページ単位でテキストを分割
* **検索手法**: シンプルな類似度ベースの検索でクエリに関連する文を抽出
* **制約条件**: モデルの入力トークン制限に収まるよう関連文のみを選択

文章のベクトル化に使用するSentenceTransfomerは、「Paper&Hucks Vol.32」で井伊講師がご紹介していた日本語センテンストランスフォーマーである「sarashina-embedding-v1-1b」（SB Intuitions開発）を使用してみました。

#### 2.1.1 参照文書のベクトル化
テキスト整形なしの参照文書はページごとにチャンクに分けてJSONファイル化しています。（チャンク分割、JSONファイル化は別ノートブック「PDFtoChank.ipynb」にて実施
### 2.1.4 初期RAGによる回答の生成
では、得られた類似度トップ３をプロンプトに組み込んで回答を生成します。

生成した回答もJSONファイルに保存します。

ファイル名は**raw_rag_response.json**です。
"""
### 2.1.5 初期RAGによる回答結果の評価

1.2で説明したGeminiAPIを利用した評価方法で評価します。
### 2.1.5 テキスト整形なしRAG（初期RAG）の結果

テキストの整形なしでRAGを使いましたが、スコアが向上しませんでした。なぜかは最後に考察します。

合計点数: 3
平均値: 0.60

## 2.2 テキスト整形による品質改善検証（品質改善？RAG)


　DAY３の講義において、データをきれいにすることが重要であるとの言及があったことから、さらなる精度向上を目指して、テキストファイルの整形を試みました。

　手動でテキストをきれいにするのは手間ですので正規表現などを用いて自動で整形しました。（別ファイルPDFtoChank.ipynbにて実施）
整形の方針は、無駄な空白を除く、無駄な改行を除く、必要な改行を入れる。の３点を実施しました。

### 2.2.1 テキスト整形した参照文書のベクトル化


参照文書のテキストの整形を行い、ページごとにチャンクに分けてJSONファイル化しています。（チャンク分割、JSONファイル化は別ノートブック「PDFtoChank.ipynb」にて実施）
これを、整形なしの文書と同様、ベクトル化します。
embeddingのモデルは同様に「sarashina-embedding-v1-1b」
### 2.2.2 質問とドキュメントの類似度の算出

質問は同じものを用いるので、次は類似度の算出です。同様にコサイン類似度で算出し、結果をJSONファイルに書き出します。

保存するファイル名は、**pattern2_similarity.json** です。

### 2.2.4 品質改善？RAGによる回答結果の評価
  **合計点数:7**
**平均値:1.40**

### 2.2.5 テキスト整形ありのRAG（品質改善？RAG）の結果

テキスト整形の結果、少しは品質が改善しました。テキスト整形が効果を挙げた部分もあるようですが、参考情報から読み取れない回答を回答不可とした部分の配点が功を奏しただけであり、だけですので、必ずしもテキスト整形の効果とも言えないと考えられます。  

# 3 全体を通じた考察
GeminiAPIでの評価の結果以下の通りとなりました。

1.   RAGなし

    **合計点数:3**
    **平均値：0.60**
2.   RAGあり（テキスト整形なし）

    **合計点数:3**
    **平均値:0.60**

3.    RAGあり（テキスト整形なし）

    **合計点数:7**
    **平均値:1.40**

　テキスト整形によるノイズの除去のおかげか、RAGあり（テキスト整形あり）において、７年度予算の総額やGDPの記載のないことなど一部は正確に回答できるようになりました。


　一方で、RAGを行っても回答の精度が大幅に向上しなかったのは、参照文献自体の構成の問題かもしれません。防衛省のみならず、政府機関が公表する資料には表やグラフが多用され、PyPDF2による単純なテキスト化では、表やグラフの文脈を表現しきれなかったものと考えられます。

　事実、質問とのコサイン類似度のトップ３のチャンク参照文書には、回答を含む文書が選ばれないケースがありました。例えば、１番目の防衛予算の総額と内訳を問う質問に対し、総額を含む文書が選ばれましたが、内訳に関する文書は正しく選ばれませんでした。これは、元文書においては、内訳の部分が表や円グラフで示されており、この部分が適切にテキスト化されていませんでした。

# 4 さらに改善させるための考察
#### 考察１　表やグラフの読み取り精度を上げる
  　3で考察した通り、表やグラフを文脈にそってテキスト化することがRAGの精度を改善させるために必要であると考えます。これは、講義の中でお話のあったデータの質をあげるというアプローチの１つであり、表がグラフのある文書をRAGの参照文書とする際には避けては通れない事項であると考えます。

  　Geminiに参照文書を読ませて質問、模範回答を生成させた際、グラフや表に含まれる内容も正確に回答していました。マルチモーダルなLLMなどでテキスト化すれば精度の高い表やグラフの読み取りさせるのも、一つの手段となりうると考えられます。

#### 考察２　RAGに用いる文書の選定
  　一方で、テキスト化により文脈が失われる表やグラフを多用した文書ではなく、テキスト中心の文書をRAGの参照文書とするというアプローチもあると思います。

#### 考察３　チャンクの分割の工夫
  　今回は期限が短いこともあり、ページによるチャンク分割を行いましたが、目次の項目ごとにチャンクを分けることにより、必要なチャンクをLLMに渡すことができ精度が向上するものと考えられます。
  　その検証として、目次から抽出した目次の項目ごとにチャンクを分割することを自動化し、その上でRAGの参考文書にすることを試みたのですが、チャンク分割が自動でうまく分けられずに諦めました。
  精度を見る上では手動で印をつけてチャンク分けすることも可能ですので、機会があればこの方法も試したいと思います。


