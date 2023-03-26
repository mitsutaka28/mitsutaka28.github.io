[メインページ](../../index.markdown)

[章目次](./chap2.md)
## 2.2. グラフの表現

本節では，グラフの定義を紹介していく．ここではエッジの重みが付与されていない"単純グラフ"(simple
graph)に焦点を当て，より複雑な"複合グラフ"(complex
graph)については本章の後半で見ていくことにする． 
<div class="definition">


<strong>定義 2.1 グラフ</strong>

グラフは $\symbfscr{G} = \left\{\symbfscr{V},\,\symbfscr{E}\right\}$ と表記される．ここで， $\symbfscr{V}=\left\{v\_1,\dots,v\_N\right\}$ は $N=|\symbfscr{V}|$ 個のノードで構成される集合で， $\symbfscr{E}=\left\{e\_1,\dots,e\_M\right\}$ は $M$ 個のエッジで構成される集合である．

</div>

ノードはグラフには欠かせない要素である．実際，ソーシャルグラフではユーザをノードとみなし，化合物グラフでは原子をノードとして扱っている．グラフ $\symbfscr{G}$ の大きさは，そのグラフが持つノードの個数，つまり $N=|\symbfscr{V}|$ と定義される．エッジ集合 $\symbfscr{E}$ は，ノード間の接続を記述している．あるエッジ $e\_i\in\symbfscr{E}$ は， $2$ つのノード $v^{1}\_e,\,v^{2}\_e$ をつなぐので，
 $(v^{1}\_e,\,v^{2}\_e)$ と表現することも可能である．ただし有向グラフでは，この表現は「エッジはノード $v^{1}\_e$ からノード $v^{2}\_e$ を向いている」ことを意味することになる．無向グラフでは， $e=(v^{1}\_e,v^{2}\_e)=(v^{2}\_e,v^{1}\_e)$ となるから $2$ つのノードの順序に違いはなくなる．本章では，特に言及しない限りは無向グラフに限定して話を進めるので，ノード $v^{1}\_e$ と $v^{2}\_e$ は単に「エッジ $e$ で接続される」と考えて良い．あるノード $v\_i$ と他のノード $v\_j$ の間にエッジが存在している場合，「 $v\_i$ と $v\_j$ は隣接している」という．
ソーシャルグラフにおける"友人関係"はノードをつなぐエッジとしてみることができ，化合物グラフでは"化学結合"をエッジとみなすことが可能である（ここでは，化学結合の種類の違いは無視して，あらゆる化学結合を一つのエッジとしている）．

グラフ $\symbfscr{G}=\left\{\symbfscr{V}, \symbfscr{E}\right\}$ は，ノード間の接続状況を表す隣接行列を使うことによっても同等に表現できる．

<div class="definition">
 
<strong>定義 2.2 隣接行列</strong>

あるグラフ $\sgraph$ に対応する隣接行列を $\symbf{A}\in \left\{0,1\right\}^{N\times N}$ と表す． $\symbf{A}$ の $i$ 行 $j$ 列要素である $\symbf{A}_{i,j}$ は， $2$ つのノード $v\_i$ と $v\_j$ の接続状況を表している．具体的には， $v\_i$ が $v\_j$ に隣接している場合には $\symbf{A}_{i,j}=1$ ，隣接していない場合には $\symbf{A}_{i,j}=0$ とする．

</div>

無向グラフにおいては，ノード $v\_i$ が $v\_j$ と隣接していることと，ノード $v\_j$ が $v\_j$ と隣接していることは区別しないから，グラフ内の任意の $v\_i$ と $v\_j$ に対して， $\symbf{A}_{i,j} = \symbf{A}_{j,i}$ が成り立つ．したがって，無向グラフに対応する隣接行列は対称行列である．

![ノード数5，エッジ数6のグラフ](./fig/fig2_1.pdf){width="0.5\\linewidth"}


<div class="eg">
 
<strong>例 2.3</strong>

ノード数 $5$ ，エッジ数 $6$ のグラフの例をに示した．このグラフのノード集合は $\symbfscr{V}=\allowbreak\left\{v\_1,v\_2,v\_3,v\_4,v\_5\right\}$ ，エッジ集合は $\symbfscr{E} =\allowbreak \left\{e\_1,e\_2,e\_3,e\_4,e\_5,e\_6\right\}$ である．

このグラフの隣接行列は次のように表せる．  $$ \symbf{A} = 
\begin{pmatrix}
    0 & 1 & 0 & 1 & 1\\
    1 & 0 & 1 & 0 & 0\\
    0 & 1 & 0 & 0 & 1\\
    1 & 0 & 0 & 0 & 1\\
    1 & 0 & 1 & 1 & 0
\end{pmatrix} $$  
</div>



[メインページ](../../index.markdown)

[章目次](./chap2.md)