[メインページ](../../index.markdown)

[章目次](./chap9.md)
## 9.4. グラフ上でのVAE

変分オートエンコーダー（Variational Autoencoder, VAE)は生成モデルの一種であり, データセット $\mathcal{X}=\left\\{\mathbf{x}\_1, \ldots, \mathbf{x}\_N\right\\}$ について確率分布のモデル化することを目的とする. また, 潜在変数モデルでもあり, 潜在変数からデータサンプルを生成する. 標準正規分布 $p(\mathbf{z})$ からサンプル抽出された潜在変数 $\mathbf{z}$ が与えられたとき, これと同様のサンプルデータを以下の確率で生成できる潜在モデルを学習することを考える:

 $$
 p(\mathbf{x} \mid \mathbf{z} ; \boldsymbol{\Theta})=\mathcal{N}\left(\mathbf{x} \mid f(\mathbf{z} ; \boldsymbol{\Theta}), \sigma^{2} \cdot \mathbf{I}\right)
    
\tag{9.9} $$
 

ここで,  $\mathcal{N}\left(\mathbf{x} \mid f(\mathbf{z} ; \boldsymbol{\Theta}), \sigma^{2} \cdot \mathbf{I}\right)$ は,  $f(\mathbf{z} ; \boldsymbol{\Theta})$ を平均,  $\sigma^{2} \cdot \mathbf{I}$ を共分散行列とするガウス分布を表す.  $\boldsymbol{\Theta})$ は学習するパラメータであり,  $\mathbf{x}$ は与えられたデータと同じ分野の, 生成されたサンプルデータである. 例えば, 入力データが画像の場合, イメージを生成することを考える.  $f(\mathbf{z} ; \boldsymbol{\Theta})$ は（確率的ではない）決定論的関数であり, 潜在変数 $\mathbf{z}$ をの生成モデルの確率の平均値に変換する. なお, 実際には生成されるデータサンプルの確率分布はガウス分布である必要はなく, 特定の用途に応じて他の分布でもよい. ここでは便宜上, コンピュータービジョンの分野でのタスクで画像を生成する際に用いられるガウス分布を例として話を進める. の生成モデルが与えられたデータ $\mathcal{X}$ を代表しているかを確かめるため,  $\mathcal{X}$ 中のデータサンプル $\mathbf{x}\_i$ についての次のような対数尤度を最大化する必要がある:

 $$
 \log p\left(\mathbf{x}_i\right)=\log \int p\left(\mathbf{x}_i \mid \mathbf{z} ; \boldsymbol{\Theta}\right) p(\mathbf{z}) d \mathbf{z} \text { for } \mathbf{x}_i \in X
    
\tag{9.10} $$
 

しかし, の積分は扱いが難しい. さらに, 真の事後分布 $p(\mathbf{z} \mid \mathbf{x} ; \Theta)$ も厄介で, これによってEMアルゴリズムを適用できない. この問題に対応するため, 推論モデル $q(\mathbf{z} \mid \mathbf{x} ; \mathbf{\Phi})$ が導入された(Kingma and Welling, 2013). これはパラメータ $\mathbf{\Phi}$ を用いて, 先の厄介な真の事後確率分布 $p(\mathbf{z} \mid \mathbf{x} ; \Theta)$ を近似する. 通常は,  $q(\mathbf{z} \mid \mathbf{x} ; \mathbf{\Phi})$ はガウス分布 $q(\mathbf{z} \mid \mathbf{x} ; \mathbf{\Phi})=\mathcal{N}(\mu(\mathbf{x} ; \mathbf{\Phi}), \Sigma(\mathbf{x} ; \mathbf{\Phi}))$ でモデル化され, 平均と共分散行列は $\mathbf{\Phi}$ をパラメータにとる決定論的関数を通じて学習される. そして, の対数尤度は次のように書き換えることができる:

 $$
 \log p\left(\mathbf{x}_i\right)=D_{K L}(q(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\Phi}) \| p(\mathbf{z} \mid \mathbf{x} ; \boldsymbol{\Theta}))+\mathcal{L}\left(\boldsymbol{\Theta}, \mathbf{\Phi} ; \mathbf{x}_i\right)
    \nonumber $$
 

