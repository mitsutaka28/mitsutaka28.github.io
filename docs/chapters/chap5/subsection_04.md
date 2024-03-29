[メインページ](../../index.markdown)

[章目次](./chap5.md)
## 5.4. グラフプーリング

グラフフィルタは，グラフ構造を変えることなくノードの特徴を集約する． フィルタ操作後には各ノードは新しい特徴表現を持つことになる． 通常，こうしたノード表現を活用するノードに着目したタスクに対しては，グラフフィルタ操作で十分な場合が多い． しかしながら，グラフ全体に着目したタスクでは，「グラフ全体の表現」が必要になってくる． このような表現を得るためには，ノードからの情報を集約しそれをまとめる必要がある． グラフ(全体の)表現を生成するために重要となってくる情報は大きく分けて2種類ある． 1つ目は「ノードの特徴量が持つ情報」であり，2つ目は「グラフ構造が持つ情報」である． グラフ表現はこの両者の情報を持っていることが期待される．

そこで，従来の畳込みニューラルネットワークと同様に，"グラフプーリング層"が提案されており，これを用いてグラフ全体の表現を生成する． 初期に考案されたグラフプーリング層は平坦グラフプーリング(Flat Graph pooling)である．これは，1つのステップで全てのノード表現からグラフ全体の表現を生成するという意味で，平坦(flat)なプーリングである． 例えば，平均プーリング層や最大プーリング層は，各特徴チャンネルごとに導入することで，グラフニューラルネットワークでも応用できる． その後，元のグラフを段階的に粗く(縮小)することでグラフ情報を集約する，階層的グラフプーリング(Hierarchical Graph Pooling)が考案された． 階層的グラフプーリングの設計では，通常，複数のグラフプーリング層を考え，それぞれがいくつかに積み重なったグラフフィルタ層の後に続くように設計される（図5.5）． 通常，1つの（平坦・階層的）グラフプーリング層は，グラフを入力とし，「粗化されたグラフ(coarsened graph)」を出力する． このプロセスは，式(5.2)でまとめていたことを思い出そう．以下に再掲する：

 $$
 \symbf{A}^{(\mathrm{op})}, \symbf{F}^{(\mathrm{op})}=\operatorname{pool}\left(\symbf{A}^{(\mathrm{ip})},\, \symbf{F}^{(\mathrm{ip})}\right)
    
\tag{5.41} $$
 

まずは平坦グラフプーリングから紹介し，その次に階層的グラフプーリングを解説していくことにする．

### 平坦グラフプーリング

平坦グラフプーリング層では，ノード表現から直接グラフ全体の表現を生成する． この層では新しいグラフは生成されず，ノードが一つだけ生成される． したがって，平坦グラフプーリング層でのプーリング操作は式(5.41)の表現を使って次のようにまとめることができる：

 

$$
 \symbf{f}_G=\operatorname{pool}\left(\symbf{A}^{(\mathrm{ip})}, \symbf{F}^{(\mathrm{ip})}\right) \nonumber $$


 

ここで， $\symbf{f}\_{\symcal{G}} \in \mathbb{R}^{1 \times d_{\text {op }}}$ はグラフ表現である．

代表的ないくつかの平坦プーリング層を紹介しよう． まず従来のCNNにおける最大プーリングと平均プーリングは，GNNについても適用することができる． 具体的には，グラフにおける最大プーリング操作は次のように書くことができる：

 

$$
 \symbf{f}_G=\max \left(\symbf{F}^{(\mathrm{ip})}\right) \nonumber $$


 

この計算では，各チャンネルに対して次のように最大値を取得している：

 

$$
 \symbf{f}_G[i]=\max \left(\symbf{F}_{:, i}^{(\mathrm{ip})}\right) \nonumber $$


 

