[メインページ](../../index.markdown)

[章目次](./chap2.md)
## 2.4. スペクトルグラフ理論

スペクトルグラフ理論は，ラプラシアン行列の固有値や固有ベクトルを通じてグラフの性質を分析する理論である．ここからは，グラフのラプラシアン行列を導入したのち，ラプラシアン行列の固有値・固有ベクトルの主な性質について説明していく．

### ラプラシアン行列

ここではグラフの隣接行列に加え，もう一つのグラフの行列表現である"ラプラシアン行列"を導入する． 
<div class="definition">
 
<strong>定義 2.28 ラプラシアン行列</strong>
  $\symbf{A}$ を隣接行列とするグラフ $\mathcal{G} = \left\{\mathcal{V},\mathcal{E}\right\}$ に対して，ラプラシアン行列は以下のように定義される．

 $$
 
\tag{2.7}
    \symbf{L} = \symbf{D} - \symbf{A}. $$
 

ここで， $\symbf{D}$ は対角次数行列 $\symbf{D}=\textrm{diag}(d(v_1),\dots,d(v_{\|\mathcal{V}\|}))$ である． 
</div>
 ラプラシアン行列のもう一つの定義は，式(2.7)を正規化したものである． 
<div class="definition">
 
<strong>定義 2.29 正規化ラプラシアン行列</strong>


 $\symbf{A}$ を隣接行列とするグラフ $\mathcal{G} = \left\{\mathcal{V},\mathcal{E}\right\}$ について，その正規化ラプラシアン行列は以下のように定義される．

 $$
 
\tag{2.8}
    \symbf{L} = \symbf{D}^{-\tfrac{1}{2}}(\symbf{D} - \symbf{A})\symbf{D}^{-\tfrac{1}{2}} = \symbf{I} - \symbf{D}^{-\tfrac{1}{2}}\symbf{A}\symbf{D}^{-\tfrac{1}{2}}. $$
  
</div>
 ここからは，特に言及しない限り，定義2.28で導入した（非正規化）ラプラシアン行列の議論に焦点を当てる．しかし，本書の後半の章では，正規化ラプラシアン行列も利用されることに注意されたい．

次数行列 $\symbf{D}$ と隣接行列 $\symbf{A}$ はどちらも対称行列であるため，ラプラシアン行列は対称行列である．ここで，ベクトル $\symbf{f}$ を考える．このベクトルの $i$ 番目の要素 $\symbf{f}[i]$ は，ノード $v_i$ に関連しているものとする． $\symbf{L}$ に $\symbf{f}$ をかけると，新しいベクトル $\symbf{h}$ を得ることができる．

 

$$

\begin{aligned}
    \symbf{h} &= \symbf{L}\symbf{f}\nonumber\\
    &= (\symbf{D} - \symbf{A})\symbf{f}\nonumber\\
    &= \symbf{D}\symbf{f} - \symbf{A}\symbf{f}.\nonumber
\end{aligned}
$$

 

 $\symbf{h}$ の $i$ 番目の要素は以下のようになる．

 

$$

\begin{aligned}
\symbf{h}[i] &= d(v_i)\cdot \symbf{f}[i] - \sum^{N}_{j=1}\symbf{A}_{i,j}\cdot\symbf{f}[j]\nonumber\\
&= d(v_i)\cdot\symbf{f}[i] - \sum_{v_j\in \mathcal{N}(v_i)}\symbf{A}_{i,j}\cdot\symbf{f}[j] \nonumber\\
&= \sum_{v_j\in \mathcal{N}(v_i)}(\symbf{f}[i] - \symbf{f}[j]).
\end{aligned}
\tag{2.9}
$$

 

式(2.9)から明らかのように， $\symbf{h}[i]$ は，ノード $v_i$ とその近傍ノード $\mathcal{N}(v_i)$ との間における， $\symbf{f}$ の差を合計したものになる．次に， $\symbf{f}^{T}\symbf{L}\symbf{f}$ を以下のように求める．

 

$$

\begin{aligned}
    \symbf{f}^{T}\symbf{L}\symbf{f} &= \sum_{v_i\in \mathcal{V}}\symbf{f}[i] \sum_{v_j\in \mathcal{N}(v_i)}(\symbf{f}[i] - \symbf{f}[j])\nonumber\\
    &= \sum_{v_i\in \mathcal{V}}\sum_{v_j\in \mathcal{N}(v_i)}(\symbf{f}[i]\cdot\symbf{f}[i] - \symbf{f}[i]\cdot\symbf{f}[j])\nonumber\\
    &=\sum_{v_i\in \mathcal{V}}\sum_{v_j\in\mathcal{N}(v_i)}(\dfrac{1}{2}\symbf{f}[i]\cdot\symbf{f}[i] - \symbf{f}[i]\cdot\symbf{f}[j] + \dfrac{1}{2}\symbf{f}[j]\cdot\symbf{f}[j])\nonumber\\
    &= \dfrac{1}{2}\sum_{v_i\in \mathcal{V}}\sum_{v_j\in\mathcal{V}(v_i)}(\symbf{f}[i] - \symbf{f}[j])^{2}.
\end{aligned}
\tag{2.10}
$$

 

以上から， $\symbf{f}^{T}\symbf{L}\symbf{f}$ は隣接するノード間における， $\symbf{f}$ の差の二乗を合計したものとなる．言い換えれば，隣接するノード（に付随した $\symbf{f}$ ）の値がどれだけ異なるかを測定していることになる．  $\symbf{f}^{T}\symbf{L}\symbf{f}$ は，どんな非ゼロ実数ベクトル $\symbf{f}$ を選んでも常に非負であり，このことは，ラプラシアン行列が半正定値であることを示している．

