[メインページ](../../index.markdown)

[章目次](./chap3.md)
## 3.6. 深層ニューラルネットワークの学習

この節では，深層ニューラルネットワークの学習手順について説明する.
まず，ニューラルネットワークを学習するための一般的なアプローチである，勾配降下法とその変形版について簡単に紹介する.
次に，ニューラルネットワークのパラメータの勾配を計算するための効率的な動的アルゴリズムである，逆伝播法について説明する.

### 勾配降下法

深層モデルを学習させるためには，学習させたいパラメータに関する損失関数 $\mathcal{L}$ を最小化する必要がある.
 $\mathbf{W}$ を最適化するすべてのパラメータとして，損失関数を一般に $\mathcal{L}(\mathbf{W})$ と表す.
深層学習における損失関数の最小化には，勾配降下法とその変形版がよく用いられる.
勾配降下法（Cauchy，n.d.）は，一次の繰り返し最適化アルゴリズムである.
各繰り返しにおいて，以下のように負の勾配の方向へ一歩ずつ，パラメータ $\mathbf{W}$ を更新する：

 $$ \mathbf{W}^{\prime}=\mathbf{W}-\eta \cdot \nabla_{\mathbf{W}} \mathcal{L}(\mathbf{W})
    
\tag{3.13} $$ 

ここで, $\nabla_{\mathbf{W}} \mathcal{L}(\mathbf{W})$ は勾配を表し， $\eta$ は学習率を表す.
学習率は正の値で，どの程度 $\mathbf{W}$ を更新するかを表す.
深層学習では，学習率 $\eta$ は小さい定数に固定されることが多い.

損失関数は通常，学習サンプルのペナルティの総和である.
したがって，損失関数を以下のように書くことができる：

 $$ \mathcal{L}(\mathbf{W})=\sum_{i=1}^{N_s} \mathcal{L}\_i(\mathbf{W})
    
\tag{3.14} $$ 

ここで， $\mathcal{L}\_i(\mathbf{W})$ は $i$ 番目のサンプルの損失を表し,
 $N_s$ はサンプル数を表す.
多くの場合，直接 $\nabla_{\mathbf{W}}\mathcal{L}(\mathbf{W})$ を計算するのは非常に手間がかかる.
そこで，深層ニューラルネットワークの学習で非常によく使われている，ミニバッチ勾配降下法が登場する.
ミニバッチ勾配降下法では，すべての学習サンプルに対して勾配を評価するのではなく，学習データから少量のサンプルを抽出し，それを使って勾配を推定する.
この推定された勾配を利用してパラメータを更新する.
具体的には，勾配は $\sum_{j\in\mathcal{M}}\nabla_{\mathbf{W}}\mathcal{L}_j(\mathbf{W})$ として推定される.
ここで, $\mathcal{M}$ はミニバッチのサンプル集合を表す. Adagrad (Duchi et
al.，2011)，Adadelta (Zeiler，2012)，および Adam (Kingma and Ba，2014)
などの深層ニューラルネットワークの学習のために，様々な勾配降下法（の変形版）が開発されている.
これらは一般的に，標準的な勾配降下法よりも収束性が高い.

### 逆伝播法

<figure>

<img src="./fig/fig3_17.png" width="100%"/>

<figcaption>図3.17 隣り合う層のニューロン</figcaption>

</figure>

勾配に基づく最適化を行うための重要なステップの1つは，すべてのパラメータに関する勾配を計算することである.
逆伝播法は動的計画法を用いて勾配を計算する効率的な方法である.
逆伝播法は2つの段階からなる： １段階目は「順伝搬」である.
この段階では，入力はモデルの深い層まで通過する．出力は現在のパラメータセットに対して計算され,損失関数の値が評価される.
2段階目は「逆伝播」である.
この段階の目的は，パラメータに対する損失関数の勾配を計算することである.
チェーン・ルールによれば，すべてのパラメータに対する勾配は，出力層から後ろ向きに動的に計算することができる.
次に，逆伝播パスについて説明する.

図3.17はそれぞれ異なる層のつながっているニューラルネットワークのユニット $h^{0}，h^{1}，\ldots，h^{k}，o$ を表す.
ここで， $h^i$ は $i$ 番目の層のユニットを表し， $h^0$ は入力層のユニット， $o$ は出力層のユニットを表す.
 $\left(h^{r-1}，h^{r}\right)$ の間に1本しかエッジがないとすると，損失関数の微分はチェーン・ルール[^2]
を使って次のように書くことができる： 



 $$ \frac{\partial \mathcal{L}}{\partial w_{\left(h^{r-1}，h^{r}\right)}}=\frac{\partial \mathcal{L}}{\partial o} \cdot\left[\frac{\partial o}{\partial h^{k}} \prod_{i=r}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}}\right] \cdot \frac{\partial h^{r}}{\partial w_{\left(h^{r-1}，h_r\right)}} \forall r \in 1 \ldots k
    
