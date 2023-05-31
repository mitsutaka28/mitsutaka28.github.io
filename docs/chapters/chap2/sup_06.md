# 2.6 サポートページ
## ヘテログラフ
ヘテログラフは、異なるタイプのノードとエッジを含むグラフのことを指します。
それぞれのノードとエッジは特定のタイプに関連付けられています。
ノードタイプの集合を $\mathcal{T}_n$ ，エッジタイプの集合を $\mathcal{T}_e$ とし、
各ノードと各エッジをそれぞれのタイプに対応付ける写像 $\phi_n\colon \mathcal{V}\rightarrow\mathcal{T}_n$ と $\phi_e\colon \mathcal{E}\rightarrow\mathcal{T}_e$ が存在します。

### numpy

### networkx
```python
import networkx as nx

# 空の有向グラフを作成
G = nx.MultiDiGraph()

# 各ノードを追加し、ノードタイプを指定
G.add_node(0, type='author')
G.add_node(1, type='paper')
G.add_node(2, type='conference')
G.add_node(3, type='author')
G.add_node(4, type='paper')
G.add_node(5, type='conference')

# エッジを追加し、エッジタイプを指定
G.add_edge(0, 1, type='writes')
G.add_edge(1, 0, type='written_by')
G.add_edge(1, 2, type='presented_at')
G.add_edge(2, 1, type='presents')
G.add_edge(3, 4, type='writes')
G.add_edge(4, 3, type='written_by')
G.add_edge(4, 5, type='presented_at')
G.add_edge(5, 4, type='presents')

# グラフのノードとエッジを表示
print('Nodes:')
for node in G.nodes(data=True):
    print(node)

print('Edges:')
for edge in G.edges(data=True):
    print(edge)
```

このスクリプトでは、networkxを使ってヘテログラフを作成しています。
まず、空の有向グラフを作成し、次にノードとエッジを追加します。
各ノードとエッジはそれぞれ特定のタイプを持っており、そのタイプは属性として付与されます。最後に、作成したグラフのノードとエッジを表示しています。

## 二部グラフ
二部グラフは、ノードの集合が互いに素な2つの部分集合 $\mathcal{V}_1$ と $\mathcal{V}_2$ に分割でき、
各エッジが $\mathcal{V}_1$ のノードと $\mathcal{V}_2$ のノードを接続するようなグラフのことを言います。
具体的には、グラフ $\mathcal{G} = \left\\{\mathcal{V},\mathcal{E}\right\\}$ が二部グラフであるとは、
 $\mathcal{V} = \mathcal{V}_1\cup \mathcal{V}_2$ かつ $\mathcal{V}_1\cap \mathcal{V}_2 = \emptyset$ が成り立ち、
 さらにすべての $e = (v^1_e,v^2_e)\in \mathcal{E}$ に対して、 $v^1_e\in \mathcal{V}_1$ ,,, $v^2_e\in \mathcal{V}_2$ が満たされるときを指します。

### numpy


### networkx
```python
import networkx as nx
import matplotlib.pyplot as plt

# 空のグラフを作成
G = nx.Graph()

# 各ノードを追加し、ノードセットを指定
for i in range(3):
    G.add_node(i, bipartite=0)  # ノードセット0に属するノード
for i in range(3, 6):
    G.add_node(i, bipartite=1)  # ノードセット1に属するノード

# エッジを追加
G.add_edges_from([(0, 3), (0, 4), (1, 4), (1, 5), (2, 5)])

# グラフを描画
pos = nx.bipartite_layout(G, [0, 1, 2])
nx.draw(G, pos, with_labels=True)
plt.show()
```
このスクリプトでは、networkxを使って二部グラフを作成しています。
まず、空のグラフを作成し、次にノードとエッジを追加します。
ノードの追加時には、各ノードがどのノードセットに属するかを示す'bipartite'という属性を設定します。
そして、エッジを追加し、最後にグラフを描画します。
二部グラフのレイアウトは'bipartite_layout'関数を用いて設定します。

## 多次元グラフ
多次元グラフは、ノード集合 $\mathcal{V} = \left\\{v_1,\dots,v_N\right\\}$ および複数のエッジ集合 $\left\\{\mathcal{E}_1,\dots,\mathcal{E}_D\right\\}$ で構成され、
各エッジ集合 $\mathcal{E}_d$ がそれぞれの $d$ 次元におけるノード間の $d$ 番目の関係タイプを表すようなグラフです。
これらの関係は $D$ 個の隣接行列 $\symbf{A}^{(1)},\dots,\symbf{A}^{(D)}$ としても表現できます。
 $d$ 次元に対応する隣接行列 $\symbf{A}\_d\in \mathbb{R}^{N\times N}$ は、ノード間にあるエッジ集合 $\mathcal{E}_d$ を記述します。
  $\symbf{A}\_d$ の $i,j$ 要素 $\symbf{A}\_d\[i,j\]$ は、ノード $v_i$ と $v_j$ の間に次元$d$のエッジが存在するとき（ $(v_i,v_j)\in\mathcal{E}\_d$ ）にのみ $1$ 、存在しない場合は $0$ となります。
  
### numpy