ここで,  $\mathcal{L}\left(\boldsymbol{\Theta}, \boldsymbol{\Phi} ; \mathbf{x}\_i\right)$ は, 近似分布と真の事後分布のKLダイバージェンス（右辺第1項）が非負であることから,  $\mathbf{x}\_i$ の対数尤度の変分下限と呼ばれる. 具体的には, 変分下限は次のように書くことができる:

 $$
 \mathcal{L}\left(\boldsymbol{\Theta}, \boldsymbol{\Phi} ; \mathbf{x}_i\right)=\underbrace{\mathbb{E}_{q\left(\mathbf{z} \mid \mathbf{x}_{\mathbf{i}} \boldsymbol{\Phi}\right)}\left[\log p\left(\mathbf{x}_i \mid \mathbf{z} ; \boldsymbol{\Theta}\right)\right]}_{\text {reconstruction }}-\underbrace{D_{\text {KL }}\left(q\left(\mathbf{z} \mid \mathbf{x}_{\mathbf{i}} ; \boldsymbol{\Phi}\right) \| p(\mathbf{z})\right)}_{\text {regularization }}
    
\tag{9.11} $$
 

 $\mathcal{X}$ 中のデータサンプルについて, の対数尤度を最大化する代わりに, の変分下限を $\boldsymbol{\Theta}$ と $\boldsymbol{\Phi}$ に対して微分して最大化する. なお, 変分下限のマイナス $-\mathcal{L}\left(\boldsymbol{\Theta}, \boldsymbol{\Phi} ; \mathbf{x}\_i\right)$ を最小化することは, で紹介した古典的なオートエンコーダーの処理に似ているので, このモデルは「変分オートエンコーダー」と呼ばれる. 具体的には, 右辺の第1項は再構成の過程とみなすことができ,  $q\left(\mathbf{z} \mid \mathbf{x}\_{\mathbf{i}} ; \mathbf{\Phi}\right)$ はエンコーダー(推論モデル)で $p\left(\mathbf{x}\_i \mid \mathbf{z} ; \boldsymbol{\Theta}\right)$ はデコーダー(生成モデル)である. エンコーダーが入力を表現に変換する, 古典的なオートエンコーダーと違って, このエンコーダー $q\left(\mathbf{z} \mid \mathbf{x}\_{\mathbf{i}} ; \mathbf{\Phi}\right)$ は入力 $\mathbf{x}\_i$ を潜在ガウス分布に変換する. 右辺の第1項を最大化することは, 入力 $\mathbf{x}\_i$ とデコードされた $p\left(\mathbf{x}\_i \mid \mathbf{z} ; \boldsymbol{\Theta}\right)$ の平均 $f(\mathbf{z} ; \boldsymbol{\Theta})$ の差を最小化することとみなすことができる. 同時に, 右辺の第2項は正則化項とみなすことができ, 近似された事後分布 $q\left(\mathbf{z} \mid \mathbf{x}\_{\mathbf{i}} ; \boldsymbol{\Phi}\right)$ が事前分布 $p(\mathbf{z})$ に近くなるようにしている. 学習後, 生成モデル $p\left(\mathbf{x}\_i \mid \mathbf{z} ; \boldsymbol{\Theta}\right)$ は与えられたデータに類似したデータサンプルを生成するために利用される(このとき, 潜在変数は標準ガウス分布 $p(\mathbf{z})$ からサンプル抽出される).

### ノード表現学習のためのVAE

(Kipf and Welling, 2016b)では, グラフデータに対して, ノード表現を学習するのに変分オートエンコーダーが用いられた. 推論モデルは各ノードを多変量ガウス分布にエンコードするもので, 全ノードについての同時確率分布は次のようになる:

  

$$