ここで， $\symbf{F}\_{:, i}^{(\mathrm{ip})}$ は $\symbf{F}^{(\mathrm{ip})}$ の $i$ 番目のチャンネルである． 同様に，グラフにおける平均プーリング操作もチャンネルごとに適用することができる：

 

$$
 \symbf{f}_G=\mathrm{ave}\left(\symbf{F}^{(\mathrm{ip})}\right) \nonumber $$


 

Li *et al*. (2015)では，"ゲート付きグローバルプーリング"と呼ばれる，アテンション機構を用いた平坦プーリング操作が提案されている． 各ノードの重要度を測定するアテンションスコアを用いて，ノード表現を集約し，グラフ表現を生成する． 具体的には，アテンションスコアは次のように計算される：

 

$$
 s_i=\frac{\exp \left(h\left(\symbf{F}_i^{(\mathrm{ip})}\right)\right)}{\sum_{v_j \in \symcal{V}} \exp \left(h\left(\symbf{F}_j^{(\mathrm{ip})}\right)\right)} \nonumber $$


 

ここで， $h$ は $\symbf{F}^{(\mathrm{ip})}$ をスカラー値に変換する順伝搬型ネットワークであり， その後ソフトマックス関数を用いて正規化される． 学習されたアテンションスコアを用いることで，ノード表現を集約したグラフ表現が得られる：

 

$$
 \symbf{f}_G=\sum_{v_i \in \symcal{V}} s_i \cdot \tanh \left(\symbf{F}_i^{(\mathrm{ip})} \boldsymbol{\Theta}_{i p}\right) \nonumber $$


 

ここで， $\boldsymbol{\Theta}\_{i p}$ は学習対象のパラメータであり，活性化関数 $\tanh(\cdot)$ は恒等関数でもよい．

いくつかの平坦グラフプーリング操作は，フィルタ層の設計に組み込まれている． 例えば，Li *et al*. (2015)では，すべてのノードとつながっている「偽(fake)」ノードがグラフに追加される． この「偽」ノードの表現はフィルタ操作の過程で学習することができ，その表現は，グラフ内の全ノードに接続されているので，グラフ全体の情報を捉えることができる． したがって，このような「偽」ノードの表現をグラフ表現として活用することもできる．

### 階層的グラフプーリング

平坦プーリング層は通常，ノード表現を集約してグラフ表現を生成する際，階層的なグラフ構造の情報を無視する． 一方，階層的グラフプーリング層は，グラフ表現の生成に至るまで段階的にグラフを粗くすることによって，階層的なグラフ構造情報を保持することを目指す．

階層的グラフプーリング層は，どのようにグラフを粗くしていくかによって大まかに分類することができる．

1.  **ダウンサンプリング型プーリング.** サブサンプリング（またはダウンサンプリング） [^8]
    によってグラフを粗くする．つまり，重要なノードを選び，それらを粗化されたグラフのノードとする．

2.  **スーパーノード型プーリング.** 入力グラフのノードを統合して「スーパーノード(supernode)」を構成し，それらを用いて粗化されたグラフを生成する．

この2種類の階層的グラフプーリングの主な違いは，ダウンサンプリング型では元のグラフのノードを保持するのに対し，スーパーノード型では粗化されたグラフ用に新しくノードを生成するという点である．

ここからは，これら2種類のプーリングにおける代表的な手法を紹介する． 特に，粗化されたグラフ $\symbf{A}^{(\mathrm{op})}$ とノード特徴量 $\symbf{F}^{(\mathrm{op})}$ がどのように生成されるかを説明することで，式(5.41)の階層的プーリング層の過程を詳しく説明する．

#### ダウンサンプリング型プーリング

入力グラフを粗化するため，ある重要度に応じて $N_{\mathrm{op}}$ 個のノードを選び，これらのノードについてのグラフ構造やノードの特徴を用いて粗化グラフとする． ダウンサンプリング型プーリングは3つの重要な要素から成る．

1.  ダウンサンプリングの基準とする量を計算する．

