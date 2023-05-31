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
具体的には、グラフ $\mathcal{G} = \left{\mathcal{V},\mathcal{E}\right}$ が二部グラフであるとは、
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

[メインページ](../../index.markdown)

[章目次](./chap2.md)
