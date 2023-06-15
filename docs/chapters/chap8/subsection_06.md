[メインページ](../../index.markdown)

[章目次](./chap8.md)
## 8.6. ハイパーグラフのニューラルネットワーク

現実世界の多くの状況では，「対象間が一対一の関係を超える関係性を持つ」という場面が多く存在する． 例えば，論文間の関係を表すグラフでは，ある著者は自身が執筆した $2$ つ以上の論文と関連することがある． このとき「著者」は，複数の「論文（ノード）」と接続する**ハイパーエッジ**とみなすことができる（図2.9参照）． 単純グラフのエッジと比較して，ハイパーエッジは高次元の関係をエンコードすることができる． ハイパーエッジを持つグラフはハイパーグラフと呼ばれる． ハイパーグラフの形式的な定義は2.6.5節で確認せよ．

ハイパーグラフ用のグラフフィルタを設計する上での鍵は，ハイパーエッジによりエンコードされた高次元の関係性を取り扱いやすいようにすることである． 具体的には，これらのハイパーエッジから一対一の関係を抽出し，その結果，ハイパーグラフは単純グラフに変換される．その後，5.3節で紹介された単純グラフ用に設計されたグラフフィルタが適用される(Fent *et al*., 2019b; Yadati *et al*., 2019)． 次に，ハイパーエッジから一対一関係を抽出する代表的な手法をいくつか紹介する．

Feng *et al*.(2019b)における研究では，ハイパーエッジを利用してノード間の一対一の関係が推定される． 2つのノードが少なくとも1つのハイパーエッジ内で同時に現れる場合，それらのノードは「接続している」とみなされる． もし同じノード組が複数のハイパーエッジに現れる場合，それらハイパーエッジそれぞれからの影響は重み付けされながら組み合わされることになる． こうして，「ハイパーエッジを利用したノード組の一対一の関係を表す**隣接行列**」は，次のように定式化される：  

$$
 \tilde{\symbf{A}}_{hy} = \symbf{D}^{-1/2}_v\symbf{H}\symbf{W}\symbf{D}^{-1}_e\symbf{H}^{T}\symbf{D}^{-1/2}_v $$


  ここで，行列 $\symbf{D}_v$ ， $\symbf{H}$ ， $\symbf{W}$ ， $\symbf{D}_e$ は定義2.39で定義されている通りである． つまり， $\symbf{H}$ はノードとハイパーエッジ間の関係を表す接続行列， $\symbf{W}$ はハイパーエッジの重みを記述する対角行列， $\symbf{D}_v$ と $\symbf{D}_e$ はそれぞれノードとハイパーエッジの次数行列である． 上式が得られれば，グラフフィルタをその行列 $\tilde{\symbf{A}}\_{hy}$ で定義された単純グラフに適用することが可能になる． Feng *et al*.(2019b)での研究では，以下のように表現できるGCNフィルタが採用されている：  

$$
 \symbf{F}^{(l)} = \sigma(\tilde{\symbf{A}}_{hy}\symbf{F}^{(l-1)}\symbf{\Theta}^{(l-1)}) $$


  ここで， $\sigma$ は非線形活性化関数である．

ハイパーエッジから一対一関係に変換する別の手法として，Yadati *et al*.(2019)では，Chan *et al*.(2018)で提案された方法を採用している[^1]． そこでは，一対一のエッジを作るために，ノード集合で構成されている各ハイパーエッジ $e$ から，代表とする2つのノードを選択している． この2つのノード $(v_i,v_j)$ は以下のように選択される：  

$$
 (v_i,v_j) := \underset{v_i,v_j\in e}{\operatorname{argmax}}\|\symbf{h}(v_i) - \symbf{h}(v_j)\|^{2}_2 $$


   $\symbf{h}(v_i)$ は，ノード $v_i$ に関連する量(例えば，ノード特徴量)を表している．

グラフニューラルネットワークの設定においては， $(l-1)$ 層で学習した隠れ表現 $\symbf{F}^{(l-1)}$ が， $l$ 層でのノード間の関係を測定するための特徴量となる． そのため，各層における特徴量によって一対一の関係を抽出し，それらの関係を用いて新たな重み付きグラフを構築する．このグラフのエッジの重みはそれぞれのハイパーエッジに基づいて決定される．こうして構築した重み付きグラフの隣接行列を $\symbf{A}^{(l-1)}$ と表記する． 隣接行列 $\symbf{A}^{(l-1)}$ を用いた $l$ 層におけるグラフフィルタは，次のように表される：  

$$
 \symbf{F}^{(l)}=\sigma(\tilde{\symbf{A}}^{(l-1)}\symbf{F}^{(l-1)}\symbf{\Theta}^{(l-1)}) $$


  ここで， $\tilde{\symbf{A}}^{(l-1)}$ は $\symbf{A}^{(l-1)}$ を正規化したもので，これは第5章のGCNフィルタの節で紹介した方法に沿って行う[^2]． なお，グラフフィルタの隣接行列 $\symbf{A}^{(l-1)}$ は固定されたものではなく，前の層からの隠れ表現に応じて調整されることに注意されたい．

以上で述べた設計の主な欠点は，各ハイパーエッジのノードのうち $2$ つしか考慮されていないことである． これにより，ハイパーエッジ内の他のノードの情報が失われる可能性がでてきてしまう． さらに，これにより非常にスパースな重み付けグラフが生成される可能性がある． そのため，Chan and Liang.(2019)では，隣接行列 $\symbf{A}^{(l-1)}$ を改善する新たなアプローチが提案されている． このアプローチでは，各ハイパーグラフから選択されたノードは，そのノードが属するハイパーエッジ内の残りのノードすべてにも接続されるようにするものである． その結果，各ハイパーエッジは $2\|e\| - 3$ 個のエッジを生成することになる（ $\|e\|$ 個はハイパーエッジ $e$ 内のノード数）． このとき，ハイパーエッジ $e$ から抽出される各エッジの重みは $1/(2\|e\|-3)$ と設定される． そして，隣接行列 $\symbf{A}^{(l-1)}$ はこれらのエッジに基づいて構築され，式(8.8)のグラフフィルタリングで使用される．


[メインページ](../../index.markdown)

[章目次](./chap8.md)

[前の節へ](./subsection_05.md) [次の節へ](./subsection_07.md)