2.  粗化グラフのグラフ構造（隣接行列）を生成する．

3.  粗化グラフ内のノードが持つ特徴量を生成する．

これら3つの要素の設計がダウンサンプリング型の各手法によって異なることが多い．

代表的なダウンサンプリング型グラフプーリングについて説明する． Gao and Ji (2019)で提案された"gPool"という方法は，グラフの粗化のために初めて導入されたダウンサンプリング手法である． まずgPoolにおけるノードの重要度は，入力ノードの特徴 $\symbf{F}^{(\mathrm{ip})}$ から次のように学習される．

 $$
 \symbf{y}=\frac{\symbf{F}^{(\mathrm{ip})} \symbf{p}}{\|\symbf{p}\|}
    
\tag{5.42} $$
 

ここで， $\symbf{F}^{(\mathrm{ip})} \in \mathbb{R}^{N_{\mathrm{ip}} \times d_{\mathrm{ip}}}$ は入力ノードの特徴を表す行列であり， $\symbf{p} \in \mathbb{R}^{d_{\mathrm{ip}}}$ は学習対象のベクトルである． このべクトルは入力の特徴を重要度スコアに射影する． 重要度スコア $\symbf{y}$ を計算した後は，ノードをスコアに基づいてランク付けし，重要度の高い $N_{\mathrm{op}}$ 個のノードを選び出す：

 

$$
 \mathrm{idx}=\operatorname{rank}\left(\symbf{y},\, N_{\mathrm{op}}\right) \nonumber $$


 

ここで， $N_{\mathrm{op}}$ は粗化グラフのノード数で，idxはその選ばれた上位 $N_{\mathrm{op}}$ 個のノードを指し示すインデックス(要素番号)である．

これらの選択されたノードをそのインデックスで表現することで，粗化されたグラフのグラフ構造とノード特徴を生成することができる． 具体的には，粗化グラフのグラフ構造（隣接行列）は，元の入力グラフ構造（隣接行列）から次のように生成することができる：

 

$$
 \symbf{A}^{(\mathrm{op})}=\symbf{A}^{(\mathrm{ip})}(\mathrm{idx},\, \mathrm{idx}) \nonumber $$


 

ここで， $\symbf{A}^{(\mathrm{ip})}(\mathrm{idx},\, \mathrm{idx})$ は $\symbf{A}$ からidxに対応する行と列を抜き出す操作を表す． 同様に，ノードの特徴も入力ノードの特徴から抜き出すことができる．

Gao and Ji (2019)では，入力の特徴から新しい特徴への情報の流れを制御するためにゲート構造が導入されている． 具体的には，重要度の高いノードがより多くの情報を粗化グラフに流せるようになっており，次のようにモデル化することができる：

 

$$

\begin{aligned}
    \tilde{\symbf{y}} &=\sigma(\symbf{y}(\mathrm{idx})) \nonumber \\ 
    \tilde{\symbf{F}} &=\symbf{F}^{(\mathrm{ip})}(\mathrm{idx},:) \nonumber \\ 
    \symbf{F}_p &=\tilde{\symbf{F}} \odot\left(\tilde{\symbf{y}}\,\symbf{1}_{d_{\mathrm{ip}}}^{\top}\right) \nonumber
\end{aligned}
$$

 

ここで， $\sigma(\cdot)$ はシグモイド関数であり，重要度スコアを(0, 1)に変換する．  $\symbf{1}\_{d_{\mathrm{ip}}} \in \mathbb{R}^{d_{\mathrm{ip}}}$ はすべての要素が1のベクトルである．  $\symbf{y}(\mathrm{idx})$ は $\symbf{y}$ からidxに対応する要素を抜き出し， $\symbf{F}^{(\mathrm{ip})}(\mathrm{idx})$ はidxに対応する行を取得する．

