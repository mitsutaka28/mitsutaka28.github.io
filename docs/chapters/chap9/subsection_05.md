[メインページ](../../index.markdown)

[章目次](./chap9.md)
## 9.5. グラフ上でのGAN

敵対的生成ネットワーク(GAN)は, 敵対的プロセスによって複雑なデータの分布を推定する枠組みである. GANでは, 生成モデルを敵対者に見立てて, あるサンプルデータが元のデータからのものか, 生成モデルによって生成されたものかを見分けることを学習する(Good- fellow et al., 2014a). 具体的には, 生成モデル $G(\mathbf{z} ; \boldsymbol{\Theta})$ は事前のノイズ分布 $p(\mathbf{z})$ からサンプルした, ノイズ変数 $\mathbf{z}$ をパラメータ $\boldsymbol{\Theta})$ を用いてデータ空間に変換する. 一方で, 識別モデル $D(\mathbf{x} ; \mathbf{\Phi})$ はパラメータ $\mathbf{\Phi}$ を用いた2値分類モデルとしてモデル化され, 与えられたデータサンプル $\mathrm{x}$ がデータの分布 $p_{\text {data }}(\mathbf{x})$ からサンプルされたものか, 生成モデル $G$ から生成されたものかを識別する. 具体的には,  $D(\mathbf{x} ; \mathbf{\Phi})$ は $\mathbf{x}$ を,  $\mathbf{x}$ が生成モデルではなく与えられたデータセットから来ている確率を表すスカラー値に変換する. 学習過程においては, 2つのモデルは互いに競っている. 生成モデルは識別器を十分に欺けるような偽のサンプルデータを生成することを学習しようとし, 識別器は生成モデルによって生成されたサンプルを偽のサンプルデータと識別できるように自身を改良しようとする. この競争により, 生成されたサンプルが本物のサンプルと区別がつかなくなるまで両モデルを改良することができる. この競争は2人用のミニマックス・ゲームとして次のようにモデル化することができる:

 $$
 \min _{\boldsymbol{\Theta}} \max _{\boldsymbol{\Phi}} \mathbb{E}_{\mathbf{x} \sim p_{\text {data }}(\mathbf{x})}[\log D(\mathbf{x} ; \boldsymbol{\Phi})]+\mathbb{E}_{\mathbf{z} \sim p(\mathbf{z})}[\log (1-D(G(\mathbf{z} ; \boldsymbol{\Theta})))]    \nonumber $$
 

生成モデルと識別モデルのパラメータは交互に最適化される. 本節では, ノード表現学習とグラフ生成タスクを例に, GANのフレームワークがどのようにグラフ構造データに適用されるかを説明する.

### ノード表現学習のためのGAN

(Wang et al., 2018a)ではGANのフレームワークがノード表現学習に適用された. ノード $v_i$ に対して, 生成モデルは近傍ノードの分布を近似することを目的とする. 分布はは $p\left(v_j \mid v_i\right)$ と表され, ノードの全集合 $\mathcal{V}$ について定義される. 新の近傍ノードの集合 $\mathcal{N}\left(v_i\right)$ は $p\left(v_j \mid v_i\right)$ から抽出された観測サンプルデータとみなすことができる. 生成モデルは,  $G\left(v_j \mid v_i ; \boldsymbol{\Theta}\right)$ と表され,  $\mathcal{V}$ からノード $v_i$ につながっている可能性が高いノードを生成(より正確にいえば選択)する.  $G\left(v_j \mid v_i ; \boldsymbol{\Theta}\right)$ はノード $v_i$ の偽の隣接ノードとして $v_j$ をサンプル抽出する確率とみなすことができる. 識別モデルは,  $D\left(v_j, v_i ; \mathbf{\Phi}\right)$ と表され, 与えられたノードのペア $(v_j, v_i)$ がグラフ中でつながっているかを判定する. 識別モデルの出力は2つのノード $v_j$ と $v_i$ の間にエッジが存在する確率とみなすことができる. 生成器 $G$ と識別器 $D$ は互いに競争しする: 生成器 $G$ は背後にある確率分布 $p_{\text {true }}\left(v_j \mid v_i\right)$ に適合することで,  $v_i$ と関連性が十分高く, 識別器を欺けるようなノード $v_j$ を生成(選択)する. 一方で, 識別器は生成器によって生成されたノードとノード $v_i$ の実際の隣接ノードとを区別しようとする. 形式的には, 2つのモデルは以下のようなミニマックス・ゲームを行っている:

  