\begin{eqnarray}
    q(\mathbf{Z} \mid \mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi})=\prod_{v_i \in \mathcal{V}} q\left(\mathbf{z}_i \mid \mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi}\right) \nonumber \\
    \text { with } q\left(\mathbf{z}_i \mid \mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi}\right)=\mathcal{N}\left(\mathbf{z}_i \mid \boldsymbol{\mu}_i, \operatorname{diag}\left(\boldsymbol{\sigma}_i^{2}\right)\right)
    
\end{eqnarray}
\tag{9.12}
$$

  

ここで,  $\boldsymbol{\mu}\_i$ と $\boldsymbol{\sigma}\_i$ は平均と分散を表し, 決定論的グラフニューラルネットワークにより次のようにして得られる:

  

$$

\begin{eqnarray}
    \boldsymbol{\mu}=\mathrm{GNN}\left(\mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi}_{\mu}\right) \nonumber\\ 
    \log \boldsymbol{\sigma}=\mathrm{GNN}\left(\mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi}_{\sigma}\right)\nonumber
\end{eqnarray}
$$

  

ここで,  $\boldsymbol{\mu}$ と $\boldsymbol{\sigma}$ は,  $\boldsymbol{\mu}\_i$ と $\boldsymbol{\sigma}\_i$ がそれぞれ $i$ 番目の行となる行列である.  $\boldsymbol{\Phi}\_{\mu}$ と $\boldsymbol{\Phi}\_{\sigma}$ はでは $\boldsymbol{\Phi}$ としてまとめられている. 具体的に, (Kipf and Welling, 2016b)では, 推論モデルを構築するためのグラフニューラルネットワークモデルとして, GCNフィルターが使われている. グラフの隣接行列を生成する(再構成する)生成モデルは, 潜在変数 $\mathbf{Z}$ 間の内積を用いて次のようにモデル化される:

  

$$

\begin{eqnarray}
    p(\mathbf{A} \mid \mathbf{Z})=\prod_{i=1}^{N} \prod_{j=1}^{N} p\left(\mathbf{A}_{i, j} \mid \mathbf{z}_i, \mathbf{z}_j\right) \nonumber\\ 
    \text { with } p\left(\mathbf{A}_{i, j}=1 \mid \mathbf{z}_i, \mathbf{z}_j\right)=\sigma\left(\mathbf{z}_i^{\top} \mathbf{z}_j\right)\nonumber
\end{eqnarray}
$$

  

ここで,  $\mathbf{A}\_{i, j}$ は隣接行列 $\mathbf{A}$ の $i,j$ 番目の要素を表し,  $\sigma(\cdot)$ はシグモイド関数である. なお, 生成モデルにはパラメータが存在しないことに注意されたい. 推論モデルの変分パラメータは, 次の変分下限を最適化することで得られる.

 $$
 \mathcal{L}=\mathbb{E}_{q(\mathbf{Z} \mid \mathbf{X}, \mathbf{A} ; \boldsymbol{\Phi})}[\log p(\mathbf{A} \mid \mathbf{Z})]-\operatorname{KL}[q(\mathbf{Z} \mid \mathbf{X}, \mathbf{A} ; \mathbf{\Phi}) \| p(\mathbf{Z})]    \nonumber $$
 

ここで,  $p(\mathbf{Z})=\prod_i p\left(\mathbf{z}\_{\mathbf{i}}\right)=\prod_i \mathcal{N}\left(\mathbf{z}\_i \mid 0, \mathbf{I}\right)$ は潜在変数 $\mathbf{Z}$ を変数に持つガウス型の事前分布である.

### グラフ生成のためのVAE

グラフ生成のタスクにおいては, グラフ集合 $\left\\{\mathcal{G}\_i\right\\}$ が与えられ, それらに似ているグラフを生成しようとする. 変分オートエンコーダーは, 分子グラフなどの小さなグラフを生成するのに用いられてきた(Simonovsky and Komodakis, 2018). 具体的には, グラフ $\mathcal{G}$ について, 推論モデル $q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})$ はグラフを潜在変数の分布に変換する. 同時に, デコーダーは生成モデル $p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})$ で表される.  $\boldsymbol{\Phi}$ と $\boldsymbol{\Theta}$ はいずれもパラメターであり, 次で表される,  $\mathcal{G}$ の対数尤度 $p(\mathcal{G} ; \boldsymbol{\Theta}))$ の変分下限を最適化することで得られる:

 $$
 \mathcal{L}(\Phi, \Theta ; \mathcal{G})=\mathbb{E}_{q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})}[\log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})]-D_{\mathrm{KL}}[q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi}) \| p(\mathbf{z})]    