\tag{3.15} $$ 

ここで, $w_{\left(h^{r-1}，h^{r}\right)}$ はユニット $h^{r-1}$ と $h^r$ の間のパラメータを表す.

複数層のニューラルネットワークでは， $\left(h^{r-1}，h^{r}\right)$ の間に複数パスある場合が多い.
したがって，上の計算で得られた勾配を合計する必要がある：

 $$ \frac{\partial \mathcal{L}}{\partial w\left(h^{r-1}，h^{r}\right)}=\underbrace{\frac{\partial \mathcal{L}}{\partial o} \cdot\left[\sum_{\left[h^{r}，h^{r+1}，\ldots，h^{k}，o\right] \in \mathcal{P}} \frac{\partial o}{\partial h^{k}} \prod_{i=r}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}}\right]}_{\text {逆伝搬の前のプロセスで} \Delta\left(h^{r}，o\right)=\frac{\partial \mathcal{L}}{\partial h^{r}}\text {が計算される}} \frac{\partial h^{r}}{\partial w_{\left(h^{r-1}，h^{r}\right)}}
    
\tag{3.16} $$ 

ここで， $\mathcal{P}$ は $h^r$ から $o$ に至るパスの集合を表し，これは $\left(h^{r-1}，h^{r}\right)$ まで拡張することができる.
式(3.16)の右辺は2つの要素があり，後者は計算が面倒（後述）なのに対し、最初の部分（ $\Delta\left(h^{r}，o\right)=\frac{\partial \mathcal{L}}{\partial h^{r}}$ と式中に記載）は再帰的に計算が可能である.
次に，第一項を再帰的に評価する方法について説明する．
第一項は以下のように計算することができる：

 

$$
\begin{aligned} 
    \Delta\left(h^{r}，o\right) &=\frac{\partial \mathcal{L}}{\partial o} \cdot\left[\sum_{\left[h^{r}，h^{r+1}，\ldots，h^{k}，o\right] \in \mathcal{P}} \frac{\partial o}{\partial h^{k}} \prod_{i=r}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}}\right] \\ &=\frac{\partial \mathcal{L}}{\partial o} \cdot\left[\sum_{\left[h^{r}，h^{r+1}，\ldots，h^{k}，o\right] \in \mathcal{P}} \frac{\partial o}{\partial h^{k}} \prod_{i=r+1}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}} \cdot \frac{\partial h^{r+1}}{\partial h^{r}}\right] 
    \end{aligned}
    
\tag{3.17} $$ 

<figure>

<img src="./fig/fig3_18.png" width="100%"/>

<figcaption>図3.18 パスの分解</figcaption>

</figure>

図3.18に示したように， $\mathcal{P}$ に属する任意のパスは2つのパートにわけることができる：
 $\left(h^{r}，h^{r+1}\right)$ 間のエッジと， $h^{r+1}$ から $o$ までの残りのパスである.
次に，エッジ $\left(h^{r}，h^{r+1}\right)$ を用いて $\mathcal{P}$ のパスを分類する.
 $\left(h^{r}，h^{r+1}\right)$ と同じエッジを共有するパスを $\mathcal{P}_{r+1}$ と書く.

 

$$
\begin{aligned} \Delta\left(h^{r}，o\right) &=\frac{\partial \mathcal{L}}{\partial o} \cdot\left[\sum_{\left(h^{r}，h^{r+1}\right) \in \mathcal{E}} \frac{\partial h^{r+1}}{\partial h^{r}} \cdot\left[\sum_{\left[h^{r+1}，\ldots，h_k，o\right] \in \mathcal{P}_{r+1}^{\prime}} \frac{\partial o}{\partial h_k} \prod_{i=r+1}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}}\right]\right] \\ &=\sum_{\left(h^{r}，h^{r+1}\right) \in \mathcal{E}} \frac{\partial h^{r+1}}{\partial h^{r}} \cdot \frac{\partial \mathcal{L}}{\partial o} \cdot\left[\sum_{\left[h^{r+1}，\ldots，h_k，o\right] \in \mathcal{P}_{r+1}^{\prime}} \frac{\partial o}{\partial h_k} \prod_{i=r+1}^{k-1} \frac{\partial h^{i+1}}{\partial h^{i}}\right] \\ &=\sum_{\left(h^{r}，h^{r+1}\right) \in \mathcal{E}} \frac{\partial h^{r+1}}{\partial h^{r}} \cdot \Delta\left(h^{r+1}，o\right) \end{aligned}
    
\tag{3.18} $$ 