$$

\begin{eqnarray}
    \min_{\boldsymbol{\Theta}} \max_{\boldsymbol{\Phi}} V(G, D)=\sum_{v_i \in \mathcal{V}}\left(\mathbb{E}_{v_j \sim p_{\text {true }}}\left(v_j v_i\right)[\log D\left(v_j, v_i ; \boldsymbol{\Phi}\right)]\right\. \nonumber\\
\end{eqnarray}
$$

  

生成器 $G$ と識別器 $D$ のパラメータは, 目的関数 $V(G, D)$ を最大化・最小化することによって最適化することができる. 次に, 生成器と識別器の設計について詳細を述べる.

#### 生成器

生成器をモデル化する直接的な方法としては, 全ノード $\mathcal{V}$ についてソフトマックス関数を使うことである:

 $$
 G\left(v_j \mid v_i ; \boldsymbol{\Theta}\right)=\frac{\exp \left(\boldsymbol{\theta}_j^{\top} \boldsymbol{\theta}_i\right)}{\sum_{v_k \in \mathcal{V}} \exp \left(\boldsymbol{\theta}_k^{\top} \boldsymbol{\theta}_i\right)}
    
\tag{9.15} $$
 

ここで,  $\boldsymbol{\theta}\_i \in \mathbb{R}^{d}$ はこの生成器に固有のノード $v_i$ の $d$ 次元表現であり,  $\boldsymbol{\Theta}$ はすべてのノードについての表現を含む(これらは生成器もパラメータでもある). なお, この定式化では, ノード間の関連性は2つのノード表現の内積で測定される. 2つのノードの関連性が高ければ低次元表現が近くなると予想されるため, この考え方は合理的であるといえる. ひとたびパラメータ $\boldsymbol{\Theta}$ が学習されれば, ノード $v_i$ について, 生成器 $G$ を使って分布 $G\left(v_j \mid v_i ; \boldsymbol{\Theta}\right)$ にしたがってノードをサンプル抽出することができる. すでに述べたように, より正確に言えば, 生成器の過程は偽のノードを生成するのではなく, ノード集合全体 $\mathcal{V}$ からノードを選択することである.

のソフトマックス関数は確率分布をモデル化する直感的な方法である一方で, 計算量の問題が深刻である. 具体的には,  $\mathcal{V}$ にわたって総和を取る必要があるため, の分母の計算量はとてつもなく大きい. この問題に対処するため, で導入したような階層的ソフトマックス(Morin and Bengio, 2005; Mikolov et al., 2013) を使うこともできる.

#### 識別器

識別器は2値分類器としてモデル化され, 与えられたノードの組 $(v_j, v_i)$ がグラフ中でつながっているかどうかを判別する. 具体的には,  $D\left(v_j, v_i ; \mathbf{\Phi}\right)$ はノード $v_j$ と $v_i$ 間のエッジの存在確率をモデル化する:

 

$$
 D\left(v_j, v_i ; \boldsymbol{\Phi}\right)=\sigma\left(\boldsymbol{\phi}_j^{\top} \boldsymbol{\phi}_i\right)=\frac{1}{1+\exp \left(-\boldsymbol{\phi}_j^{\top} \boldsymbol{\phi}_i\right)} $$


 

ここで,  $\boldsymbol{\phi}\_i \in \mathbb{R}^{d}$ はこの識別器に固有の, ノード $v_i$ の低次元表現である. この表記 $\boldsymbol{\Phi}$ は, 学習する識別器のパラメータである, 全ノードの表現をまとめて表すために用いている. 学習後, 生成器と識別器の両方, あるいはそれらの組み合わせによるノード表現は後段のタスクに利用することができる.

### グラフ生成のためのGAN

敵対的生成ネットワークは(De Cao and Kipf, 2018)でグラフ生成に適用された. 具体的には, GANは分子グラフを生成するのに応用されている. と同様に, ノード数 $N$ の分子グラフ $\mathcal{G}$ は2つの要素から構成される: 1つ目は, ノードの種類を表す行列 $\mathbf{F} \in\{0,1\}^{N \times t_e}$ である. 行列 $\mathbf{F}$ の $i$ 行目は $i$ 番目のノードに対応し,  $t_n$ はノードの種類(ここでは異なる原子）を表す. 2つ目は, エッジーの種類を表すテンソル $\mathbf{E} \in\{0,1\}^{N \times N \times t_e}$ で,  $t_e$ はエッジの種類（ここではことなる結合）を表す. 生成器の目的は, 与えられた分子集合に類似した分子グラフを生成するだけでなく, 生成された分子の溶解度など特定の物性を最適化することである. したがって, GAN のフレームワークには生成器と識別器の他に, 判定器も存在する. これは, 生成されたグラフが特定の物性の観点からどの程度優れているかを測定する(学習時に報酬が与えられる). 判定器は正解がわかっている他の分子について事前学習済のネットワークである. 望ましいグラフを生成するための指針としてのみ使われる. 学習過程においては, 生成器と識別器は互いに競い合いながら学習される. しかい, 判定器は固定されブラックボックスとして扱われる. 具体的には, 生成器と識別器は次の2人用のミニマックス・ゲームをしていることになる:

 $$
 \min_{\boldsymbol{\Theta}} \max_{\boldsymbol{\Phi}} \mathbb{E}_{\mathcal{G} \sim p_{\text {data }}(\mathcal{G})}[\log D(\mathcal{G} ; \boldsymbol{\Phi})]+\mathbb{E}_{\mathbf{z} \sim p(\mathbf{z})}[\log (1-D(G(\mathbf{z} ; \boldsymbol{\Theta})))-\lambda J(G(\mathbf{z} ; \boldsymbol{\Theta}))]    \nonumber $$
 

ここで,  $p_{\text {data }}(\mathcal{G})$ は与えられた分子グラフの真の分布を表し,  $J()$ は判定ネットワークである. 判定ネットワークは, 入力分子グラフの特定の物性を示すスカラー値を出力する(これは最大化されるべきものである). 次に, 本フレームワークにおける生成器, 識別器, そして判定器について述べる.

#### 生成器

生成器 $G(\mathbf{z} ; \boldsymbol{\Theta})$ はで導入したものと同様で, ノイズ分布 $p(\mathbf{z})=\mathcal{N}(0, \mathbf{I})$ からサンプル抽出された潜在表現 $\mathbf{z}$ について生成される, 全結合の確率的ネットワークである. 具体的には, 生成器 $G(\mathbf{z} ; \boldsymbol{\Theta})$ は潜在表現 $\mathbf{z}$ を2つの密な要素に写像する. 生成された, ノード数 $k$ のグラフを次のように特徴づける:  $\tilde{\mathbf{E}} \in \mathbb{R}^{k \times k \times t_e}$ はエッジの種類の確率分布を表し,  $\tilde{\mathbf{F}} \in \mathbb{R}^{k \times t_n}$ はノードの種類の確率分布を表す. 分子グラフを生成するためには,  $\tilde{\mathbf{E}}$ と $\tilde{\mathbf{F}}$ から離散行列 $\mathbf{E}$ と $\mathbf{F}$ がそれぞれサンプル抽出される. 学習過程では, 連続的な確率的グラフ $\tilde{\mathbf{E}}, \tilde{\mathbf{F}}$ が利用され, 逆伝播で勾配を得ることができる.

#### 識別器と判定器

識別器も判定器も, グラフ $\mathcal{G}=\{\mathbf{E}, \mathbf{F}\}$ を入力として受け取り, スカラー値を出力する. (De Cao and Kipf, 2018)では, これら2つの要素をモデル化するのにグラフニューラルネットワークが用いられた. 具体的には, 入力グラフ $\mathcal{G}$ のグラフ表現は次のように得られる:

 $$
 \mathbf{h}_{\mathcal{G}}=\operatorname{pool}(\mathrm{GNN}(\mathbf{E}, \mathbf{F}))
    \nonumber $$
 

ここで, GNN()はいくつかのグラフフィルターが積み重なっており, pool()はグラフプーリング操作を表す. (De Cao and Kipf, 2018)では, で紹介したゲート付きグラフプーリングが使われており, グラフ表現 $\mathbf{h}\_{\mathcal{G}}$ を生成している. グラフ表現は次に2, 3の全結合層に送られ, スカラー値を生成する. 特に, 識別器では, 生成されたスカラー値は0から1の値をとり, 生成されたグラフが本物の与えられたグラフ集合に属する分子グラフである確率を表す. また, 判別器はそのグラフの特定の物性を表すスカラー値を出力する. 識別器は生成器と交互に学習される. しかし, 判定器は別の分子グラフによって事前学習されており, GANのフレームワークの学習中は固定されたブラックボックスとして扱われる.


[メインページ](../../index.markdown)

[章目次](./chap9.md)

[前の節へ](./subsection_04.md) [次の節へ](./subsection_06.md)


