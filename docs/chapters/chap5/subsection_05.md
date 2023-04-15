[メインページ](../../index.markdown)

[章目次](./chap5.md)
## 5.5. GNNの学習

この節では，ノード分類とグラフ分類を後段タスクの例として取り上げ，GNNでどのようにパラメータを学習するかについて説明する．
ノード分類とグラフ分類については定義2.42と定義2.46でそれぞれ定式化している．

### ノード分類における学習

定義2.42で導入したように，あるグラフのノードの集合 $\mathcal{V}$ は２つの重なりのない集合に分けることができる．
 $\mathcal{V}_l$ はラベル付きのノード， $\mathcal{V}_u$ はラベルなしのノードである．
ノード分類の目標はラベル付きのノード $\mathcal{V}_l$ からラベルなしのノード $\mathcal{V}_u$ のラベルを予測するモデルを学習することである．
GNNモデルはグラフ全体を入力としてノード表現を生成し，このノード表現がノード分類器の学習に使われることが多い．
具体的には，いくつかのグラフフィルター層からなるGNNモデルを $G N N_{\text {node }}(,)$ と表すことにする(5.2.1節)．
 $G N N_{\text {node }}(,)$ 関数はグラフ構造とノードの特徴を入力に，ノードの特徴を次のように更新する:

 $$ \mathbf{F}^{(\text {out })}=G N N_{\text {node }}\left\(\mathbf{A}, \mathbf{F} ; \boldsymbol{\Theta}\_1\right\)
    
\tag{5.46} $$ 

ここで， $\boldsymbol{\Theta}\_1$ はモデルパラメータで,
 $\mathbf{A} \in \mathbb{R}^{N \times N}$ は隣接行列,
 $\mathbf{F} \in \mathbb{R}^{N \times d_{\text {in }}}$ は元のグラフの入力特徴量,
 $\mathbf{F}^{\text {(out) }} \in \mathbb{R}^{N \times d_{\mathrm{out}}}$ は生成した出力グラフの特徴量である．
そして，出力ノードの特徴を用いて次のようにノード分類を行う:

 $$ \mathbf{Z}=\operatorname{softmax}\left\(\mathbf{F}^{(\text {out })} \boldsymbol{\Theta}\_2\right\)
    
\tag{5.47} $$ 

ここで， $\mathbf{Z} \in \mathbb{R}^{N \times C}$ は各ノードについての0から1の値をとる出力確率であり， $\boldsymbol{\Theta}\_2 \in \mathbb{R}^{d_{\mathrm{out}} \times C}$ は特徴量 $\mathbf{F}_{\text {out }}$ をクラス $C$ の次元に変換するパラメータ行列である．
 $\mathbf{Z}$ の $i$ 番目の行はノード $v_i$ についての予測ラベルを表す．
予測ラベルは通常，最も大きな確率を持つラベルである． 以上をまとめると,

 $$ \mathbf{Z}=f_{G N N}(\mathbf{A}, \mathbf{F} ; \boldsymbol{\Theta})
    
\tag{5.48} $$ 

ここで， $f_{G N N}$ は式(5.47)と式(5.48)からなり,
 $\boldsymbol{\Theta}$ は $\boldsymbol{\Theta}_1$ と $\boldsymbol{\Theta}_2$ を含む．
式(5.48)の $\boldsymbol{\Theta}$ は次の量を最小化することで学習することができる．

 $$ \mathcal{L}_{\text {train }}=\sum_{v\_i \in \mathcal{V}\_l} \ell\left\(f_{G N N}(\mathbf{A}, \mathbf{F} ; \boldsymbol{\Theta})\_i, y\_i\right\)
    
\tag{5.49} $$ 

ここで， $f_{G N N}(\mathbf{A}, \mathbf{F} ; \boldsymbol{\Theta})\_i, y\_i$ は出力の $i$ 行目を表す．
すなわち，ノード $v_i$ の出力確率である．
 $y_i$ は対応するラベルであり， $\ell(\cdot, \cdot)$ はクロスエントロピーなどの損失関数である．

### グラフ分類における学習

定義2.46で導入したように，グラフ分類問題では，各グラフがラベルづけされたサンプルとして扱われる．
学習データは $\mathcal{D}=\left\\{\mathcal{G}\_i, y\_i\right\\}$ と書くことができる．
 $y_i$ はグラフ $\mathcal{G}$ に対応するラベルである．
グラフ分類問題では，学習データ $\mathcal{D}$ についてモデルを学習し，ラベルづけされていないグラフについて正しい予測をすることを目指す．
グラフニューラルネットワークのモデルは通常，特徴量のエンコーダーとして用いられることが多く,
次のように入力グラフを特徴量表現に変換する:

 $$ \mathbf{f}_{\mathcal{G}}=G N N_{\mathrm{graph}}\left\(\mathcal{G} ; \boldsymbol{\Theta}\_1\right\)
    
\tag{5.50} $$ 

ここで， $G N N_{\mathrm{graph}}$ はグラフレベルでの表現を学習するグラフニューラルネットワークのモデルであり，グラフフィルター層とグラフプーリング層で構成されることが多い．
 $\mathbf{f}\_G \in\mathbb{R}^{1 \times d_{\text {out }}}$ は生成されたグラフレベルでの表現であり，これを用いてグラフ分類を次のように行う．

 $$ \mathbf{z}_{\mathcal{G}}=\operatorname{softmax}\left\(\mathbf{f}_{\mathcal{G}} \boldsymbol{\Theta}\_2\right\)
    
\tag{5.51} $$ 

ここで, $\boldsymbol{\Theta}\_2 \in \mathbb{R}^{d_{\mathrm{out}} \times C}$ はグラフ表現を分類クラス $C$ の次元に変換する．
 $\mathbf{Z}_{\mathcal{G}} \in \mathbb{R}^{1 \times C}$ は入力グラフ $\mathcal{G}$ に対する予測確率を表す．
以上をまとめると次のようになる:

 $$ \mathbf{z}_{\mathcal{G}}=f_{G N N}(\mathcal{G} ; \boldsymbol{\Theta})
    
\tag{5.52} $$ 

ここで， $f_{G N N}$ は式(5.50)と式(5.51)からなり,
 $\boldsymbol{\Theta}$ は $\boldsymbol{\Theta}_1$ と $\boldsymbol{\Theta}_2$ を含む．
パラメータ $\boldsymbol{\Theta}$ は次の量を最小化することで学習することができる．

 

$$ \mathcal{L}_{\text {train }}=\sum_{\mathcal{G}\_i \in \mathcal{D}} \ell\left\(f_{G N N}\left\(\mathcal{G}\_i, \boldsymbol{\Theta}\right\), y\_i\right\) \nonumber $$

 

 $y_i$ はグラフ $\mathcal{G}_i$ に対応するラベルであり， $\ell(\cdot, \cdot)$ は損失関数である．


[メインページ](../../index.markdown)

[章目次](./chap5.md)

[前の節へ](./subsection_04.md) [次の節へ](./subsection_06.md)