ここまで見てきたように，gPoolでは，式(5.42)のように，入力の特徴量のみに基づいて重要度スコアが学習されている． 一方，Lee *et al*. (2019)では，この段階でグラフ構造を組み込むため，GCNフィルタが用いられた． 具体的には，重要度スコアは次のように計算される．

 

$$
 \symbf{y}=\alpha\left(\mathrm{GCN}\text{-}\mathrm{Filter}\left(\symbf{A}^{(\mathrm{ip})}, \symbf{F}^{(\mathrm{ip})}\right)\right) $$


 

ここで， $\alpha$ は $\tanh$ などの活性化関数である． なお， $\symbf{y}$ は行列ではなくベクトルである． つまり，GCNフィルタの出力チャンネル数は1に設定されている． このグラフプーリング操作は"SAGPool"と呼ばれる．

#### スーパーノード型プーリング

ダウンサンプリング型の階層的グラフプーリングは，ある重要度に基づいてノードを選択し，入力グラフを粗化する方法だった． その過程で，選ばれなかったノードは捨てられるのでそれらの情報は失われてしまう． それに対してスーパーノード型プーリングでは，スーパーノードを生成することによって入力グラフの粗化を試みる． 具体的には，入力グラフのノードを異なるクラスターに割り当てる方法を学習し，そのクラスターをスーパーノードとして取り扱う． これらのスーパーノードが粗化グラフにおけるノードとして扱われることになる． スーパーノード間のエッジとスーパーノードの特徴を生成することで，粗化グラフを構成する． スーパーノード型のグラフプーリングには3つの重要な要素から成る．

1.  粗化グラフのノードとしてスーパーノードを生成する

2.  粗化グラフのグラフ構造（隣接行列）を生成する

3.  粗化グラフの内のノードが持つ特徴量を生成する

ここからは，スーパーノード型プーリングの代表的ないくつかの手法を紹介していく．

**diffpool**

diffpoolのアルゴリズムは，微分可能な方法で(ひいては学習可能となる方法で)スーパーノードを生成する． 具体的には，GCNフィルタを用いて入力グラフのノードから，特定のスーパーノードに割り当てられる確率的な行列を学習する．

 $$
 \symbf{S}=\operatorname{softmax}\left(\mathrm{GCN}\text{-}\mathrm{Filter}\left(\symbf{A}^{(\mathrm{ip})}, \symbf{F}^{(\mathrm{ip})}\right)\right)
    
\tag{5.44} $$
 

ここで， $\symbf{S} \in \mathbb{R}^{N_{\mathrm{ip}} \times N_{\mathrm{op}}}$ は学習される行列である． 式(5.3)で示したように， $\symbf{F}^{(\mathrm{ip})}$ は直近のグラフフィルタ層の出力である． しかし，Ying *et al*.(2018c)では，プーリング層の入力はその前のプーリング層の出力（つまり，学習ブロック $\symbf{F}^{(\mathrm{ib})}$ の入力; 5.2.2節のブロック構造参照)としている．

式(5.44)では1つのフィルタしかないが，いくつかのGCNフィルタを積み重ねて割り当て行列を学習させることもできる． 学習される割り当て行列の各列をスーパーノードとみなすことができる． そして，ソフトマックス関数はこの行列の行ごとに適用されている． そのため，各行は合計が1に規格化されていることになる． この行列の $i$ 行目の $j$ 列目の要素は， $i$ 番目のノードが $j$ 番目のスーパーノードに割り当てる可能性を表している．

割り当て行列 $\symbf{S}$ が得られたら，粗化グラフのグラフ構造とノードの特徴量を生成することができる． 具体的には，この割り当て行列 $\symbf{S}$ を活用することで，粗化グラフのグラフ構造(隣接行列)は次のように生成することができる：

 

$$
 \symbf{A}^{(\mathrm{op})}=\symbf{S}^{\top} \symbf{A}^{(\mathrm{ip})} \symbf{S} \in \mathbb{R}^{N_{\mathrm{op}} \times N_{\mathrm{op}}}\nonumber $$


 

