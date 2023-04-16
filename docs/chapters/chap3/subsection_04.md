[メインページ](../../index.markdown)

[章目次](./chap3.md)
## 3.4. リカレントニューラルネットワーク

音声認識, 機械翻訳, センチメント分類などの多くのタスクにおいて,
系列データを扱う必要がある. 機械翻訳は,
ある言語の文（単語の列）が与えられたときに,
それを別の言語に翻訳することを目的とする. したがって,
入力と出力の両方が系列データである. センチメント分類は,
与えられた文や文書のセンチメントを予測するもので, 入力は系列データで,
出力はセンチメントの分類を示す値である. こうしたタスクのために,
系列データの各要素を入力層の入力ユニットと見なし,
標準的なニューラルネットワークモデルを使えばよいと考えるかもしれない.
しかし, この戦略は2つの主な理由により,
系列データに対してはうまくいかない. まず,
標準的なネットワークモデルでは,
入力と出力のサイズが固定されていることが多いが,
系列データの入力や出力はサンプルによって異なる長さを持つことがある.
次に, より重要なこととして, 標準的なネットワークモデルでは,
系列の異なる位置からの入力を扱うためのパラメータが共有されていない.
例えば, 言語関連のタスクでは, \"I went to the Yellow Stone National park
last summer\" と \"Last summer, I went to the Yellow Stone National
park\" という2つの文が与えられたとする. 学習モデルは,
両方の文において時期は\"last summer\"であると解釈してほしいが, \"last
summer\"という情報はそれぞれの文で異なる位置に現れる.
これを扱う自然な方法は, CNNと同様にパラメータを共有することである.
以上の2つの課題を解決するために登場したのが, RNN（Recurrent Neural
Network）である. RNNは, 系列の各要素に同じ関数を再帰的に適用する.
系列のすべての位置が同じ関数で処理されるため,
異なる位置間でのパラメータの共有が自然に実現される. 一方で,
系列の長さに関係なく同じ関数を繰り返し適用できるため,
長さの異なる配列を本質的に扱うことができる.

### 従来のRNNの構成

長さ $n$ の系列は,
（ $\mathbf{x^{(1)}},\mathbf{x^{(2)}},...,\mathbf{x^{(n)}}$ ）と表すことができる.
図3.12に示すように, 従来のRNNモデルでは, 系列の要素を1つずつ取り込み,
ニューラルネットワークのブロックで処理していた.
ニューラルネットワークのブロックは,
系列の要素だけでなく前のブロックから流れてきた情報も入力として受け取ることが多い.
その結果、系列の初期位置の情報が系列全体に流れることができる.
各ニューラルネットワークのブロックは同一である. なお,
図3.12のRNNモデルは各位置 $i$ に出力 $\mathbf{y^{(i)}}$ を持つが,
これはRNNモデルには必須ではない.

ニューラルネットワークのブロックは, 2つの入力と2つの出力をもつ.
出力を $\mathbf{y^{(i)}}$ ,
次の位置に流れる情報を $\mathbf{h^{(i)}}$ とする.
最初の要素 $\mathbf{h^{(0)}}$ は0で初期化されることが多い.
 $i$ 番目の要素を処理する手順は, 次のように定式化できる：

 

$$ \begin{array}{l}\mathbf{h}^{(i)}=\alpha_h\left(\mathbf{W}_{hh} \cdot \mathbf{h}^{(i-1)}+\mathbf{W}_{hx} \mathbf{x}^{(i-1)}+\mathbf{b}_h\right) \nonumber \\ \mathbf{y}^{(i)}=\alpha_y\left(\mathbf{W}_{yh} \mathbf{h}^{(i)}+\mathbf{b}_y\right)\end{array}\nonumber $$

 

ここで、 $\mathbf{W}\_{hh},\mathbf{W}\_{hx},\mathbf{W}\_{yh}$ は線形変換をする行列で,
 $\mathbf{b}_h, \mathbf{b}_y$ はバイアス項,
 $\alpha_h, \alpha_y$ は活性化関数を表す.

