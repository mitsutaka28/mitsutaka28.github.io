[メインページ](../../index.markdown)

[章目次](./chap14.md)
## 14.2. より深い層を持つグラフニューラルネットワーク

グラフフィルタ層（例：GCNフィルタ, GATフィルタなど; 詳しくは5.3.2節のグラフフィルタ操作を参照）の数を大幅に増やすと，ノード分類の性能が大きく低下することが報告されている． この性能な低下は主に**Oversmoothing（過度な平滑化）**によるものである． これは，グラフフィルタの層数が増えると，ノードの特徴量が互いに類似したものになり，区別が難しくなる現象を指している(Li *et al*., 2018b)． ここでは，GCNフィルタを例にOversmoothingの問題について説明する． 直感的に説明すると，GCNフィルタは空間的な観点から見れば，「ノード表現をその近傍ノード表現の**平均**に基づいて更新する」という働きを持っている． このプロセスは，自然と近傍ノードの表現が類似する結果をもたらす． したがって，グラフフィルタ操作を深く積み重ねると，（連結グラフであると仮定すれば）全ノード表現が似たものになってくる．

Li *et al*.(2018b)では，グラフフィルタ層の数が無限大に近づくという極限状態におけるOversmoothing現象が解析されている． 具体的には，フィルタの層数が無限大に近づけると，各ノード表現は入力特徴量に関係なく同じ値に収束することになる． ただし，分析を簡単にするために，Li *et al*.(2018b)では，グラフフィルタ層の間の非線形活性化層を無視していることに注意せよ． このように非線形活性化層がない状況下で，入力特徴量 $\symbf{F}$ に対して $L$ 回のGCNフィルタを適用すると，以下のように表現できる：

 

$$

\begin{aligned}
    &\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\left(\cdots\left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\symbf{F}\symbf{\Theta}^{(0)}\right)\symbf{\Theta}^{(1)}\right)\cdots\right)\symbf{\Theta}^{(L-1)}\notag\\
    &= \left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\right)^{L}\symbf{F}\symbf{\Theta}
\end{aligned}
\tag{14.1}
$$

 

ここで， $\symbf{\Theta}$ は $\symbf{\Theta}^{(0)},\dots,\symbf{\Theta}^{(L-1)}$ の積を表す．また， $\tilde{\symbf{A}}$ は式(5.21)のGCNフィルタで紹介した $\symbf{A}+\symbf{I}$ を表し， $\tilde{\symbf{D}}$ は対応する次数行列である． 式(14.1)のフィルタリング操作は， $\symbf{F}^{(0)}$ の各列に $\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}$ の操作を適用するという視点で見ることができる．

以下の定理は，単一チャンネルのグラフ信号におけるOversmoothing現象を述べたものである(Li *et al*., 2018b)． 
<div class="theorem">
 
<strong>定理 14.1</strong>
 隣接行列 $\symbf{A}$ を持つ（二部グラフではない）連結グラフを $\symscr{G}$ とする． このとき，任意の入力特徴量 $\symbf{f}\in\mathbb{R}^{N}$ に対して，以下の関係式が成り立つ：

 $$
 \lim_{L\to\infty}\left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\right)^{L}\symbf{f} = \theta_1\cdot\symbf{u}_1
\tag{14.2} $$
 

ここで， $\tilde{\symbf{A}}=\symbf{A} + \symbf{I}$ とし， $\tilde{\symbf{D}}$ はそれに対応する次数行列を表している．  $\tilde{\symbf{A}}$ は自己ループを含むグラフ $\symscr{G}$ の修正された隣接行列とみなすことができる． また，ベクトル $\symbf{u}\_1$ は $\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}$ の最大固有値に対応する固有ベクトルで， $\theta\_1=\symbf{u}^{\top}\_1\symbf{f}$ である． ここで， $\symbf{u}\_1=\tilde{\symbf{D}}^{-\tfrac{1}{2}}\symbf{1}$ であり，これは唯一「ノードの次数」という情報を含んでいる． 
<div class="prf">
<strong>証明: </strong>
  $\tilde{\symbf{L}}_{\text{nor}}=\symbf{I} - \tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}$ とおくと，これは $\tilde{\symbf{A}}$ に対する正規化ラプラシアン行列を表している． Chung and Graham (1997)において提示された補題1.7によると， $\tilde{\symbf{L}}_{\text{nor}}$ は $0=\lambda_1<\lambda_2\dots\lambda_N<2$ を満たす固有値集合を持ち，それぞれの固有値に対応する固有ベクトルは $\symbf{u}_1,\dots,\symbf{u}_N$ である． そして，これを行列形式で表せば， $\tilde{\symbf{L}}_{\text{nor}}$ の固有値分解は $\tilde{\symbf{L}}_{\text{nor}} = \symbf{U}\symbf{\Lambda}\symbf{U}^{\top}$ となる． ここで， $\symbf{U}=\left[\symbf{u}_1,\dots,\symbf{u}_N\right]$ はすべての固有ベクトルから成る行列で， $\symbf{\Lambda} = \mathrm{diag}([\lambda_1,\dots,\lambda_N])$ は固有値を対角成分に持つ行列である． こうして $\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}$ の固有値と固有ベクトルは， $\tilde{\symbf{L}}$ の固有値と固有ベクトルと以下のように関連付けられる：  

$$
 \tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}} = \symbf{I} - \tilde{\symbf{L}}_{\text{nor}} = \symbf{U}\symbf{U}^{\top} - \symbf{U}\symbf{\Lambda}\symbf{U}^{\top} = \symbf{U}(\symbf{I} - \symbf{\Lambda})\symbf{U}^{\top} $$


  したがって， $1=1-\lambda_1 > 1-\lambda_2 > \dots > 1-\lambda_N > -1$ は，それぞれ固有ベクトル $\symbf{u}_1,\dots,\symbf{u}_N$ に対応する， $\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}$  の固有値となる． 次に，この式を $L$ 乗すると，以下を得る：  

