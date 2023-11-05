[メインページ](../../index.markdown)

[章目次](./chap7.md)
## 7.2. ノードごとのサンプリング

式(7.3)のノードごとの集計操作は次のように書き直すことができる:

 $$
 \mathbf{F}_i^{(l)}=\left\|\tilde{\symcal{N}}\left(v_i\right)\right\| \sum_{v_j \in \tilde{\symcal{N}}\left(v_i\right)} \frac{1}{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \mathbf{\Theta}^{(l-1)}
    
\tag{7.6} $$
 

これは次のように期待値の形で書くことができる:

 $$
 \mathbf{F}_i^{(l)}=\left\|\tilde{\symcal{N}}\left(v_i\right)\right\| \cdot \mathbb{E}\left[\mathscr{F}_{v_i}\right]    
\tag{7.7} $$
 

ここで,  $\mathscr{F}\_{v_i}$ は次のように定義される離散的なランダム変数である:

 $$
 \nonumber
    p\left(\mathscr{F}_{v_i}=\hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}\right)=
    \begin{cases}
    \frac{1}{\tilde{\symcal{N}}\left(v_i\right)}, & \text { if } v_j \in \tilde{\symcal{N}}\left(v_i\right) \\ 
    0, & \text { otherwise }.        
    \end{cases} $$
 

式(7.7)について, 必要なメモリーを減らしつつ計算を高速化する自然なアイデアは, モンテカルロ・サンプリングによって期待値を近似することである. 具体的には, 期待値 $\mathbb{E}\left[\mathscr{F}\_{v_i}\right]$ は次のように推定することができる:

 $$
 \mathbb{E}\left[\mathscr{F}_{v_i}\right] \approx \hat{\mathscr{F}}_{v_i}=\frac{1}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in n^{l}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}
    
\tag{7.8} $$
 

ここで,  $n^{l}\left(v_i\right) \subset \tilde{\symcal{N}}\left(v_i\right)$ はノード $v_i$ の $l$ 層目の計算の際に, 次の確率分布に従って $\symcal{V}$ からサンプリングされたノード群を表す:

 $$
 p\left(v_j \mid v_i\right)=
    \begin{cases}
    \frac{1}{\tilde{\symcal{N}}\left(v_i\right)}, & \text { if } v_j \in \tilde{\symcal{N}}\left(v_i\right) \\
    0, & \text { otherwise }.    
    \end{cases}
    
\tag{7.9} $$
 

次に示すように, 式(7.8)の推定量は不偏となる:

  

$$

\begin{eqnarray}
\mathbb{E}\left[\hat{\mathscr{F}}_{v_i}\right] &=&\mathbb{E}\left[\frac{1}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in n^{l}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \mathbb{1}\left\{v_j \in n^{l}\left(v_i\right)\right\}\right] \nonumber\\
&=&\mathbb{E}\left[\frac{1}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in \symcal{V}} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \mathbb{1}\left\{v_j \in n^{l}\left(v_i\right)\right\}\right]  \nonumber \\
&=&\frac{1}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in \symcal{V}} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \mathbb{E}\left[\mathbb{1}\left\{v_j \in n^{l}\left(v_i\right)\right\}\right] \nonumber\\
&=&\frac{1}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in \symcal{V}} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \frac{\left\|n^{l}\left(v_i\right)\right\|}{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|} \nonumber\\
&=&\frac{1}{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|} \sum_{v_j \in \symcal{V}} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \nonumber\\ 
&=&\mathbb{E}\left[\mathscr{F}_{v_i}\right] \nonumber
\end{eqnarray}
$$

  

ここで,  $\mathbb{1}\left\\{v_j \in n^{l}\left(v_i\right)\right\\}$ はランダム変数を表し,  $v_j \in n^{l}\left(v_i\right)$ の時は1, それ以外の時は0をとる.

式(7.8)より, ノードごとの集計操作は次のように書くことができる:

 $$
 \mathbf{F}_i^{(l)}=\frac{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in n^{l}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}
    
\tag{7.10} $$
 

式(7.10)で利用されるサンプル操作は, ノード集合 $n^{l}\left(v_i\right)$ がノード $v_i$ のみについてのサンプリングであり, 他のノードと共有しないことから, ノードごとのサンプリング(*node-wise sampling*) と呼ばれる. 具体的には, GraphSAGEフィルタ（詳細は5.3.2節参照）は近傍サンプリング操作が含まれているのでノードごとのサンプリングとみなすことができる. 特定のグラフフィルタ層において, サンプリングサイズ $\left\|n^{l}\left(v_i\right)\right\|$ は全てのノードについて一定の値にする（ $\left\|n^{l}\left(v_i\right)\right\|=m$ ）ことが多い. グラフフィルタごとにサンプリングサイズは異なるが, 本章では便宜上, 全て同じサンプリングサイズ $m$ であるとする.