\tag{9.13} $$
 

ここで,  $p(\mathbf{z})=\mathcal{N}(\mathbf{0}, \mathbf{I})$ は $\mathbf{z})$ についてのガウス型事前分布である. 次に, エンコーダー(推論モデル) $q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})$ とデコーダー(生成モデル) $p(\mathcal{G} \mid \mathbf{z} ; \Theta)$ の詳細を述べ, 最後にどのようにして $\mathbb{E}\_{q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})}[\log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})]$ を評価するか議論する.

#### エンコーダー：推論モデル

(Simonovsky and Komodakis, 2018)では, 少数ノードからなる小さなグラフを生成することだった. 例えば, 分子のグラフは通常とても小さい. さらに, これらのグラフはノードとエッジに属性情報が付与されていることが想定される. 分子のグラフの場合には, ノードとエッジの属性はノードとエッジの種類であることを表し, 1-hotベクトルとしてエンコードされている. 具体的には, グラフ $\mathcal{G}=\{\mathbf{A}, \mathbf{F}, \mathbf{E}\}$ はその隣接行列 $\mathbf{A} \in\{0,1\}^{N \times N}$ , ノード属性 $\mathbf{F} \in\{0,1\}^{N \times t_n}$ , エッジ属性 $\mathbf{E} \in\{0,1\}^{N \times N \times t_e}$ で表すことができる. 通常, 分子のグラフにおいては, ノードの数 $N$ は数十程度である.  $\mathbf{F}$ は各ノードの属性(種類)を示す行列であり,  $t_n$ はノードの種類数である.  $\mathbf{F}$ の $i$ 行目は $i$ 番目のノードの種類を示す1-hotベクトルである. 同様に,  $\mathbf{E}$ はエッジーの種類を示すテンソルで,  $t_e$ はエッジの種類数を表す. なお, グラフは完全ではない場合が多いので, エッジ数が $N\times N$ であることはない. したがって,  $\mathbf{E}$ において, 存在しないエッジに対応する1-hotベクトルは $\mathbf{0}$ ベクトルになる. 与えられたグラフ情報を十分に活用するため, プーリング層をもつグラフニューラルネットワークモデルを利用して, エンコーダーを次のようにモデル化する:

  

$$

\begin{eqnarray}
    &&q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})=\mathcal{N}\left(\boldsymbol{\mu}, \sigma^{2}\right) \nonumber\\
    &&\boldsymbol{\mu}=\operatorname{pool}\left(\mathrm{GNN}_{\mu}(\mathcal{G})\right) ; \quad \log \sigma=\operatorname{pool}\left(\mathrm{GNN}_{\sigma}(\mathcal{G})\right)\nonumber
\end{eqnarray}
$$

  

ここで, 平均と分散はグラフニューラルネットワークモデルによって学習される. 詳細には, で導入したECCフィルターを使ってグラフニューラルネットワークモデルを構築してノード表現を学習し, で導入した, ゲート付きグローバルプーリングを用いてノード表現をプーリングしてグラフ表現を生成する.

#### デコーダー：生成モデル