$$
 \left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\right)^{L} = \left(\symbf{U}(\symbf{I} - \Lambda)\symbf{U}^{\top}\right)^{L} = \symbf{U}(\symbf{I} - \symbf{\Lambda})^{L}\symbf{U}^{\top} $$


  式(14.2)の極限は次のように表現できる：  

$$

\begin{aligned}
    \lim_{L\to\infty}\left(\tilde{\symbf{D}}^{-\tfrac{1}{2}}\tilde{\symbf{A}}\tilde{\symbf{D}}^{-\tfrac{1}{2}}\right)^{L}\symbf{f} &= \lim_{L\to\infty}\symbf{U}(\symbf{I} - \symbf{\Lambda})^{L}\symbf{U}^{\top}\symbf{f}\\
    &= \symbf{U}\,\mathrm{diag}([1, 0,\dots,0])\symbf{U}^{\top}\symbf{f}\\
    &= \symbf{u}_1\cdot(\symbf{u}^{\top}_1\symbf{f})\\
    &= \theta_1\cdot\symbf{u}_1
\end{aligned}
$$

 Q.E.D. 
</div>
 
</div>
 定理14.1は，1チャネルグラフ信号 $\symbf{f}$ に対してGCNフィルタを繰り返し適用すると，結果として得られるのは $\theta\_1\cdot\symbf{u}\_1$ であり，これは「ノードの次数を超える情報を捉えることはない」という事実を示している． 式(14.1)に示されるような多チャンネルの場合，行列 $\symbf{F}\symbf{\Theta}$ の各列は異なるスケール $\theta\_1$ によって $\theta\_1\cdot\symbf{u}\_1$ に写像される． したがって，異なる列同士は異なるスケールで同じ情報を含むことになる． さらにいえば， $\symbf{u}\_1$ に含まれる次数の情報は，ほとんどのノード分類タスクには有用でない可能性がある． このことは「グラフフィルタリング層の数が増えるとノード分類の性能が低下する」ことの理由でもある． ReLU活性化関数を使用した非線形活性化を含む場合について，Oono and Suzuki.(2020)でも同様の観察結果が得られることが報告されている． 特に，Oono and Suzuki.(2020)によれば，ReLU活性化関数はOversmoothingを加速させることが示されている． GCNフィルタを導入する目的は，隣接ノードの情報を用いてノード表現を更新することにある． 例えば $k$ 個のGCNフィルタを積み重ねることで，各ノードは $k$ 次近傍の情報を取得することが可能になる． ノード分類をうまく行うためには，各ノード周辺からの情報を適切に取り込むことが必要となってくるが，上記のように，グラフフィルタリング層を過度に積み重ねすぎると，（特徴量を均一化させる）Oversmoothingの問題に直面することになってしまう． この問題を緩和するための方法は，Xu *et al*.(2018a)やRong *et al*.(2020)，Zhao and Akoglu.(2019)などで提案されている．

### Jumping Knowledge

Xu *et al*.(2018a)によれば，異なるノードは異なる深さの近傍を必要とするため，個々のノードにはそれぞれ異なる数のグラフフィルタリング層が必要であると言える． そのため，Xu *et al*.(2018a)ではJumping Knowledgeという戦略が提案されている． この戦略は，異なる層から得られる各ノードの隠れ表現を柔軟に組み合わせて，最終的な表現を作成する方法である． 具体的には，ノード $v_i$ の $1$ 番目から最後の $L$ 番目までの隠れ表現を $\symbf{F}^{(1)}\_i,\dots,\symbf{F}^{(L)}\_i$ としたとき，これらを組み合わせて以下のようなノード $v_i$ の最終的な表現を生成する：  

$$
 \symbf{F}^{o}_i = \operatorname{JK}\left(\symbf{F}^{(0)}_i,\symbf{F}^{(1)}_i,\dots,\symbf{F}^{(L)}_i\right) $$


  ここで， $\operatorname{JK}\left(\cdot\right)$ は各ノードに適応する関数であり，具体的には「maxプーリング操作」や「アテンションを取り入れたLSTM」として実装することができる．

### DropEdge

DropEdge (Rong *et al*., 2020)はOversmoothing問題を緩和するために導入されたもので，トレーニングの各エポック中にグラフの一部のエッジをランダムに削除する手法である． 具体的には，各エポックの学習前に，エッジ集合 $\symscr{E}$ から一様にエッジをサンプリング率 $p$ で選び，その結果得られる部分集合を $\symscr{E}\_p$ として取り出す． サンプリングされたエッジをエッジ集合から取り除くと，残りのエッジは $\symscr{E}\_r = \symscr{E}/\symscr{E}\_p$ と表される． その後，エポックの学習にはグラフ $\symscr{G}^{\prime} = \left\\{\symscr{V},\symscr{E}\_r\right\\}$ が使用されることになる．

### PairNorm

良い分類性能を保証するために，ノード表現はある程度滑らかさを持つことが望ましいことは既に述べたことだが，それらのノード表現が過度に似通ってしまう自体は避けたい． これに対する直感的なアイデアとして，接続されていないノード間の表現が相対的に離れていることを保証すればよい． そこで，Zhao and Akoglu(2019)では，正則化項を導入して，接続されていないノード表現が異なるように強制する手法であるPairNormが提案されている．


[メインページ](../../index.markdown)

[章目次](./chap14.md)

[前の節へ](./subsection_01.md) [次の節へ](./subsection_03.md)


