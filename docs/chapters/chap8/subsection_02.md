[メインページ](../../index.markdown)

[章目次](./chap8.md)
## 8.2. ヘテログラフ上のグラフニューラルネットワーク

ヘテログラフは，定義2.35で定義されているように複数種類のノードとエッジから成り， 現実的な場面で広く確認されるグラフである． 例えば，論文・著者・会場の間の関係はヘテログラフによって表現することができる（2.6.1節参照）． グラフニューラルネットワークモデルはヘテログラフに対応するように調整されている(Zhang *et al*., 2018b; Wang *et al*., 2019i; Chen *et al*., 2019b)． ヘテログラフの複雑さを扱うために，異なるセマンティクスを持つノード間の様々な関係を捉えるメタパス（定義4.5でのメタパス・スキーマとメタパスの定義を参照）が採用されている． 特に，Zhang *et al*.(2018b)やChen *et al*.(2019b)では，ヘテログラフをいくつかのホモグラフに分割するためにメタパスが用いられている． 具体的には，メタパスはノード間のエッジとして扱われ，同じメタパス・スキーマに従うメタパスは同じタイプのエッジとみなされる． 各メタパス・スキーマは，そのスキーマに従うメタパスをエッジとみなして，ノードとエッジが1種類ずつの単純グラフを作り出す． そして，これらの単純グラフに対して，第5章で説明したグラフフィルタリング操作が適用されることになる． こういった方法で，各ノードが持つ局所的なセマンティクス情報を捉えた表現がいくつか作られ，それらを結合することで最終的なノード表現が生成される．

同様に，メタパスは**メタパス近傍**を定義するために使用される．あるメタパス・スキーマ $\psi$ が与えられたとき，ノード $v_j$ がノード $v_i$ からスキーマ $\psi$ に従うメタパスを通じて到達可能である場合，「ノード $v_j$ はノード $v_i$ の $\psi$ 近傍」と定義される． 異なるメタパスに基づくメタパス近傍は，グラフフィルタリングプロセス中で異なる取り扱いがなされることになる(Wang *et al*., 2019i)． 具体的には，異なるタイプのメタパス近傍から集約された情報は，アテンション機構を通じて組み合わされ，結果として更新されたノード表現が生成される(Wang *et al*., 2019i)． まず，メタパス近傍を形式的に定義し，それからヘテログラフ向けに設計されたグラフフィルタを説明する． 
<div class="definition">
 
<strong>定義 8.1 メタパス近傍</strong>
 ヘテログラフにおけるノード $v_i$ とメタパス・スキーマ $\psi$ が与えられたとき， $\symcal{N}_{\psi}(v_i)$ と表記するノード $v_i$ の $\psi$ 近傍は，メタパス・スキーマ $\psi$ に従うメタパスを通じてノード $v_i$ と接続するノード群で構成される． 
</div>


ヘテログラフ用のグラフフィルタは，以下の2つのステップで設計される．

1.  タスクで採用されるメタパス・スキーマの集合 $\symbf{\Psi}$ に含まれる各 $\psi\in\symbf{\Psi}$ について， $\psi$ 近傍からの情報を集約する．

2.  各メタパス近傍から集約した情報を組み合わせてノード表現を生成する．具体的には，ノード $v_i$ に対する（ $l$ 層目における）グラフフィルタリング操作は，そのノード表現を次のように更新する：  

$$

\begin{aligned}
        \symbf{z}^{(l)}_{\psi,i} &= \sum_{v_j\in\symcal{N}_{\psi}(v_i)}\alpha^{(l-1)}_{\psi,ij}\symbf{F}^{(l-1)}_j\symbf{\Theta}^{(l-1)}_{\psi}\\
        \symbf{F}^{(l)}_i &= \sum_{\psi\in\symbf{\Psi}}\beta^{(l)}_{\psi}\symbf{z}^{(l)}_{\psi,i}
\end{aligned}
$$

 

ここで， $\symbf{z}^{(l)}\_{\psi,i}$ はノード $v_i$ の $\psi$ 近傍から集約された情報であり， $\symbf{\Theta}^{(l-1)}\_{\psi}$ は $\psi$ 近傍特有のパラメータ， $\alpha^{(l-1)}\_{\psi,ij}$ と $\beta^{(l)}\_{\psi}$ は，5.3.2節で紹介したGATフィルタと同様に学習可能な重要度(アテンションスコア)である． つまり， $\alpha^{(l-1)}\_{\psi,i}$ は $l$ 番目の $\psi$ 近傍 $v_j\in\symcal{N}\_{\psi}(v_i)$ の $v_i$ への寄与度合いを表しており， $v_i$ のノード表現を更新するために使われる． これは以下のように定義される：  

$$
 \alpha^{(l-1)}_{\psi,ij} = \dfrac{\exp\left(\sigma\left(\symbf{a}^{T}_{\psi}\cdot\left[\symbf{F}^{(l-1)}_i\symbf{\Theta}^{(l-1)}_{\psi},\symbf{F}^{(l-1)}_j\symbf{\Theta}^{(l-1)}_{\psi}\right]\right)\right)}{\sum_{v_k\in\symcal{N}_{\psi}(v_i)}\exp\left(\sigma\left(\symbf{a}^{T}_{\psi}\cdot\left[\symbf{F}^{(l-1)}_i\symbf{\Theta}^{(l-1)}_{\psi},\symbf{F}^{(l-1)}_k\symbf{\Theta}^{(l-1)}_{\psi}\right]\right)\right)} $$


  ここで， $\symbf{a}\_{\psi}$ は学習対象のパラメータ化されたベクトルである．

一方で，異なるメタパス近傍からの情報を組み合わせるためのアテンションスコア $\beta^{(l)}\_{\psi}$ は，特定のノード $v_i$ ごとに固有な値ではなく，全ノードの表現更新において共有される．  $\beta^{(l)}\_{\psi}$ は，それぞれのノードの $\psi$ 近傍からの寄与度合いを表している．これは以下のように定義される．  

$$
 \beta^{(l)}_{\psi} = \dfrac{\exp\left(\dfrac{1}{\|\symcal{V}\|}\sum_{i\in\symcal{V}}\symbf{q}^{T}\cdot\tanh\left(\symbf{z}^{(l)}_{\psi,i}\symbf{\Theta}^{(l)}_{\beta} + \symbf{b}\right)\right)}{\sum_{\psi\in\symbf{\Psi}}\exp\left(\dfrac{1}{\|\symcal{V}\|}\sum_{i\in\symcal{V}}\symbf{q}^{T}\cdot\tanh\left(\symbf{z}^{(l)}_{\psi,i}\symbf{\Theta}^{(l)}_{\beta} + \symbf{b}\right)\right)} $$


  ここで， $\symbf{q}$ や， $\symbf{\Theta}^{(l)}\_{\beta}$ ， $\symbf{b}$ は学習対象のパラメータである．


[メインページ](../../index.markdown)

[章目次](./chap8.md)

[前の節へ](./subsection_01.md) [次の節へ](./subsection_03.md)