### networkx
```python
import networkx as nx
import matplotlib.pyplot as plt

# マルチグラフを作成
G = nx.MultiGraph()

# ノードを追加
G.add_nodes_from(range(6))

# 各次元のエッジを追加
edges_dim1 = [(0, 1), (1, 2), (2, 3), (3, 4), (4, 5)]
edges_dim2 = [(0, 2), (1, 3), (2, 4), (3, 5)]
edges_dim3 = [(0, 3), (1, 4), (2, 5)]

G.add_edges_from(edges_dim1, color='red', weight=0.8, relation='relation1')
G.add_edges_from(edges_dim2, color='blue', weight=0.4, relation='relation2')
G.add_edges_from(edges_dim3, color='green', weight=0.6, relation='relation3')

# グラフを描画
pos = nx.spring_layout(G)
edges = G.edges(data=True)
colors = [d['color'] for _, _, d in edges]
weights = [d['weight'] for _, _, d in edges]
# 続き
nx.draw(G, pos, edges=edges, edge_color=colors, width=weights)
plt.show()

# エッジの関係タイプを表示
for u, v, data in G.edges(data=True):
    print(f""ノード{u}とノード{v}の間のエッジは、関係{data['relation']}を持つ"")
```

## 符号付きグラフ
符号付きグラフはエッジに正または負の符号を持つグラフで、正のエッジは通常の関係を、負のエッジは対立する関係を表すことが多いです。
ノード間の関係が協力的（友好的）か競争的（敵対的）かを明示的に表すことができます。
たとえば、ソーシャルネットワークでは友達関係（正）とブロック関係（負）を表現できます。
### numpy

### networkx
```python
import networkx as nx
import matplotlib.pyplot as plt
import random

# グラフの生成
G = nx.Graph()

# ノードの追加
G.add_nodes_from(range(6))  # ノード数6

# エッジの追加 (エッジの符号はランダム)
for i in range(6):
    for j in range(i+1, 6):
        sign = random.choice([-1, 1])  # 符号をランダムに選択
        G.add_edge(i, j, sign=sign)

# 符号によってエッジの色を分ける
colors = ['red' if data['sign'] == -1 else 'blue' for u, v, data in G.edges(data=True)]

# グラフの描画
nx.draw(G, with_labels=True, edge_color=colors)
plt.show()

# エッジの符号を表示
for u, v, data in G.edges(data=True):
    print(f""ノード{u}とノード{v}の間のエッジは、{'負' if data['sign'] == -1 else '正'}です"")
```
## ハイパーグラフ
ハイパーグラフとは、通常のグラフが2つのノード間の関係（エッジ）のみを表現するのに対し、3つ以上のノード間の関係（ハイパーエッジ）を表現できるグラフの一種です。
例えば、複数の著者が共同で論文を書いた場合、それらの著者と論文の関係はハイパーエッジで表現できます。

### numpy

### networkx
```python
import networkx as nx
import matplotlib.pyplot as plt
import random

# グラフの生成
G = nx.Graph()

# ノードの追加
G.add_nodes_from(range(6))  # ノード数6

# ハイパーエッジの追加 (ハイパーエッジは中間ノードとして表現)
for hyperedge_id in range(3):  # ハイパーエッジ数3
    # ハイパーエッジに接続するノードをランダムに選択 (2つ以上)
    nodes = random.sample(range(6), random.randint(2, 6))
    for node in nodes:
        G.add_edge(f""hyperedge_{hyperedge_id}"", node)

# グラフの描画
nx.draw(G, with_labels=True)
plt.show()
```
このコードは、6つのノードと、それらをつなぐハイパーエッジ（中間ノードとして表現）を持つハイパーグラフを作成します。
具体的には、各ハイパーエッジに対して、接続するノードをランダムに選択し、ハイパーエッジと各ノード間にエッジを追加しています。
最後に、グラフを描画します。この結果から、ハイパーエッジが複数のノードを結びつける様子を視覚的に把握することができます。

## 離散ダイナミックグラフ
ダイナミックグラフ（または時間依存グラフ）は、時間とともにその構造が変化するグラフです。
これは一連のグラフのスナップショットとして表現され、各スナップショットはある特定の時間点でのグラフの状態を表現します。
このような表現方法は、特にグラフの構造が時間とともに動的に変化する現象をモデル化する際に用いられます。

### numpy

### networkx
```python
import networkx as nx
import matplotlib.pyplot as plt
import random

# 初期グラフの生成
G0 = nx.Graph()
G0.add_nodes_from(range(6))  # ノード数6
# エッジをランダムに追加
for i in range(5):  # エッジ数5
    G0.add_edge(random.randint(0, 5), random.randint(0, 5))

# ダイナミックグラフの生成 (5ステップ)
dynamic_graph = [G0]
for t in range(1, 5):  # 時間ステップ数5
    Gt = nx.Graph()
    Gt.add_nodes_from(range(6))  # ノード数6
    # エッジをランダムに追加
    for i in range(5):  # エッジ数5
        Gt.add_edge(random.randint(0, 5), random.randint(0, 5))
    dynamic_graph.append(Gt)

# ダイナミックグラフの描画
for t, G in enumerate(dynamic_graph):
    plt.figure()
    nx.draw(G, with_labels=True)
    plt.title(f""Time step {t}"")
    plt.show()
```
このコードは、各時間ステップでランダムなエッジを持つグラフを生成し、それらのグラフをリストに格納してダイナミックグラフを作成します。
最後に、各時間ステップでのグラフを描画します。この結果から、グラフの構造が時間とともにどのように変化するかを視覚的に把握することができます。

[メインページ](../../index.markdown)

[章目次](./chap2.md)
