# 次数
## 定義
グラフにおけるノードの"次数"とは，そのノードが隣接する他のノードの数を指します．
## コード例
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
### 備考 

# 近傍
## 定義
グラフ内のあるノードの「近傍」は、そのノードが直接接続しているすべてのノードの集合を指します。
## コード例
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