同様に，スーパーノードの特徴量は，割り当て行列 $\symbf{S}$ に従って入力グラフのノード特徴量を線型結合することによって計算することができる：

 

$$
 \symbf{F}^{(\mathrm{op})}=\symbf{S}^{\top} \symbf{F}^{(\text{inter})} \in \mathbb{R}^{N_{\mathrm{op}} \times d_{\mathrm{op}}} \nonumber $$


 

ここで， $\symbf{F}^{(\text{inter})} \in \mathbb{R}^{N_{\mathrm{ip}} \times d_{\mathrm{op}}}$ は，以下のようにGCNフィルタを通じて学習された中間的な特徴量である：

 $$
 \symbf{F}^{(\text{inter})}=\mathrm{GCN}\text{-}\mathrm{Filter}\left(\symbf{A}^{(\mathrm{ip})},\, \symbf{F}^{(\mathrm{ip})}\right)
    
\tag{5.45} $$
 

式(5.45)では1つのフィルタのみだが，複数のGCNフィルタを積み重ねることもできる． diffpoolのプロセスは以下のようにまとめることができる：

 

$$
 \symbf{A}^{(\mathrm{op})},\, \symbf{F}^{(\mathrm{op})}=\operatorname{diffpool}\left(\symbf{A}^{(\mathrm{ip})}, \symbf{F}^{(\mathrm{ip})}\right) \nonumber $$


 

**EigenPooling**

EigenPooling (Ma *et al*., 2019b)はスペクトルクラスタリングを用いてスーパーノードを生成するアルゴリズムにより，粗化グラフのグラフ構造とノードの特徴量を形成することに焦点を当てている． スペクトルクラスタリングの適用後には，重複のないクラスターの組が得られ， これらのクラスターを粗化グラフのスーパーノードとみなすことができる． 入力グラフのノードとスーパーノードの対応を表す行列は $\symbf{S} \in\{0,1\}^{N_{\mathrm{ip}} \times N_{\text{op}}}$ と表現される． ここで，各行について1つの要素だけが1であり，その他の要素は0である． 具体的にいえば， $i$ 番目のノードが $j$ 番目のスーパーノードに割り当てられているときのみ， $S_{i, j}=1$ となる．

 $k$ 番目のスーパーノードについて，対応する「クラスター内のグラフ構造」を記述するために $\symbf{A}^{(k)} \in \mathbb{R}^{N^{(k)} \times N^{(k)}}$ という表記を用いる． ここで， $N^{(k)}$ は対応するクラスター内のノードの数である． さらに，サンプリング演算子 $\symbf{C}^{(k)} \in\{0,1\}^{N_{\mathrm{ip}} \times N^{(k)}}$ を次のように定義する：

 

$$
 \symbf{C}_{i,\, j}^{(k)}=1 \quad \text { if and only if } \quad \Gamma^{(k)}(j)=v_i \nonumber $$


 

ここで， $\Gamma^{(k)}$ は $k$ 番目のクラスターのノード一覧を表し， $\Gamma^{(k)}(j)=v_i$ は，ノード $v_i$ がこのクラスター内の $j$ 番目のノードに対応していることを意味する． このサンプリング演算子を用いると， $k$ 番目のクラスターの隣接行列は，次のように定義することができる：

 

$$
 \symbf{A}^{(k)}=\left(\symbf{C}^{(k)}\right)^{\top} \symbf{A}^{(\mathrm{ip})} \symbf{C}^{(k)} \nonumber $$


 

次に，粗化グラフのグラフ構造とノードの特徴量を生成するプロセスについて説明する． スーパーノード間のグラフ構造を構成するためには，元のグラフのクラスター間のつながりのみを考慮する． そのためにまずは，各クラスター内のノード間のエッジのみで構成される「クラスター内隣接行列」を生成する：

 