系列データを扱う場合,
系列の長期的な依存関係を捉えることが非常に重要となる. 例えば,
言語モデルでは文中で遠くに現れる2つの単語が密接に関連していることがある.
しかし,
従来のRNNモデルは長期的な依存関係を捉えるのが苦手であることが分かっている.
一番の問題は,
多くのネットワークのブロックにわたって勾配が消滅するか爆発しがちであるということだ.
この2つの現象は, 学習のプロセスで問題を引き起こす.
勾配爆発は最適化に影響を及ぼし,
勾配消失は後の位置の情報が前の位置の計算に伝わりづらくなってしまう.
これらの問題を解決するため, ゲート付きRNNモデルが提案されている.
代表的なゲート付きRNNモデルとして, LSTM (Long short-term memory)
(Hochreiter and Schmidhuber, 1997) とGRU (gated recurrent unit) (Cho et
al., 2014a) がある.

<figure>

<img src="./fig/fig3_12.png" width="100%"/>

<figcaption>図3.12 従来のRNNの構成</figcaption>

</figure>

### LSTM

LSTMの全体的な構造は従来のRNNモデルと同じである. また,
系列の要素に同一のニューラルネットワークを適用するチェーン構造も持っている.
重要な違いは, LSTMの情報の流れを制御するため,
ゲート・ユニットが導入されている点である. 図3.13に示すように,
系列内の連続した位置を流れる情報にはセル状態 $\mathbf{C}^{(t-1)}$ と隠れ状態 $\mathbf{h}^{(t-1)}$ が含まれている.
セル状態は前の状態から次の状態へ情報を伝達し,
隠れ状態はどのようにして情報を伝達するのかを決める.
セル状態 $\mathbf{h}^{(t)}$ は,
必要に応じて（例えば入力も出力も系列であるようなタスクなどで）この位置の出力としても機能する.

<figure>

<img src="./fig/fig3_13.png" width="100%"/>

<figcaption>図3.13 LSTMの構成ブロック</figcaption>

</figure>

LSTMではまず最初のステップで,
前のセルから来た情報のうち何を捨てるかを決める.
この決定は「忘却ゲート」で行われる.
忘却ゲートでは前の隠れ状態 $\mathbf{h}^{(t-1)}$ と新しい入力 $\mathbf{x}^{(t)}$ をもとにして,0から1の値を出力し,
セル状態 $\mathbf{C}^{(t-1)}$ に渡される. 各要素の0から1の値は,
どの程度の情報を捨てるかを表す. 出力は,
セル状態 $\mathbf{C}^{(t-1)}$ と同じ次元をもつベクトル $\mathbf{f}_t$ に集約される.
具体的には, 忘却ゲートは以下のように定式化される：

 

$$ \mathbf{f}_t=\sigma\left(\mathbf{W}_f \cdot \mathbf{x}^{(t)}+\mathbf{U}_f \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_f\right)\nonumber $$

 

ここで,  $\mathbf{W}_f$ と $\mathbf{U}_f$ はパラメータで,
 $\mathbf{b}_f$ はバイアス項,
 $\sigma()$ はシグモイド関数で入力を0から1に変換する.

次のステップで,
入力 $\mathbf{x}^{(t)}$ のうち新しいセル状態に何を保存するかを決める.
この決定は「忘却ゲート」と同様に, 「入力ゲート」で行われる.
入力ゲートは以下のように定式化される：
 

$$ \mathbf{i}_t=\sigma\left(\mathbf{W}_i \cdot \mathbf{x}^{(t)}+\mathbf{U}_i \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_i\right)\nonumber $$

 
入力 $\mathbf{x}^{(t)}$ を数層のニューラルネットワークで処理することで候補セル $\tilde{\mathbf{C}}^{(t)}$ を生成し,
これを使ってセル状態を更新する. このプロセスは次のように書ける：

 

$$ \tilde{\mathbf{C}}^{(t)}=\tanh \left(\mathbf{W}_c \cdot \mathbf{x}^{(t)}+\mathbf{U}_c \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_c\right)\nonumber $$

 

その後,
古いセル状態 $\mathbf{C}^{(t-1)}$ と新しい候補セル $\tilde{\mathbf{C}}^{(t)}$ を用いて,
新しいセル状態 $\mathbf{C}^{(t)}$ を生成する：

 

$$ \mathbf{C}^{(t)}=\mathbf{f}_t \odot \mathbf{C}^{(t-1)}+\mathbf{i}_t \odot \tilde{\mathbf{C}}^{(t)}\nonumber $$

 

ここで,  $\odot$ という記号はアダマール積（成分ごとに積をとる）を表す.