このノードごとサンプリングする方法は, 各層で計算に関係するノードの数を $m$ に固定して制御することができるが, それでも $m$ が大きい場合には依然として「近傍爆発」の問題が避けられない. 図7.1と同じようにトップダウンで考えると, ノード $v_i$ についての最終的な表現 $\mathbf{F}\_i^{(L)}$ は $m^L$ のオーダーである. これは層の数 $L$ が大きくなると指数関数的に増加する. 空間・時間計算量はそれぞれ $O\left(m^{L} \cdot d^{2}\right)$ と  $O\left(\left\|\symcal{V}\_l\right\| \cdot m^{L} \cdot d^{2}\right)$ である. この問題を緩和する方法の1つはサンプリングサイズ $m$ を小さな値にすることである. しかし,  $m$ を小さくすることで式(7.8)の推定の分散が大きくなってしまうためあまり望ましくない.

妥当な分散を維持しつつも 極めて小さなサンプリングサイズ $m$ （2程度）を実現するサンプリング方法が(Chen et al., 2018a)で提案されている.  $l=2, \ldots, L$ の各表現 $\mathbf{F}\_i^{(l-1)}$ について, 履歴の値 $\overline{\mathbf{F}}\_i^{(l-1)}$ を保持しておいて, 式(7.3)の計算時にそれらを利用するというアイデアである.  $\mathbf{F}\_i^{(l)}$ を計算するたび, 対応する履歴表現 $\overline{\mathbf{F}}\_i^{(l)}$ を $\mathbf{F}\_i^{(l-1)}$ で更新する. 学習過程においてモデルのパラメータがすぐには変化しないという仮定のもとで, 履歴表現は実際の表現に近い値になると考えられる. 式(7.3)の推定には, これまで通りモンテカルロ・サンプリングを使用する. しかし,  $n^{l}\left(v_i\right)$ にサンプリングされないノードについては, その履歴表現を計算に含める. 式(7.3)は2つの項に分けることができる:

 $$
 \mathbf{F}_i^{(l)}=\sum_{v_j \in \tilde{\symcal{N}}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \Delta \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}+\sum_{v_j \in \tilde{\symcal{N}}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \overline{\mathbf{F}}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}
    
\tag{7.11} $$
 

ここで,

  

$$

\begin{eqnarray}
\Delta \mathbf{F}_j^{(l-1)}=\mathbf{F}_j^{(l-1)}-\overline{\mathbf{F}}_j^{(l-1)}
\nonumber
\end{eqnarray}
$$

  

である.  $\Delta \mathbf{F}\_j^{(l-1)}$ 項は最新の表現と履歴表現の差分を表す. 式(7.3)のすべての項を推定するのにモンテカルロ・サンプリングを使うのではなく, 差分についてのみ推定する:

 $$
 \sum_{v_j \in \tilde{\symcal{N}}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \Delta \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)} \approx \frac{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in n^{l}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \Delta \mathbf{F}_j^{(l-1)} \boldsymbol{\Theta}^{(l-1)}
    
\tag{7.12} $$
 

式(7.12)を用いることで, 式(7.11)は次のように推定することができる:

 $$
 \mathbf{F}_i^{(l)} \approx \frac{\left\|\tilde{\symcal{N}}\left(v_i\right)\right\|}{\left\|n^{l}\left(v_i\right)\right\|} \sum_{v_j \in n^{l}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \Delta \mathbf{F}_j^{(l-1)} \mathbf{\Theta}^{(l-1)}+\sum_{v_j \in \tilde{\symcal{N}}\left(v_i\right)} \hat{\mathbf{A}}_{i, j} \overline{\mathbf{F}}_j^{(l-1)} \mathbf{\Theta}^{(l-1)}
    
\tag{7.13} $$
 

これは, 制御変数（control-variate, CV）推定量と呼ばれ, ノード表現の更新に利用される. 式(7.13)の右辺第2項は保存してあるノードの履歴表現から計算されるので, 再帰的に計算する必要がなく, 計算量の観点から効率的である. 式(7.12)の推定が不偏なので, CV推定量は不偏推定量である.  $\Delta \mathbf{F}\_i^{(l-1)}$ は $\mathbf{F}\_i^{(l-1)}$ よりもずっと小さいので, 式(7.13)の分散は式(7.10)の分散よりも小さくなる. しかし, タダでは分散は小さくならない. この操作の時間計算量は, 式(7.10)の集約操作と同様に $O\left(m^{L} \cdot d^{2}\right)$ （CV推定量の場合には $m$ ははるかに小さくすることができる）となる一方で, 必要なメモリーはずっと大きくなる. 実際, この操作に関係するすべてのノードの履歴表現を保持するためには $O\left(d e g^{L} \cdot d\right)$ だけのメモリーが必要になる. これはノードごとのサンプリングなしでのSGDの場合と同様である. なお, 空間計算量はサンプリングサイズ $m$ にはよらないため,  $m$ を小さくしたからといって空間計算量を減らすことには必ずしも繋がらない.


[メインページ](../../index.markdown)

[章目次](./chap7.md)

[前の節へ](./subsection_01.md) [次の節へ](./subsection_03.md)