生成モデルは, 与えられた潜在表現 $\mathbf{z}$ からグラフ $\mathcal{G}$ を生成することを目的とする. 言い換えれば, 3つの行列 $\mathbf{A}$ ,  $\mathbf{E}$ , そして $\mathbf{F}$ を生成する. (Simonovsky and Komodakis, 2018)では, 生成するグラフの大きさ(k)は小さく限定されていた. 具体的には,  $k$ 個のノードからなる, 確率的完全連結グラフ $\widetilde{\mathcal{G}}=\{\widetilde{\mathbf{A}}, \widetilde{\mathbf{E}}, \widetilde{\mathbf{F}}\}$ を出力するように生成モデルを設計していた. この確率的グラフにおいて, ノードとエッジの存在はベルヌーイ分布としてモデル化され, ノードとエッジの種類は多項分布としてモデル化された. 予測された完全連結グラフの隣接行列 $\widetilde{\mathbf{A}} \in[0,1]^{k \times k}$ はノードの存在確率を対角成分 $\widetilde{\mathbf{A}}\_{i,i}$ に含み, エッジの存在確率を非対角成分 $\widetilde{\mathbf{A}}\_{i,j}$ に含んでいる. エッジの種類の確率はテンソル $\widetilde{\mathbf{E}} \in \mathbb{R}^{k \times k \times t_e}$ に含まれている. ノードの種類の確率は行列 $\widetilde{\mathbf{F}} \in \mathbb{R}^{k \times t_n}$ で表される. 生成モデル部分のモデル化には異なるアーキテクチャを使うこともできる. (Simonovsky and Komodakis, 2018)では, シンプルな順伝搬ネットワークモデルが採用されており, 入力は潜在変数 $\mathbf{z}$ , 最後の層で3つの行列を出力する.  $\widetilde{\mathbf{A}}$ を得るのにシグモイド関数が使われており, これがノードとエッジの存在確率を表す.  $\widetilde{\mathbf{E}}$ や $\widetilde{\mathbf{F}}$ を得るのには, それぞれエッジごと, ノードごとのソフトマックス関数が使われている. なお, 得られた確率的グラフ $\widetilde{\mathcal{G}}$ は生成モデルとみなすことができ, 次のように表すことができる:

 $$
 p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})=p(\mathcal{G} \mid \widetilde{\mathcal{G}})
    \nonumber $$
 

ここで,

 $$
 \widetilde{\mathcal{G}}=\operatorname{MLP}(\mathbf{z} ; \boldsymbol{\Theta})
    \nonumber $$
 

である. $MLP()$ は順伝搬ネットワークモデルである.

#### 再構成損失の評価

を最適化するには,  $\mathbb{E}\_{q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})}[\log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})]$ を評価することが残っており, これは入力グラフ $\mathcal{G}$ と再構成した確率的グラフ $\widetilde{\mathcal{G}}$ がどれくらい近いかを評価している, とみなすことができる. グラフには特定のノードの並びが存在しないので, 2つのグラフを比較するのは簡単ではない. (Simonovsky and Komodakis, 2018)では, 最大プーリングマッチングアルゴリズム(Cho et al., 2014b)を用いて,  $\mathcal{G}$ と $\widetilde{\mathcal{G}}$ の対応 $\mathbf{P} \in\{0,1\}^{k \times N}$ を見つける. このアルゴリズムは, 2つのグラフのノード間の類似度に基づいており,  $N$ は $\mathcal{G}$ 中のノード数,  $k$ は $\widetilde{\mathcal{G}}$ 中のノード数である. 具体的には,  $\widetilde{\mathcal{G}}$ の $i$ 番目のノードがもとのグラフの $j$ 番目のノードに対応するときのみ $\mathbf{P}\_{i, j}=1$ であり, それ以外では $\mathbf{P}\_{i, j}=0$ となる. アラインメント行列 $\mathbf{P}$ があれば, 2つのグラフ中の情報を比較できるようにアラインメントすることができる. 入力の隣接行列は $\mathbf{A}^{\prime}=\mathbf{P A} \mathbf{P}^{T}$ と予測グラフに変換することができる一方で, ノードとエッジの種類についての予測は $\widetilde{\mathbf{F}}^{\prime}=\mathbf{P}^{T} \widetilde{\mathbf{F}}, \widetilde{\mathbf{E}}\_{:,: l}^{\prime}=\mathbf{P}^{T} \widetilde{\mathbf{E}}\_{:,:, l} \mathbf{P}$ と入力グラフに変換することができる. そして,  $\mathbb{E}\_{q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})}[\log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})]$ は $q(\mathbf{z} \mid \mathcal{G})$ からサンプル抽出した一つの潜在変数 $\mathbf{z}$ を用いて次のように見積もることができる:

 $$
 \mathbb{E}_{q(\mathbf{z} \mid \mathcal{G} ; \boldsymbol{\Phi})}[\log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})] \approx \log p(\mathcal{G} \mid \mathbf{z} ; \boldsymbol{\Theta})=\log p\left(\mathbf{A}^{\prime}, \mathbf{E}, \mathbf{F} \mid \widetilde{\mathbf{A}}, \widetilde{\mathbf{E}}^{\prime} \widetilde{\mathbf{F}}^{\prime}\right)
    \nonumber $$
 

