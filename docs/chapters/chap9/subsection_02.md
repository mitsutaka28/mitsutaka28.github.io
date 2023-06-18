[メインページ](../../index.markdown)

[章目次](./chap9.md)
## 9.2. グラフ上でのオートエンコーダー

で紹介したオートエンコーダーは, 入力データに対して圧縮された低次元表現を得るための教師なし学習モデルとみなすことができる. オートエンコーダーは低次元ノード表現の学習に採用されている(Wang et al., 2016; Kipf and Welling, 2016b; Pan et al., 2018). (Wang et al., 2016)では, 各ノードの近傍の情報が入力として利用され, 再構成されるので, 学習された低次元表現はノードの構造情報を保持することができる. で紹介したように, エンコーダーとデコーダーはいずれも順伝搬ニューラルネットワークでモデル化されている. (Kipf and Welling, 2016b; Pan et al., 2018)では, ノードを低次元表現に符号化するエンコーダとして, 入力ノードの特徴とグラフ構造の両方を利用するグラフニューラルネットワークモデルが採用されている. そして, これらの符号化されたノード表現を用いてグラフの構造情報が再構成される.

以下では, 低次元ノード表現を学習するためのこれら2種類のグラフオートエンコーダーについて簡単に紹介する.

(Wang et al., 2016)では, 各ノード $v_i \in \mathcal{V}$ について, グラフの隣接行列 $\mathbf{a}\_i=\mathbf{A}\_i$ の対応する行がエンコーダーの入力となり, その低次元表現を取得する方法が紹介されている:

 $$
 \mathbf{z}_i=f_{e n c}\left(\mathbf{a}_i ; \boldsymbol{\Theta}_{e n c}\right)
    \nonumber $$
 

ここで,  $f\_{e n c}$ はエンコーダーであり,  $\boldsymbol{\Theta}\_{e n c}$ でパラメータ化される順伝搬ニューラルネットワークでモデル化される. 次に,  $\mathbf{z}\_i$ がデコーダーの入力として使われ,  $\mathbf{a}\_i$ を再構成する:

 $$
 \tilde{\mathbf{a}}_i=f_{d e c}\left(\mathbf{z}_i ; \boldsymbol{\Theta}_{d e c}\right)
    \nonumber $$
 

ここで,  $f\_{d e c}$ はデコーダーで,  $\boldsymbol{\Theta}\_{d e c}$ はパラメータを表す. 再構成損失は,  $\mathcal{V}$ 内のすべてのノードについて $\tilde{\mathbf{a}}\_i$ が $\mathbf{a}\_i$ に近いという制約で定義することができる:

 $$
 \mathcal{L}_{e n c}=\sum_{v_i \in \mathcal{V}}\left\|\mathbf{a}_i-\tilde{\mathbf{a}}_i\right\|_2^{2}
    \nonumber $$
 

上記の再構成損失を最小化することで, 近傍情報を低次元表現 $\mathbf{z}\_i$ に「圧縮」することができる. 近傍ノードとのペアワイズ類似度は, 明示的には捉えられない. しかし, オートエンコーダー（のパラメータ）は全ノードで共有されているため, エンコーダーは類似の入力を持つノードを似ているノード表現に対応付けると考えられ, 暗黙のうちに類似性が保持される. 隣接行列 $\mathbf{A}$ 固有のスパース性があるため, 上記の再構成損失が問題となる場合がある.  $\mathbf{a}\_i$ の要素の大部分は0であるため, 最適化処理では0の要素の再構成にオーバーフィットしやすくなる可能性がある. この問題を解決するために, 再構成損失を次のように変更することで, 0でない要素の再構成誤差に対してより多くのペナルティを課す:

 $$
 \mathcal{L}_{e n c}=\sum_{v_i \in \mathcal{V}}\left\|\left(\mathbf{a}_i-\tilde{\mathbf{a}}_i\right) \odot \mathbf{b}_i\right\|_2^{2}
    \nonumber $$
 

ここで,  $\odot$ はアダマール積を表し,  $\mathbf{b}\_i=\left\\{b_{i, j}\right\\}\_{j=1}^{\|\mathcal{V}\|}$ は $A_{i, j}=0$ のときに $b_{i,j}=1$ で,  $A_{i, j} \neq 0$ のときに $b_{i, j}=\beta>1$ である.  $\beta$ は学習されるハイパーパラメータである. さらに直接的に, つながっているノードが似ている低次元表現をもつようにするため, 正則化損失を導入する:

 $$
 \mathcal{L}_{c o n}=\sum_{v_i, v_j \in \mathcal{V}} \mathbf{A}_{i, j} \cdot\left\|\mathbf{z}_i-\mathbf{z}_j\right\|_2^{2}
    \nonumber $$
 

最後に, エンコーダーとデコーダーのパラメータに関する正則化損失も目的関数に含め, 最終的には以下のような損失を最小化することになる:

 $$
 \mathcal{L}=\mathcal{L}_{e n c}+\lambda \cdot \mathcal{L}_{c o n}+\eta \cdot \mathcal{L}_{r e g}
    \nonumber $$
 

ここで,  $\mathcal{L}\_{r e g}$ はパラメータの正則化に対応し, 次で表される:

 

$$
 \mathcal{L}_{r e g}=\left\|\boldsymbol{\Theta}_{e n c}\right\|_2^{2}+\left\|\boldsymbol{\Theta}_{d e c}\right\|_2^{2} $$


 

上述したグラフオートエンコーダーモデルは, グラフ構造のみを利用することができ, ノードの特徴を取り込むことができない. (Kipf and Welling, 2016b）では, グラフ構造の情報とノードの特徴の両方を活用するグラフニューラルネットワークモデルがエンコーダーとして採用されている. 具体的には, エンコーダは以下のようにモデル化される:

 $$
 \mathbf{Z}=f_{G N N}\left(\mathbf{A}, \mathbf{X} ; \boldsymbol{\Theta}_{G N N}\right)
    \nonumber $$
 

ここで,  $f\_{G N N}$ はエンコーダーを表し, グラフニューラルネットワークモデルである. (Kipf and Welling, 2016b）では, GCNフィルターを使ってエンコーダーを構成している. デコーダーはグラフを再構成するが, 隣接行列 $\mathbf{A}$ と属性行列 $\mathbf{X}$ を含む. (Kipf and Welling, 2016b）では, 隣接行列 $\mathbf{A}$ のみを再構成の対象としている. 具体的には, 隣接行列はエンコードされた表現 $\mathbf{Z}$ から次のように再構成される:

 $$
 \hat{\mathbf{A}}=\sigma\left(\mathbf{Z} \mathbf{Z}^{T}\right)
    \nonumber $$
 

ここで,  $\sigma(\cdot)$ はシグモイド関数である. 低次元表現 $\mathbf{Z}$ は $\hat{\mathbf{A}}$ と $\mathbf{A}$ の再構成誤差を最小化することで得られる. 目的関数は次のようにモデル化することができる:

 $$
 -\sum_{v_i, v_j \in \mathcal{V}}\left(\mathbf{A}_{i, j} \log \hat{\mathbf{A}}_{i, j}+\left(1-\mathbf{A}_{i, j}\right) \log \left(1-\hat{\mathbf{A}}_{i, j}\right)\right)
    \nonumber $$
 

これは $\hat{\mathbf{A}}$ と $\mathbf{A}$ の間のクロスエントロピーとみなすことができる.


[メインページ](../../index.markdown)

[章目次](./chap9.md)

[前の節へ](./subsection_01.md) [次の節へ](./subsection_03.md)


