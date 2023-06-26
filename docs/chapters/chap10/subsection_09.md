[メインページ](../../index.markdown)

[章目次](./chap10.md)
## 10.9. 参考文献

関係抽出タスクを進めるために，グラフニューラルネットワークの他に9.3節で紹介したGraph-LSTMアルゴリズムも採用されている(Miwa and Bansal, 2016; Song *et al*., 2018c)． また，グラフニューラルネットワークは，暴言の検出(Mishra *et al*., 2019)，ニューラル要約(Fernandes *et al*., 2018)，テキスト分類(Vaswani *et al*., 2017)など，他の多くのNLPタスクにも適用されている．

Transformer (Vaswani *et al*., 2017)は自然言語処理における系列データを取り扱うために広く採用されている． 中でも，このTransformerを基に構築された事前学習モデルであるBERT (Devlin *et al*., 2018)は，多くのNLPタスクの性能を大幅に向上させている． 特定の系列データにTransformerを適用する際には，このTransformerを特殊なグラフニューラルネットワークとみなすことができる． 具体的には，入力系列から生成される完全グラフ（全結合）上でTransformerが作用する． その際，系列内の各要素はノードとして扱われる． さらに，Transformerセルフ・アテンション層は，GATフィルタ層に相当している(GATフィルタの詳細については5.3.2節を参照)．


$$
 \symscr{W} = [\text{The}, \text{quick}, \text{brown}, \text{fox}, \text{jumps}, \text{over}, \text{the}, \text{lazy}, \text{dog}] $$


 のとき，主語のエンティティと目的語のエンティティは以下のようになる： 

$$
 \symscr{W}_{\text{s}}=[\text{The}, \text{quick}, \text{brown}, \text{fox}],\qquad\symscr{W}_{\text{o}}=[\text{the}, \text{lazy}, \text{dog}] $$


 




$$

\begin{aligned}
    \symbf{F}^{(L)}\in\mathbb{R}^{d\times n}&\xrightarrow{\text{最大プーリング}}\symbf{F}_{\text{sent}}\in\mathbb{R}^{d}\\\symbf{F}^{(L)}[s1\,:\,s2]\in\mathbb{R}^{d\times \text{length_s}}&\xrightarrow{\text{最大プーリング}}\symbf{F}_s\in\mathbb{R}^{d}\qquad(\text{length_s} = s2 - s1 + 1)\\\symbf{F}^{(L)}[o1\,:\,o2]\in\mathbb{R}^{d\times\text{length_o}}&\xrightarrow{\text{最大プーリング}}\symbf{F}_o\in\mathbb{R}^{d}\qquad(\text{length_o} = o2 - o1 + 1)
\end{aligned}
$$

 

[メインページ](../../index.markdown)

[章目次](./chap10.md)

[前の節へ](./subsection_08.md) [次の節へ](./subsection_10.md)

[^1]: 訳注：例えば， 
[^2]: 訳注：文章中に含まれる単語の数を $n$ ，単語表現が持つ次元を $d$ とする．このとき，最大プーリング操作によって次元は以下のようになる： 