最後のステップで, 隠れ状態 $\mathbf{h}^{(t)}$ を生成し,
これが次のブロックに流れていく.
また、場合によってはこれがこのブロックでの出力となる.
この隠れ状態は更新されたセル状態 $\mathbf{C}^{(t)}$ に基づいており,
「出力ゲート」がセル状態のどの部分を保存するかを決定する.
出力ゲートは忘却ゲートと入力ゲートと同じように次のように定式化される：

 

$$ \mathbf{o}_t=\sigma\left(\mathbf{W}_o \cdot \mathbf{x}^{(t)}+\mathbf{U}_o \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_o\right)\nonumber $$

 

新しい隠れ状態 $\mathbf{h}^{(t)}$ は次のように生成される：

 

$$ \mathbf{h}^{(t)}=\mathbf{o}_t \odot \tanh \left(\mathbf{C}^{(t)}\right)\nonumber $$

 

LSTMの全体のプロセスは次のようにまとめることができる：

 $$ \begin{array}
    {l}\mathbf{f}_t=\sigma\left(\mathbf{W}_f \cdot \mathbf{x}^{(t)}+\mathbf{U}_f \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_f\right) \\ \mathbf{i}_t=\sigma\left(\mathbf{W}_i \cdot \mathbf{x}^{(t)}+\mathbf{U}_i \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_i\right) \\ \mathbf{o}_t=\sigma\left(\mathbf{W}_o \cdot \mathbf{x}^{(t)}+\mathbf{U}_o \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_o\right) \\ \tilde{\mathbf{C}}^{(t)}=\tanh \left(\mathbf{W}_c \cdot \mathbf{x}^{(t)}+\mathbf{U}_c \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_c\right) \\ \mathbf{C}^{(t)}=\mathbf{f}_t \odot \mathbf{C}^{(t-1)}+\mathbf{i}_t \odot \tilde{\mathbf{C}}^{(t)} \\ \mathbf{h}^{(t)}=\mathbf{o}_t \odot \tanh \left(\mathbf{C}^{(t)}\right)
    \end{array}
    
\tag{3.4} $$ 

便宜上, 式(3.4)で表される,
i番目の位置のLSTMのニューラルネットワークを次のように書く：

 $$ \mathbf{C}^{(t)}, \mathbf{h}^{(t)}=\operatorname{LSTM}\left(\mathbf{x}^{(t)}, \mathbf{C}^{(t-1)}, \mathbf{h}^{(t-1)}\right)
    
\tag{3.5} $$ 

### GRU

図3.14のGRU（Gated recurrent unit）は, LSTMを変形した構造をしており,
LSTMの忘却ゲートと入力ゲートを「更新ゲート」として統合し,
セル状態と隠れ状態を同じものとして統合したものと見なすことができる.
これらの変更により,
よりシンプルなゲート付きRNNモデルを構成することができる.
GRUは以下のように定式化される：

 $$ \begin{array}{l}\mathbf{z}_t=\sigma\left(\mathbf{W}_z \cdot \mathbf{x}^{(t)}+\mathbf{U}_z \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_z\right) \\ \mathbf{r}_t=\sigma\left(\mathbf{W}_r \cdot \mathbf{x}^{(t)}+\mathbf{U}_r \cdot \mathbf{h}^{(t-1)}+\mathbf{b}_r\right) \\ \tilde{\mathbf{h}}^{(t)}=\tanh \left(\mathbf{W} \cdot \mathbf{x}^{(t)}+\mathbf{U} \cdot\left(\mathbf{r}_t \odot \mathbf{h}^{(t-1)}\right)+\mathbf{b}\right) \\ \mathbf{h}^{(t)}=\left(\mathbf{1}-\mathbf{z}_t\right) \odot \tilde{\mathbf{h}}^{(t)}+\mathbf{z}_t \odot \mathbf{h}^{(t-1)}\end{array}
        
\tag{3.6} $$ 

ここで,  $\mathbf{z}_t$ は更新ゲートであり,
 $\mathbf{r}_t$ はリセットゲートである. 便宜上,
この式(3.6)のプロセスを次のようにまとめる：

 $$ \mathbf{h}^{(t)}=\operatorname{GRU}\left(\mathbf{x}^{(t)}, \mathbf{h}^{(t-1)}\right)
    
\tag{3.7} $$ 

<figure>

<img src="./fig/fig3_14.png" width="100%"/>

<figcaption>図3.14 GRUの構成ブロック</figcaption>

</figure>


[メインページ](../../index.markdown)

[章目次](./chap3.md)

[前の節へ](./subsection_03.md) [次の節へ](./subsection_05.md)


