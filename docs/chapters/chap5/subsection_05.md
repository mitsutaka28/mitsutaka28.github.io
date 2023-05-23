[メインページ](../../index.markdown)

[章目次](./chap5.md)
## 5.5. GNNの学習

この節では，ノード分類とグラフ分類を下流タスクの例として取り上げ，GNNでどのようにパラメータを学習するかについて説明する． なお，ノード分類とグラフ分類については，それぞれ定義2.42と定義2.46で定式化している．

### ノード分類における学習

定義2.42で導入したように，グラフのノード集合 $\mathcal{V}$ は，ラベル付きの $\mathcal{V}\_l$ とラベルなしの $\mathcal{V}\_u$ の互いに素な2つの部分集合に分けることができる． ノード分類タスクにおける目標は，ラベル付きのノード集合 $\mathcal{V}\_l$ に基づいてモデルを学習し， $\mathcal{V}\_u$ 内のラベルなしのノードが持つラベルを予測することである， GNNモデルは通常，グラフ全体を入力としてノード表現を生成し，このノード表現を用いてノード分類器を学習する． 具体的には，いくつかのグラフフィルタ層から成るGNNモデルを $\mathrm{GNN}_{\text{node}}(\cdot,\,\cdot)$ と表すことにする(5.2.1節参照)．  $\mathrm{GNN}_{\text{node}}(\cdot,\,\cdot)$ 関数はグラフ構造とノードの特徴量を入力とし，ノードの特徴を次のように更新する：

 $$ \symbf{F}^{(\text{out})}=\mathrm{GNN}_{\text {node }}\left(\symbf{A}, \symbf{F} ;\, \boldsymbol{\Theta}_1\right)
    
\tag{5.46} $$ 

ここで， $\boldsymbol{\Theta}_1$ はモデルパラメータで，  $\symbf{A} \in \mathbb{R}^{N \times N}$ は隣接行列，  $\symbf{F} \in \mathbb{R}^{N \times d_{\text{in}}}$ は元のグラフの入力特徴量，  $\symbf{F}^{\text{(out)}} \in \mathbb{R}^{N \times d_{\mathrm{out}}}$ は生成された出力特徴量である． そして，ノードの出力特徴量を用いて次のようにノード分類を行う：

 $$ \symbf{Z}=\operatorname{softmax}\left(\symbf{F}^{(\text{out})} \boldsymbol{\Theta}_2\right)
    
\tag{5.47} $$ 

ここで， $\symbf{Z} \in \mathbb{R}^{N \times C}$ は各ノード(行)の各クラス(列)への所属確率であり， $\boldsymbol{\Theta}_2 \in \mathbb{R}^{d_{\mathrm{out}} \times C}$ は特徴量 $\symbf{F}^{(\text{out})}$ をクラス $C$ の次元に変換するパラメータ行列である．  $\symbf{Z}$ の $i$ 番目の行はノード $v_i$ についての予測ラベルを表す． 予測ラベルは通常，最も大きな確率を持つラベルである． ノード分類の全プロセスは，以下のようにまとめることができます：

 $$ \symbf{Z}=f_{\mathrm{GNN}}(\symbf{A},\, \symbf{F} ;\, \boldsymbol{\Theta})
    
\tag{5.48} $$ 

ここで， $f_{\text{GNN}}$ は式(5.46)と式(5.47)から成り，  $\boldsymbol{\Theta}$ は $\boldsymbol{\Theta}_1$ と $\boldsymbol{\Theta}_2$ を含む． 式(5.48)の $\boldsymbol{\Theta}$ は次の量を最小化することで学習することができる．

 $$ \mathcal{L}_{\text {train }}=\sum_{v_i \in \mathcal{V}_l} \ell\left(f_{G N N}(\symbf{A}, \symbf{F};\, \boldsymbol{\Theta})_i,\, y_i\right)
    
\tag{5.49} $$ 

ここで， $f_{\mathrm{GNN}}(\symbf{A},\, \symbf{F} ; \boldsymbol{\Theta})_i$ は出力の $i$ 行目を表す． すなわち，ノード $v_i$ の各クラスへの所属確率である． また， $y_i$ は $v_i$ が持つラベルであり， $\ell(\cdot, \cdot)$ はクロスエントロピーなどの損失関数である．

### グラフ分類における学習

定義2.46で導入したように，グラフ分類タスクでは，各グラフはラベルを持つサンプルとして扱われる． 学習データは， $y_i$ をグラフ $\mathcal{G}$ に対応するラベルであるとすると， $\mathcal{D}=\left\\{\mathcal{G}\_i, y_i\right\\}$ と表される． グラフ分類タスクの目標は，学習データ $\mathcal{D}$ に基づいてモデルを学習し，ラベルが未知のグラフが持つであろうラベルを適切に予測を行うことである． このタスクにおけるグラフニューラルネットワークモデルは通常，入力グラフを特徴量表現に変換するような特徴量抽出器として利用される：

 $$ \symbf{f}_{\mathcal{G}}=\mathrm{GNN}_{\mathrm{graph}}\left(\mathcal{G} ; \boldsymbol{\Theta}_1\right)
    
\tag{5.50} $$ 

ここで， $\mathrm{GNN}_{\mathrm{graph}}$ はグラフ全体の表現を学習するグラフニューラルネットワークのモデルであり，グラフフィルタ層とグラフプーリング層で構成することが多い． また， $\symbf{f}_G \in\mathbb{R}^{1 \times d_{\text{out}}}$ は生成されたグラフ全体の表現であり，これを用いてグラフ分類は次のように行われる．

 $$ \symbf{z}_{\mathcal{G}}=\operatorname{softmax}\left(\symbf{f}_{\mathcal{G}}\, \boldsymbol{\Theta}_2\right)
    
\tag{5.51} $$ 

ここで， $\boldsymbol{\Theta}_2 \in \mathbb{R}^{d_{\mathrm{out}} \times C}$ はグラフ表現を分類クラス $C$ の次元に変換するパラメータ行列である． また， $\symbf{Z}_{\mathcal{G}} \in \mathbb{R}^{1 \times C}$ は入力グラフ $\mathcal{G}$ の各クラスへの所属確率を表す． グラフ分類の全プロセスは，以下のようにまとめることができる：

 $$ \symbf{z}_{\mathcal{G}}=f_{\mathrm{GNN}}(\mathcal{G} ; \boldsymbol{\Theta})
    
\tag{5.52} $$ 

ここで， $f_{\mathrm{GNN}}$ は式(5.50)と式(5.51)からなり，  $\boldsymbol{\Theta}$ は $\boldsymbol{\Theta}_1$ と $\boldsymbol{\Theta}_2$ を含む． パラメータ $\boldsymbol{\Theta}$ は次の量を最小化することで学習することができる．

 

$$ \mathcal{L}_{\text {train}}=\sum_{\mathcal{G}_i \in \mathcal{D}} \ell\left(f_{\mathrm{GNN}}\left(\mathcal{G}_i, \boldsymbol{\Theta}\right), y_i\right) \nonumber $$

 

ここで， $y_i$ はグラフ $\mathcal{G}\_i$ が持つラベルであり， $\ell(\cdot,\,\cdot)$ は損失関数である．


[メインページ](../../index.markdown)

[章目次](./chap5.md)

[前の節へ](./subsection_04.md) [次の節へ](./subsection_06.md)