ここで， $\mathcal{E}$ はユニット $h^r$ から $(r+1)$ 番目の層のユニット $h^{r+1}$ への，存在しうる全てのエッジの集合を表す.
図3.18に図示したように， $(r+1)$ 番目の層の全てのユニットは $h^r$ につながっているため,
式(3.18)の最初の和に含まれていることになる.
 $h^{r+1}$ は $h^{r}$ よりもあとの層であり， $\Delta\left(h^{r+1}，o\right)$ は逆伝搬の前のプロセスですでに評価されているので，そのまま使うことができる.
あとは，式(3.18)の評価のためには $\frac{\partial h^{r+1}}{\partial h^{r}}$ を評価するだけである.
そのためには活性化関数を考慮にいれる必要がある.
 $\alpha^{r+1}$ をユニット $h^{r+1}$ の活性化関数にいれる直前の値とする.
すなわち， $h^{r+1}=\alpha\left(a^{r+1}\right)$ とする.
 $\frac{\partial h^{r+1}}{\partial h^{r}}$ を評価するために次のようにチェーン・ルールを使う：

 $$ \frac{\partial h^{r+1}}{\partial h^{r}}=\frac{\partial \alpha\left(a^{r+1}\right)}{\partial h^{r}}=\frac{\partial \alpha\left(a^{r+1}\right)}{\partial a^{r+1}} \cdot \frac{\partial a^{r+1}}{\partial h^{r}}=\alpha^{\prime}\left(a^{r+1}\right) \cdot w_{\left(h^{r}，h^{r+1}\right)}
    
\tag{3.19} $$ 

ここで， $w_{\left(h^{r}，h^{r+1}\right)}$ はユニット $h^r$ と $h^{r+1}$ の間のパラメータである.
これを用いると， $\Delta\left(h^{r}，o\right)$ を次のように書き換えることができる：

 $$ \Delta\left(h^{r}，o\right)=\sum_{\left(h^{r}，h^{r+1}\right) \in \mathcal{E}} \alpha^{\prime}\left(a^{r+1}\right) \cdot w_{\left(h^{r}，h^{r+1}\right)} \cdot \Delta\left(h^{r+1}，o\right)
    
\tag{3.20} $$ 

ここまでが，式(3.16)の前半部分の評価であり，後半部分については次のように評価することができる：

 $$ \frac{\partial h^{r}}{\partial w_{\left(h^{r-1}，h^{r}\right)}}=\alpha^{\prime}\left(a^{r}\right) \cdot h^{r-1}
    
\tag{3.21} $$ 

以上から，式(3.20)と式(3.21)を用いることで，式(3.16)を再帰的かつ効率的に評価することができる.

### 過学習の抑制

深層ニューラルネットワークは，モデルの表現力が非常に大きいため，学習データに対して容易に過学習してしまう.
この節では，ニューラルネットワークの過学習を防ぐための実用的なテクニックを紹介する.

#### 重み正則化

機械学習において，モデルの過学習を防ぐためには，モデルのパラメータに対する正則化項を損失関数に含めるという手法が一般的である.
正則化項はモデルパラメータを比較的小さくなるように制限することで，モデルの汎化性能を向上させる.
よく用いられている正則化項は，モデルパラメータの $L_1$ ノルムと $L_2$ ノルムの2つである.

#### ドロップアウト

ドロップアウトは，過学習を防ぐために有効な手法の一つである（Srivastava
et al.2014）.
ドロップアウトでは，学習手順の各バッチにおいて，ネットワーク内のいくつかのユニットをランダムに無視する.
各ユニットを無視する確率を制御する，「ドロップアウト率」 $p$ というハイパーパラメータを用いる.
そして，各反復計算において、確率 $p$ に従って，ネットワーク中のどのニューロンを「ドロップ」するかをランダムに決定する.
ネットワーク全体を使うのではなく，残りの（「ドロップ」されていない）ニューロンとネットワークを用いて，各反復計算における計算と予測を行う.
なお，ドロップアウトは通常，学習手順でのみ利用される.
すなわち，推論手順では常にネットワーク全体が予測の際に使用される.

#### バッチ正規化

バッチ正規化（Ioffe and
Szegedy，2015）は当初，内部共変量が変化する問題を解決するために導入されたが,
過学習を緩和させることにも役立つ.
バッチ正規化とは，前の層からの情報を活性化した結果を，次の層に送る前に正規化することである.
具体的には，ミニバッチ学習の際にバッチの平均を引き，バッチの標準偏差を割ることで正規化する.
推論段階では，母集団の統計量を用いて正規化を行う.


[メインページ](../../index.markdown)

[章目次](./chap3.md)

[前の節へ](./subsection_05.md) [次の節へ](./subsection_07.md)

[^2]: 訳注：合成関数の導関数は，構成している関数の導関数の積によって表される
