# 次数
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
# 近傍
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
# 部分グラフ
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
```
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
