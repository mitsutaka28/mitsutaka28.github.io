[メインページ](../../index.markdown)

[章目次](./chap10.md)
## 10.9. 参考文献

関係抽出タスクを進めるために，グラフニューラルネットワークの他に9.3節で紹介したGraph-LSTMアルゴリズムも採用されている(Miwa and Bansal, 2016; Song *et al*., 2018c)． また，グラフニューラルネットワークは，暴言の検出(Mishra *et al*., 2019)，ニューラル要約(Fernandes *et al*., 2018)，テキスト分類(Vaswani *et al*., 2017)など，他の多くのNLPタスクにも適用されている．

Transformer (Vaswani *et al*., 2017)は自然言語処理における系列データを取り扱うために広く採用されている． なかでも，このTransformerを基に構築された事前学習モデルであるBERT (Devlin *et al*., 2018)は，多くのNLPタスクの性能を大幅に向上させている． 特定の系列データにTransformerを適用する際には，このTransformerを特殊なグラフニューラルネットワークとみなすことができる． 具体的には，入力系列から生成される完全グラフ（全結合）上でTransformerが作用する． その際，系列内の各要素はノードとして扱われる． さらに，Transformerセルフ・アテンション層は，GATフィルタ層に相当している(GATフィルタの詳細については5.3.2節を参照)．

[メインページ](../../index.markdown)

[章目次](./chap10.md)

[前の節へ](./subsection_08.md) [次の節へ](./subsection_10.md)


