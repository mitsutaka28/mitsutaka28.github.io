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