ここで,  $p\left(\mathbf{A}^{\prime}, \mathbf{E}, \mathbf{F} \mid \widetilde{\mathbf{A}}, \widetilde{\mathbf{E}}^{\prime} \widetilde{\mathbf{F}}^{\prime}\right)$ は次のようにモデル化することができる:

  

$$

\begin{eqnarray}
    &&p\left(\mathbf{A}^{\prime}, \mathbf{E}, \mathbf{F} \mid \widetilde{\mathbf{A}}, \widetilde{\mathbf{E}}^{\prime} \widetilde{\mathbf{F}^{\prime}}\right) \nonumber\\ 
    &&=\lambda_A \log p\left(\mathbf{A}^{\prime} \mid \widetilde{\mathbf{A}}\right)+\lambda_E \log p\left(\mathbf{E} \mid \widetilde{\mathbf{E}}^{\prime}\right)+\lambda_F \log p\left(\mathbf{F} \mid \widetilde{\mathbf{F}}^{\prime}\right)
    
\end{eqnarray}
\tag{9.14}
$$

  

ここで,  $\lambda_{\mathbf{A}}, \lambda_E, \lambda_F$ はハイパーパラメータである. の3項はそれぞれ $\mathbf{A}^{\prime},\mathbf{E}, \mathbf{F}$ の対数尤度であり, それぞれ $\mathbf{A}^{\prime}$ と $\widetilde{\mathbf{A}}$ 間,  $\mathbf{E}$ と $\widetilde{\mathbf{E}}^{\prime}$ 間,  $\mathbf{F}$ と $\widetilde{\mathbf{F}}^{\prime}$ 間のクロスエントロピーのマイナスでモデル化することができる. 詳細には次のように定式化される:

  

$$

\begin{eqnarray}
    \log p\left(\mathbf{A}^{\prime} \mid \widetilde{\mathbf{A}}\right)&=& \frac{1}{k} \sum_{i=1}^{k}\left[\mathbf{A}_{i, i}^{\prime} \log \widetilde{\mathbf{A}}_{i, i}+\left(1-\mathbf{A}_{i, i}^{\prime}\right) \log \left(1-\widetilde{\mathbf{A}}_{i, i}\right)\right] \nonumber\\
    && +\frac{1}{k(k-1)} \sum_{i \neq j}^{k}\left[\mathbf{A}_{i, j}^{\prime} \log \widetilde{\mathbf{A}}_{i, j}+\left(1-\mathbf{A}_{i, j}^{\prime}\right) \log \left(1-\widetilde{\mathbf{A}}_{i, j}\right)\right] \nonumber\\
    \log p\left(\mathbf{E} \mid \widetilde{\mathbf{E}}^{\prime}\right)&=&\frac{1}{\|\mathbf{A}\|_1-N} \sum_{i \neq j}^{N} \log \left(\mathbf{E}_{i, j,:}^{\top} \widetilde{\mathbf{E}}_{i, j,:}\right)\nonumber\\
    \log p\left(\mathbf{F} \mid \widetilde{\mathbf{F}}^{\prime}\right)&=&\frac{1}{N} \sum_{i=1}^{N} \log \left(\mathbf{F}_{i,:}^{\top} \widetilde{\mathbf{F}}_{i,:}^{\prime}\right) \nonumber
\end{eqnarray}
$$

  


[メインページ](../../index.markdown)

[章目次](./chap9.md)

[前の節へ](./subsection_03.md) [次の節へ](./subsection_05.md)


