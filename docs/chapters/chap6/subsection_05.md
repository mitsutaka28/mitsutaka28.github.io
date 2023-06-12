[メインページ](../../index.markdown)

[章目次](./chap6.md)
## 6.5. 参考文献

GNNのロバスト性についての研究は現在も急速に進展している．そのため，グラフに対する敵対的攻撃とその攻撃からの防御に関する情報を集めた包括的なリポジトリが構築されている(Li *et al*., 2020a)． このリポジトリを通じて，既存のアルゴリズムに対する体系的な実験を行うとともに，新たなアルゴリズムの効率的な開発も可能になっている． さらに，このリポジトリをベースにした実証研究も行われており(Jin *et al*., 2020a)，グラフに対する敵対的攻撃および防御についての深い洞察を提供することで，この研究分野の発展を支えている． また，グラフ分野に加えて，画像(Yuan *et al*., 2019; Xu *et al*., 2019b; Ren ., 2020)やテキスト(Xu *et al*., 2019b; Zhang *et al*., 2020)などといった，他の分野でも敵対的攻撃とその防御手法が研究されている．


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

[前の節へ](./subsection_04.md) [次の節へ](./subsection_06.md)

[^1]: 訳注：IGは，入力特徴の各成分のモデル予測への貢献度を評価するために提案された(Sundararajan *et al*., 2017)．入力特徴量ベクトルを $\symbf{x}$ ，基準点を $\symbf{x}'$ （通常はゼロベクトル），学習後の予測関数を $F(\cdot)$ とした場合，成分 $i$ のIGは次のように定義される：  
[^2]: 訳注：依存する変数に注意しながら連鎖律を丁寧に用いることで以下を得る． 
[^3]: 訳注：強化学習には主に二つのアプローチが存在する．