$$
 \symbf{A}_{\text{int}}=\sum_{k=1}^{N_{\mathrm{op}}} \symbf{C}^{(k)} \symbf{A}^{(k)}\left(\symbf{C}^{(k)}\right)^{\top}\nonumber $$


 

そして，各クラスター間をつなぐエッジのみで構成される「クラスター間隣接行列」は  $\symbf{A}\_{\text{ext}}=\symbf{A}-\symbf{A}\_{\text{int}}$ と書くことができる． 以上から粗化グラフの隣接行列は次のように得られる：

 

$$
 \symbf{A}^{\mathrm{op}}=\symbf{S}^{\top} \symbf{A}_{\text{ext}} \symbf{S}\nonumber $$


 

ノードの特徴を生成するためにはグラフフーリエ変換を用いる． 具体的には，各部分グラフ（ここではクラスター）のグラフ構造とノードの特徴量を用いて，対応するスーパーノードのの特徴量を生成する． まず， $k$ 番目のクラスターを例としてそのプロセスを説明する．  $\symbf{L}^{(k)}$ をこの部分グラフのラプラシアン行列とし，  $\symbf{u}\_1^{(k)}, \ldots,\, \symbf{u}\_{n^{(k)}}^{(k)}$ を対応する固有ベクトルとする． この部分グラフのノードの特徴量はサンプリング演算子 $\symbf{C}^{(k)}$ を用いることで $\symbf{F}^{(\mathrm{ip})}$ から次のように抜き出すことができる．

 

$$
 \symbf{F}_{\mathrm{ip}}^{(k)}=\left(\symbf{C}^{(k)}\right)^{\top} \symbf{F}^{(\mathrm{ip})} \nonumber $$


 

ここで， $\symbf{F}\_{\mathrm{ip}}^{(k)} \in \mathbb{R}^{N^{(k)} \times d_{\mathrm{ip}}}$ は $k$ 番目のクラスターのノードの入力特徴量である． 次に，グラフフーリエ変換を適用することで， $\symbf{F}\_{\mathrm{ip}}^{(k)}$ のすべてのチャンネルに関するグラフフーリエ係数を得る：

 

$$
 \symbf{f}_i^{(k)}=\left(\symbf{u}_i^{(k)}\right)^{\top} \symbf{F}_{\mathrm{ip}}^{(k)} \quad \text { for } \quad i=1, \ldots,\, N^{(k)} \nonumber $$


 

ここで， $\symbf{f}\_i^{(k)} \in \mathbb{R}^{1 \times d_{\mathrm{ip}}}$ はすべての特徴量チャンネルについての $i$ 番目のグラフフーリエ係数から成る．  $k$ 番目のスーパーノードの特徴量は，これらの係数を結合することで得られる：

 

$$
 \symbf{f}^{(k)}=\left[\symbf{f}_1^{(k)}, \ldots, \symbf{f}_{N^{(k)}}^{(k)}\right] \nonumber $$


 

スーパーノードの特徴を生成するために，最初の数個の係数のみを利用する場合が多いが，これには2つの理由がある． 第一に，異なる部分グラフのノード数はそれぞれ異なるため，同じ次元の特徴を確保するためには，いくつかの係数を捨てる必要がある． 第二に，現実にはグラフ信号の大部分は滑らかであるため，最初のいくつか係数は重要な情報のほとんどを捉えている場合が多い．


[メインページ](../../index.markdown)

[章目次](./chap5.md)

[前の節へ](./subsection_03.md) [次の節へ](./subsection_05.md)

[^8]: 訳注：「サブサンプリング」は，元のデータセットから更に小さなデータセット（サブセット）を選択するプロセスを指す．一方，「ダウンサンプリング」はデータの解像度を下げる（つまりデータ量を減らす）プロセスを指す．本文脈においては，どちらのプロセスもデータ量を減らすという意味で区別せずに用いている．
