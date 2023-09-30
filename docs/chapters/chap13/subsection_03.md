[メインページ](../../index.markdown)

[章目次](./chap13.md)
## 13.3. 薬物類似性の統合

技術の急速な発展に伴った, 計算創薬研究や医薬品安全性の研究のために, 複数のソースから医薬品データが収集されている. 例えば, 薬の構造情報は化学フィンガープリントソフトウェアを用いて抽出することができ, その効用情報は薬の包装（パッケージ）に書かれている情報から抽出することができる(Kuhn et al., 2016). 薬物間相互作用(DDI)予測などの後段のタスクをよりスムーズに行うためには, 複数ソースからの薬物データをうまく融合する必要がある. というのもこれらのデータは様々な観点での薬物情報を含んでいるためである. こうしたデータは薬物間の異なる類似性が組み込まれている可能性があるため, 目標としている結果との関連性が異なる場合がある. 例えば, 薬物の構造的な類似性は, 薬効の類似性よりも相互作用の仕方に影響を与える可能性がある. (Ma et al., 2018c)では,後段のタスクに応じて様々なデータソースからの薬物の類似性情報を融合するため, グラフニューラルネットワークをベースにした, 注意機構が提案されている. 具体的には, 薬物の特徴の各データソースは一つのビューとみなされる(Ma et al., 2018c). ビュー $t \in\{1, \ldots, T\}$ の全ノードの特徴は行列 $\mathbf{X}\_t \in \mathbb{R}^{N \times d_t}$ を使って表される. ここで,  $N$ は薬物の数で $d_t$ はこのビューにおける特徴量の次元である. さらに, このビューにおける薬物間の類似性に関する情報は, 類似性行列 $\mathbf{A}\_t \in \mathbb{R}^{N \times N}$ に組み込まれている. 異なるビューの特徴量と類似性行列を統合し, すべてのビューにわたって, それらをまとめた特徴量 $\mathbf{Z} \in \mathbb{R}^{N \times d}$ と類似性行列 $\mathbf{A}$ を生成することが目標となる.

異なるビューの類似性行列は次のように統合される:

 $$
 \mathbf{A}=\sum_{t=1}^{T} \operatorname{diag}\left(\mathbf{g}_t\right) \mathbf{A}_t 
\tag{13.3} $$
 

ここで,  $\mathbf{g}\_t \in \mathbb{R}^{N}$ は注意機構スコアであり, 次のように学習される:

  

$$

\begin{eqnarray}
    \mathbf{g}_t^{\prime}=\boldsymbol{\Theta}_t \mathbf{A}_t+\mathbf{b}_t, \forall t=1, \ldots, T \nonumber\\ {\left[\mathbf{g}_1, \ldots, \mathbf{g}_T\right]=\operatorname{softmax}\left(\left[\mathbf{g}_1^{\prime}, \ldots, \mathbf{g}_T^{\prime}\right]\right)}
    \nonumber
\end{eqnarray}
$$

  

ここで,  $\boldsymbol{\Theta}\_t$ と $\mathbf{b}\_t$ は学習されるパラメータで, 各行にソフトマックス関数が適用される. 統合された特徴量行列は, この統合された類似性行列を用いて, 各ビューの特徴量にGNNフィルタを適用することで得られる:

 $$
 \mathbf{Z}=\alpha(\text { GNN-Filter }(\mathbf{A}, \mathbf{X}))
    
\tag{13.4} $$
 

ここで,  $\mathbf{X}=\left[\mathbf{X}\_1, \ldots, \mathbf{X}\_T\right]$ は異なるビューからの特徴量を結合したものである. 具体的には, GCNフィルタ(5.3.2節参照)が採用されており(Ma et al., 2018c),  $\alpha()$ はソフトマックス関数である(行ごとに適用される). デコーダーは,  $\mathbf{X}$ からの情報ができる限りたくさん含むように,  $\mathbf{Z}$ から $\mathbf{X}$ を再構成する. デコーダーもGNNフィルタを用いてモデル化することができる:

 $$
 \mathbf{X}^{\prime}=\alpha(\text { GNN-Filter }(\mathbf{A}, \mathbf{Z}))
    
\tag{13.5} $$
 

ここでもGCNフィルタをグラフフィルタとして採用し, 非線形活性化関数 $\alpha()$ としてシグモイド関数が用いられる(Ma et al., 2018c). 再構成損失は次のようになる:

 

$$
 \mathcal{L}_{e d}=\left\|\mathbf{X}-\mathbf{X}^{\prime}\right\|^{2} \nonumber $$


 

式(13.3), 式(13.4), 式(13.5)のパラメータはこの再構成損失を最小化することで得られる. さらに, 統合された表現 $\mathbf{Z}$ を後段のタスクに用いることができ, 後段のタスクからの勾配を使って式(13.3), 式(13.4), 式(13.5)のパラメータを更新することもできる.


[メインページ](../../index.markdown)

[章目次](./chap13.md)

[前の節へ](./subsection_02.md) [次の節へ](./subsection_04.md)


