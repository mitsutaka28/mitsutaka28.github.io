# 2.3 サポートページ
## 次数
グラフにおけるノードの"次数"とは，そのノードが隣接する他のノードの数を指します．
### numpy
```python
import numpy as np

# 隣接行列の定義(6ノード，無向グラフ)
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# 次数の計算
degrees = np.sum(A, axis=1)

print("次数:", degrees)
```

### networkx
```python
import numpy as np
import networkx as nx

# 隣接行列の定義(6ノード，無向グラフ)
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# グラフの生成
G = nx.from_numpy_matrix(A)

# 次数の計算
degrees = [deg for node, deg in G.degree()]

print("次数:", degrees)
```
## 近傍
グラフ内のあるノードの「近傍」は、そのノードが直接接続しているすべてのノードの集合を指します。
### numpy
```python
import numpy as np

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# ノード0の近傍の計算
neighbors = np.where(A[0] > 0)[0]

print(""ノード0の近傍:"", neighbors)
```
### networkx
```python
import numpy as np
import networkx as nx

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# グラフの生成
G = nx.from_numpy_matrix(A)

# ノード0の近傍の計算
neighbors = list(G.neighbors(0))

print(""ノード0の近傍:"", neighbors)
```
## 部分グラフ
部分グラフとは、与えられたグラフのノード集合とエッジ集合の部分集合から構成されるグラフのことです。部分グラフでは、エッジ集合の部分集合に関連するすべてのノードが、ノード集合の部分集合に含まれる必要があります。
### numpy
```python
import numpy as np

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# 部分グラフに含めるノードのインデックス
nodes_idx = [0, 1, 2]

# 部分グラフの隣接行列の計算
A_subgraph = A[np.ix_(nodes_idx, nodes_idx)]

print(""部分グラフの隣接行列:\n"", A_subgraph)
```

### networkx
```python
import numpy as np
import networkx as nx

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 0, 1, 0],
    [0, 1, 0, 0, 1, 1],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# グラフの生成
G = nx.from_numpy_matrix(A)

# 部分グラフの計算
subgraph = G.subgraph(nodes_idx)

# 部分グラフの隣接行列を取得
A_subgraph_nx = nx.to_numpy_matrix(subgraph)

print(""部分グラフの隣接行列:\n"", A_subgraph_nx)
```
## 連結成分
連結成分とは、グラフの部分グラフで、その部分グラフ内の任意の2つのノード間に少なくとも1つのパスが存在し、その部分グラフのノードが、元のグラフのノード集合から部分グラフのノード集合を取り除いた集合に含まれるどのノードとも隣接していないものを指します。
### numpy
```python
import numpy as np

# 深さ優先探索を行う関数
def dfs(v, visited, A):
    # ノードvを訪れたとマークする
    visited[v] = True
    # ノードvに隣接する各ノードについて
    for i in range(len(A)):
        # ノードiが未訪問で、ノードvと隣接している場合
        if A[v, i] == 1 and not visited[i]:
            # ノードiから探索を行う
            dfs(i, visited, A)

# 連結成分を取得する関数
def connected_components(A):
    # 全てのノードを未訪問とする
    visited = [False] * len(A)
    # 連結成分を保存するリスト
    components = []
    # 各ノードについて
    for i in range(len(A)):
        # ノードiが未訪問の場合
        if not visited[i]:
            # 連結成分を保存するリスト
            component = []
            # ノードiから探索を行う
            dfs(i, visited, A)
            # 訪問済みのノードを連結成分に追加する
            for j in range(len(A)):
                if visited[j]:
                    component.append(j)
            # 連結成分を保存する
            components.append(component)
            # 訪問済みのノードをリセットする
            visited = [False if v in component else v for v in visited]
    return components

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 0, 0, 0],
    [1, 1, 0, 0, 0, 0],
    [0, 0, 0, 0, 1, 1],
    [0, 0, 0, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# 連結成分を出力
print(""連結成分:"", connected_components(A))
```

