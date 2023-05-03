[メインページ](../../index.markdown)

[章目次](./chap5.md)
## 5.7. 参考文献

本章で紹介したグラフニューラルネットワークのモデル以外にも，ニューラルネットワークを使ってグラフ分類タスクに対してグラフ全体での表現を学習しようとする研究も存在する(Yanardag and Vishwanathan, 2015; Niepert *et al*., 2016; Lee *et al*., 2018)． 本章で導入した，グラフフィルタやグラフプーリング操作に加えて，他のフィルタ操作，プーリング操作もある(Li *et al*., 2018c; Gao *et al*., 2018a; Zhang *et al*., 2018a; Liu *et al*., 2019b; Velickovic *et al*., 2019; Morris *et al*., 2019; Gao *et al*., 2020; Yuan and Ji, 2019)． また，グラフニューラルネットワークのモデルについて異なる観点からのレビューもある(Zhou *et al*., 2018a; Wu *et al*., 2020; Zhang *et al*., 2018c)． グラフニューラルネットワークの研究は注目が高まっているので，グラフニューラルネットワークのモデルを構築するための使いやすいライブラリが複数開発されている． *Pytorch Geometric* (PyG) (Fey and Lenssen, 2019)はPyTorchベースで開発されている． *Deep Graph Library* (DGL) (Wang *et al*., 2019e)はPyTorchやTensorflowを含む様々な深層学習ライブラリ上でバックエンドとして動かすことができる．


$$ \symbf{U}\left(\symbf{I} + \symbf{D}^{-\tfrac{1}{2}}\symbf{A}\symbf{D}^{-\tfrac{1}{2}}\right)\symbf{U}^{\top} =\symbf{U}\left(2\symbf{I} - (\symbf{I} - \symbf{D}^{-\tfrac{1}{2}}\symbf{A}\symbf{D}^{-\tfrac{1}{2}})\right)\symbf{U}^{\top} = 2\symbf{I} - \symbf{\Lambda}. $$

  $\symbf{L}$ の固有値は非負(定理2.30)であり，GCNにおいては $\lambda_{\text{max}}\approx 2$ としているので，行列 $\symbf{I} + \symbf{D}^{-\tfrac{1}{2}}\symbf{A}\symbf{D}^{-\tfrac{1}{2}}$ の固有値は $[0,\,2]$ に収まることがわかる．

[メインページ](../../index.markdown)

[章目次](./chap5.md)

[前の節へ](./subsection_06.md) [次の節へ](./subsection_08.md)

[^1]: 訳注： $\symbf{L}$ の固有ベクトルで構成される $\symbf{U}$ を用いて対角化を行う． 
