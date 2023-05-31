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

[メインページ](../../index.markdown)

[章目次](./chap2.md)