### ラプラシアン行列の固有値と固有ベクトル

ここでは，ラプラシアン行列の固有値と固有ベクトルの主な性質について説明していく． 
<div class="theorem">
 
<strong>定理 2.30</strong>
 グラフ $\mathcal{G} = \left\{\mathcal{V},\mathcal{E}\right\}$ について，ラプラシアン行列 $\symbf{L}$ の固有値は非負である． 
<div class="prf">
<strong>証明: </strong>
 ラプラシアン行列 $\symbf{L}$ の固有値を $\lambda$ とし，対応する正規化固有ベクトルを $\symbf{u}$ とする．固有値と固有ベクトルの定義によれば， $\lambda\symbf{u} = \symbf{L}\symbf{u}$ であり， $\symbf{u}$ は非ゼロの単位ベクトルで $\symbf{u}^{T}\symbf{u} = 1$ である．したがって，

 $$
 \nonumber
    \lambda = \lambda \symbf{u}^{T}\symbf{u} = \symbf{u}^{T}\lambda\symbf{u} = \symbf{u}^{T}\symbf{L}\symbf{u} \geq 0. $$
 

Q.E.D. 
</div>
 
</div>


 $N$ 個のノードを持つグラフ $\mathcal{G}$ には，(重複も含めて)合計で $N$ 個の固有値・固有ベクトルが存在し，定理2.30によれば，それら全ての固有値は非負である．また， $0$ に等しい固有値は常に存在している．実際，ベクトル $\symbf{u}\_1 = \tfrac{1}{\sqrt{N}}(1,\dots,1)$ を考えると，式(2.9)により， $\symbf{L}\symbf{u}\_1 = \symbf{0}=0\symbf{u}\_1$ であることがわかる．これは， $\symbf{u}\_1$ が固有値 $0$ に対応する固有ベクトルであることを表している．便宜上，固有値を $0=\lambda_1\leq\lambda_2\leq,\dots,\leq\lambda_N$ のように小さい順に並べ，それらに対応する正規化固有ベクトルを $\symbf{u}\_1,\dots,\symbf{u}\_N$ と表記することにする． 
<div class="definition">
 
<strong>定義 2.31</strong>
 グラフ $\mathcal{G}$ が与えられたとき，そのラプラシアン行列 $\symbf{L}$ の固有値 $0$ の個数（固有値 $0$ の重複度）は，グラフが持つ連結成分の数に等しい． 
<div class="prf">
<strong>証明: </strong>
  $\mathcal{G}$ は $K$ 個の連結成分を持つと仮定する．するとノード集合 $\mathcal{V}$ を $K$ 個の互いに素な部分集合 $\mathcal{V}_1,\dots,\mathcal{V}_K$ に分割することができる．

まず，固有値 $0$ に対応する正規直交固有ベクトルが少なくとも $K$ 個存在することを示す． $K$ 個のベクトルを $\symbf{u}_1,\dots,\symbf{u}_K$ とおき， $v_j\in \mathcal{V}_i$ なら $\symbf{u}_i[j] = \tfrac{1}{\sqrt{\|\mathcal{V}_i\|}}$ ，それ以外なら $0$ となるように構成する．すると， $i=1,\dots,K$ について $\symbf{L}\symbf{u}_i=0$ となるから， $K$ 個のベクトルはすべて固有値 $0$ に対応する $\symbf{L}$ の固有ベクトルであることがわかる．さらに構成したベクトルの値の定め方より， $i\neq j$ のとき $\symbf{u}^T_i \symbf{u}_j = 0$ となることがわかるから， $K$ 個の固有ベクトルは互いに直交していることになる．したがって，固有値 $0$ の重複度は少なくとも $K$ であることが示せた．

次に， $\symbf{u}_1,\dots,\symbf{u}_K$ とは異なる，固有値 $0$ に対応する固有ベクトル $\symbf{u}^{\ast}$ を考え，それら $K$ 個の固有ベクトルの全てと直交していると仮定しよう． $\symbf{u}^{\ast}$ はゼロベクトルではないため， $\symbf{u}^{\ast}$ は非ゼロ要素を持つはずである．そこで，（非ゼロとなる）要素の値を $\symbf{u}^{\ast}[d]$ とし，ノード $v_d\in \mathcal{V}_i$ に関連させる．式(2.10)によれば，

 $$
 \nonumber
    \symbf{u}^{\ast T}\symbf{L}\symbf{u}^{\ast} = \dfrac{1}{2}\sum_{v_i\in \mathcal{V}}\sum_{v_j\in \mathcal{N}(v_i)}(\symbf{u}^{\ast}[i] - \symbf{u}^{\ast}[j])^2 $$
 

と計算できる． $\symbf{u}^{\ast T}\symbf{L}\symbf{u}^{\ast}=0$ となることを保証するためには，( $v_d$ と)同じ連結成分内のノードに対する要素の値も同一である必要がある．言い換えれば， $\mathcal{V}_i$ 内の全ノードに関連した要素の値 $\symbf{u}^{\ast}[i]$ は，ノード $v_d$ に関連させた要素の値 $\symbf{u}^{\ast}[d]$ と同じになる．ゆえに， $\symbf{u}^T_i\symbf{u}^{\ast}>0$ が成り立ってしまう．つまり， $\symbf{u}^{\ast}$ は $\symbf{u}_i$ に直交していないことになり， $\symbf{u}^{\ast}$ においた仮定と矛盾する．以上より，固有値 $0$ に対応する固有ベクトルは，最初に構成した $K$ 個のベクトルより多くは存在しないことになる．Q.E.D. 
</div>
 
</div>



[メインページ](../../index.markdown)

[章目次](./chap2.md)

[前の節へ](./subsection_03.md) [次の節へ](./subsection_05.md)


