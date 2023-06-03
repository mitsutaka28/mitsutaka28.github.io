[メインページ](../../index.markdown)

[章目次](./chap6.md)
## 6.3. 敵対的攻撃に対する防御

グラフデータに対する敵対的な攻撃を防ぐために，様々な防御手法が提案されている． これらの防御手法は主に4つに分類することできる．

(1)グラフの敵対的学習(Adversarial training)

:   モデルのロバスト性を向上させるために，学習手順に敵対的なサンプルを組み込む手法．

(2)グラフの浄化(Purification)

:   敵対的攻撃を検出し，攻撃されたグラフからそれらを除去してクリーンなグラフを生成しようと試みる手法．

(3)グラフアテンション(Attention)

:   訓練段階で敵対的攻撃を特定し，モデルを訓練する際にそれらに対して注意（アテンション）を少なくする手法．

(4)グラフの構造学習(Structure learning)

:   GNNモデルを訓練しながら，同時に攻撃されたグラフからクリーンなグラフを学習することを目指す手法．

ここからは，上記の各分類別における代表的な手法をいくつか紹介していこう．

### グラフの敵対的学習

敵対的学習の概念は


$$
 \operatorname{IG}_{\symbf{x}}(i) = (\symbf{x}_i - \symbf{x}'_i)\times \int^{1}_0\dfrac{\partial F(\symbf{x}' + \alpha(\symbf{x} - \symbf{x}'))}{\partial \symbf{x}_i}d\alpha $$


 この式は，成分 $i$ の特徴量がどれだけ予測値に影響するかを求めるために，基準点から実際の入力までの範囲で特徴量の変化が予測に与える影響（勾配）を積分している．ただし実践的には，以下の数値近似式が使われる( $m$ は，積分を近似計算する際の区間分割数)： 

$$
 \operatorname{IG}^{\text{approx}}_{\symbf{x}}(i) = \dfrac{(\symbf{x}_i - \symbf{x}'_i)}{m}\times\sum^{m}_{k=1}\dfrac{\partial F(\symbf{x}' + \tfrac{k}{m} (\symbf{x}-\symbf{x}'))}{\partial \symbf{x}_i} $$


 




$$

\begin{aligned}
    \nabla^{\text{meta}}_{\mathcal{G}} &=\dfrac{\partial \mathcal{L}_{\text{atk}}(f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T))}{\partial \mathcal{G}}\\
    &=\dfrac{\partial \mathcal{L}_{\text{atk}}(f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T))}{\partial f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T)}\cdot\dfrac{\partial f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T)}{\partial \mathcal{G}}\\
    &=\dfrac{\partial \mathcal{L}_{\text{atk}}(f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T))}{\partial f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T)}\cdot\left[\dfrac{\partial f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T)}{\partial \mathcal{G}}\dfrac{\partial\mathcal{G}}{\partial\mathcal{G}} + \dfrac{\partial f_{\text{GNN}}(\mathcal{G};\,\symbf{\Theta}_T)}{\partial \symbf{\Theta}_T}\dfrac{\partial \symbf{\Theta}_T}{\partial\mathcal{G}}\right]
\end{aligned}
$$

 


    ・価値ベースのアプローチ（例：深層Q学習）:

    :   エージェントは行動価値関数(Q関数)を直接学習し，その学習されたQ関数を用いて行動を選択する．最適な行動は最大のQ値を持つ行動として選択される．深層Q学習では，このQ関数の近似にニューラルネットワークを使用している．

    ・方策ベースのアプローチ（例：方策勾配法）:

    :   エージェントは各状態での行動の選択確率（方策）を直接学習する．この方策の主な表現方法として，ニューラルネットワークを基にした方策ネットワークがある．方策勾配法は，これらの方策モデルを最適化し，報酬を最大化するアルゴリズムである．なお，行動価値関数は方策を改善するための指針として使用されることはあるが，方策ベースのアプローチでは一般的には価値を直接学習・更新することはない．

[メインページ](../../index.markdown)

[章目次](./chap6.md)

[前の節へ](./subsection_02.md) [次の節へ](./subsection_04.md)

[^1]: 訳注：IGは，入力特徴の各成分のモデル予測への貢献度を評価するために提案された(Sundararajan *et al*., 2017)．入力特徴量ベクトルを $\symbf{x}$ ，基準点を $\symbf{x}'$ （通常はゼロベクトル），学習後の予測関数を $F(\cdot)$ とした場合，成分 $i$ のIGは次のように定義される：  
[^2]: 訳注：依存する変数に注意しながら連鎖律を丁寧に用いることで以下を得る． 
[^3]: 訳注：強化学習には主に二つのアプローチが存在する．