### networkx
```python
import numpy as np
import networkx as nx

# 隣接行列からグラフを作成
G = nx.from_numpy_matrix(A)
# 連結成分を取得
components = list(nx.connected_components(G))
# 連結成分を出力
print(""連結成分:"", components)
```
## 最短パス
最短パスとは、グラフ内の2つのノード間をつなぐパスのうち、エッジの数（または重みの合計）が最も少ないパスを指します。
### numpy
```python
import numpy as np
from scipy.sparse.csgraph import dijkstra

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 0, 0, 0],
    [1, 1, 0, 1, 0, 0],
    [0, 0, 1, 0, 1, 1],
    [0, 0, 0, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# ダイクストラのアルゴリズムを用いて最短距離と最短パスを求める
dist_matrix, predecessors = dijkstra(csgraph=A, directed=False, return_predecessors=True)

# 開始ノードと終了ノード
start_node, end_node = 0, 5

# 最短パスを逆順に格納するリスト
path = []
i = end_node
while i != start_node:
    path.append(i)
    i = predecessors[start_node, i]
path.append(start_node)

# 最短パスを正しい順序にする
path = path[::-1]

print('最短パス: ', path)
```
### networkx
```python
import networkx as nx

# 隣接行列からグラフを作成
G = nx.from_numpy_matrix(A)

# 開始ノードと終了ノード
start_node, end_node = 0, 5

# 最短パスを求める
path = nx.shortest_path(G, start_node, end_node)

print('最短パス: ', path)
```
## 直径
直径とは、グラフ内の全てのノードペア間の最短パスの中で最も長いものを指します。
### numpy
```python
import numpy as np
from scipy.sparse.csgraph import dijkstra

# 隣接行列の定義
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 0, 0, 0],
    [1, 1, 0, 1, 0, 0],
    [0, 0, 1, 0, 1, 1],
    [0, 0, 0, 1, 0, 1],
    [0, 0, 0, 1, 1, 0]
])

# ダイクストラのアルゴリズムを用いて最短距離と最短パスを求める
dist_matrix, _ = dijkstra(csgraph=A, directed=False, return_predecessors=True)

# グラフの直径を求める（無限大の値は無視する）
diameter = np.max(dist_matrix[~np.isinf(dist_matrix)])
# ■無限大について：
# 全てのノードが連結していない（すなわち、グラフが複数の分離した部分グラフからなる）場合、
# あるノードから別のノードへのパスが存在しないため、その距離は無限大と見なされます。
# このような場合，ダイクストラのアルゴリズムを適用すると、無限大の距離が結果に含まれます。
# そのため、無限大の値を無視する処理が必要になります。

print('グラフの直径: ', diameter)
```
### networkx
```python
import networkx as nx

# 隣接行列からグラフを作成
G = nx.from_numpy_matrix(A)

# グラフの直径を求める
diameter = nx.diameter(G)

print('グラフの直径: ', diameter)
```
## 次数中心性
次数中心性とは、各ノードがどれだけ多くの他のノードとつながっているかを表す指標です。具体的には、あるノードの次数（接続しているエッジの数）をそのノードの次数中心性とします。
### numpy
```python
import numpy as np

# 定数グラフの作成
A = np.array([
    [0, 1, 1, 0, 0, 0],
    [1, 0, 1, 1, 0, 0],
    [1, 1, 0, 1, 1, 0],
    [0, 1, 1, 0, 1, 0],
    [0, 0, 1, 1, 0, 1],
    [0, 0, 0, 0, 1, 0]
])

# 次数中心性の計算
degree_centrality = np.sum(A, axis=1)

print(""次数中心性: "", degree_centrality)
```
### networkx
```python
import networkx as nx

# 定数グラフの作成
G = nx.from_numpy_array(A)

# 次数中心性の計算
degree_centrality = nx.degree_centrality(G)

print(""次数中心性: "", degree_centrality)
```
