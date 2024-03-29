[メインページ](../../index.markdown)

[章目次](./chap9.md)
## 9.1. はじめに

従来の深層学習モデルには，畳み込みニューラルネットワーク（CNN）, リカレントニューラルネットワーク（RNN）, ディープオートエンコーダー, 敵対的生成ネットワーク（GAN）など多くのモデルがある． これらのモデルによって様々な種類のデータに対応できる． 例えば，CNNは画像のような規則的な格子状のデータを処理することができ，RNNはテキストのような連続したデータを処理することができる． さらに，異なる設定にも対応できるよう設計されている． 例えば，優れたCNNやRNNを学習させるためには，大量のラベル付きデータが必要だが（教師あり学習）, オートエンコーダーやGANはラベルのないデータのみでも複雑なパターンを抽出することができる（教師なし学習）． これらの異なるアーキテクチャにより，深層学習技術をコンピュータビジョン，自然言語処理、データマイニング，情報検索など多くの分野に応用することができる． これまでの章では，単純グラフや複雑グラフを対象とした様々なグラフニューラルネットワーク（GNN）を紹介してきた． しかし，これらのモデルはノードの分類やグラフの分類といった特定のタスクに対してのみ開発されており，学習にラベル付きデータを必要とすることが多い． そこで，グラフ構造を持つデータに対して、より深いアーキテクチャを採用する試みがなされてきた． オートエンコーダーは，グラフデータにおけるノード表現の学習に適用されてきた(Wang et al., 2016; Kipf and Welling, 2016b; Pan et al., 2018)． 変分オートエンコーダーや敵対的生成ネットワークなどの深層生成モデルも、ノード表現の学習（Kipf and Welling, 2016b; Pan et al., 2018; Wang et al., 2018a）およびグラフ生成（Simonovsky and Komodakis, 2018; De Cao and Kipf, 2018）に適用されてきた． これらの深層グラフモデルは，GNNがカバーできない様々な設定下でのより幅広いグラフタスクに対応でき，グラフの深層学習技術を大きく発展させてきた． 本章では，深層オートエンコーダー, 変分オートエンコーダー, リカレントニューラルネットワーク，敵対的生成ネットワークを含む，グラフに関するより多くの深層モデルを取り上げる．


[メインページ](../../index.markdown)

[章目次](./chap9.md)

[次の節へ](./subsection_02.md)


