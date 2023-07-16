[メインページ](../../index.markdown)

[章目次](./chap10.md)
## 10.6. Graph-to-Sequence学習

**Sequence-to-Sequenceモデル**は，ニューラル機械翻訳(NMT) (Bahdanau ., 2014)や自然言語生成(NLG) (Song *et al*., 2017)などの自然言語処理タスクに広く適用されている． これらのモデルのほとんどは**エンコーダ・デコーダモデル**として見ることができる． エンコーダ・デコーダモデルでは，エンコーダが（単語系列などの）トークン系列を入力として受け取り，それを連続的なベクトル表現の系列に変換する． その後，デコーダはエンコードされたベクトル表現を入力とし，そこから目的に沿った新しい系列を出力する． 通常，エンコーダとデコーダには，リカレントニューラルネットワーク(RNN)やその派生形が用いられる．

一方，自然言語はグラフで表現できるため，**Graph-to-Sequenceモデル**が登場し，このモデルがニューラル機械翻訳(NMT) (Marcheggiani *et al*., 2018; Beck *et al*., 2018)やAMR-to-text (Cohen, 2019; Song *et al*., 2018b)などのNLPタスクを解決するために利用されている． これらのGraph-to-Sequenceモデルは，通常，エンコーダ（またはその一部）としてグラフニューラルネットワークを利用し，デコーダとしてはそのままRNNとその派生形を採用している． 具体的には，式(10.1)で説明したグラフニューラルネットワークモデル(Marcheggiani and Titov, 2017)が，ニューラル機械翻訳とAMR-to-textタスクにおけるエンコーダとして利用される(Marcheggiani *et al*., 2018; Song *et al*., 2018b; Cohen, 2019)．

Graph-to-Sequence学習のための一般的なエンコーダ・デコーダとして，**Graph2Seq**というフレームワークがXu *et al*.(2018b)において提案されている． このフレームワークは，グラフニューラルネットワークモデルをエンコーダとして，そしてアテンション機構付きのRNNモデルをデコーダとして利用しているのが特徴である． 本設ではこのフレームワークについて詳しく解説する． まず，GNNを基にしたエンコーダモデルについて説明し，次にデコーダについて簡潔に述べていく．

#### Graph2Seqフレームワークにおけるエンコーダおよびデコーダ

NLP分野で扱うほとんどのグラフ（AMRや構文依存木など）は有効グラフである． したがって，Graph2SeqのGNNベースのエンコーダは，情報を集約する際，入近傍ノードと出近傍ノードを区別するよう設計されている． つまり，ノード $v_i$ の近傍ノードは，2つの集合，すなわち入近傍 $\symscr{N}\_{\text{in}}(v_i)$ と出近傍 $\symscr{N}\_{\text{out}}(v_i)$ に分割される． GraphSAGEフィルタの集約操作（詳細は5.3.2節におけるGraphSAGEフィルタを参照）がノード表現の集約と更新に使用される． 具体的には，各ノードに対して2つのノード表現，すなわち，入近傍の表現と出近傍の表現を持ち続ける．  $l$ 層目でのノード $v_i$ の更新過程は次のように表現できる：  

$$

\begin{aligned}
&\symbf{F}^{(l)}_{\symscr{N}_{\text{in}}(v_i)} = \operatorname{AGGREGATE}(\left\{\symbf{F}^{(l-1)}_{\text{out}}(v_j), \forall v_j\in\symscr{N}_{\text{in}}(v_i)\right\})\\
&\symbf{F}^{(l)}_{\text{in}}(v_i) = \sigma\left([\symbf{F}^{(l-1)}_{\text{in}}(v_i),\symbf{F}^{(l)}_{\symscr{N}_{\text{in}}(v_i)}]\symbf{\Theta}^{(l-1)}_{\text{in}}\right)\\
&\symbf{F}^{(l)}_{\symscr{N}_{\text{out}}(v_i)} = \operatorname{AGGREGATE}(\left\{\symbf{F}^{(l-1)}_{\text{in}}(v_j), \forall v_j\in\symscr{N}_{\text{out}}(v_i)\right\})\\
&\symbf{F}^{(l)}_{\text{out}}(v_i) = \sigma\left([\symbf{F}^{(l-1)}_{\text{out}}(v_i),\symbf{F}^{(l)}_{\symscr{N}_{\text{out}}(v_i)}]\symbf{\Theta}^{(l-1)}_{\text{out}}\right)
\end{aligned}
$$

  ここで， $\symbf{F}^{(l)}\_{\text{in}}(v_i)$ と $\symbf{F}^{(l)}\_{\text{out}}(v_i)$ は， $l$ 層経由したノード $v_i$ の入近傍表現と出近傍表現を表す． 5.3.2節のGraphSAGEフィルタで紹介したように， $\operatorname{AGGREGATE}(\cdot)$ 関数には様々な設計が適用できる．  $L$ 層目のグラフフィルタリング層から出力された最終的な入近傍表現と出近傍表現は，それぞれ $\symbf{F}^{(L)}\_{\text{in}}(v_i)$ と $\symbf{F}^{(L)}\_{\text{out}}(v_i)$ として表される． これら2種類の表現は連結(concat)され，両方からの情報を含む最終的な表現が生成される：  

$$
 \symbf{F}^{(L)}(v_i) = \left[\symbf{F}^{(L)}_{\text{in}}(v_i),\,\symbf{F}^{(L)}_{\text{out}}(v_i)\right] $$


 

上記のノード表現を取得した後，プーリング方法を使用してグラフ表現も生成する． これはデコーダの初期化に使用される．プーリング操作は以下のように表現される：  

$$
 \symbf{F}_{\text{G}} = \operatorname{Pool}\left(\left\{\symbf{F}^{(L)}(v_i),\,\forall v_i\in \symscr{V}\right\}\right) $$


  ここで，最大プーリングや平均プーリングなど，様々なプーリング手法が採用できる．

Graph2Seqのデコーダはアテンション機構付きのRNNでモデル化される． これは系列の各トークンを生成する際，すべてのノード表現を考慮に入れたアテンション機構を持ったモデルである． なお，グラフ表現 $\symbf{F}\_G$ はRNNデコーダの初期状態として利用される．


[メインページ](../../index.markdown)

[章目次](./chap10.md)

[前の節へ](./subsection_05.md) [次の節へ](./subsection_07.md)


