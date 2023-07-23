[メインページ](../../index.markdown)

[章目次](./chap12.md)
## 12.1. はじめに

データマイニングでは，パターンや知識を大量のデータから抽出することを目指している(Han *et al*., 2011)． 現実世界で得られる数多くのデータは，そのままグラフとして表現できる形を持っている． 例えばWeb上においては，Facebookの友だち関係やTwitterのフォロー関係といった「SNSユーザ間の関係」はソーシャルグラフとして，また，ECサイトにおける「ユーザとアイテム間の過去のインタラクション」は二部グラフとして表現できる． この二部グラフでは，ユーザとアイテムを2つのノード集合とし，それらの間のインタラクションがエッジを形成する． さらに，都市・地域内の道路および道路の一区画は，それらの間の空間的関係（どのように配置され，どのように互いに接続しているか等の関係）を通して相互に依存していることが多い． これらの空間的関係は，道路や道路の一区画をノードとし，それらの間の空間的関係をエッジとした交通ネットワークによって表現することができる．

以上のことから，グラフニューラルネットワークはデータマイニングの様々なタスクの推進にごく自然に適用されてきている． 本章では，Webデータマイニング，都市データマイニング，サイバーセキュリティデータマイニングといった代表的なデータマイニングタスクにGNNがどのように適用されるかをみていく．


[メインページ](../../index.markdown)

[章目次](./chap12.md)

[前の節へ](./subsection_00.md) [次の節へ](./subsection_02.md)

