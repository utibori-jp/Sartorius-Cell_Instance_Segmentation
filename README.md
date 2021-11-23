# Sartorius - Cell Instance Segmentation
This is a repository for [Sartorius - Cell Instance Segmentation](https://www.kaggle.com/c/sartorius-cell-instance-segmentation)

![](https://github.com/utibori-jp/Sartorius-Cell_Instance_Segmentation/blob/main/images/messageImage_1636380621699.jpg)

- Directory tree
```
Sartorius - Cell Instance Segmentation
├── README.md
├── data         <---- gitで管理するデータ
├── data_ignore  <---- .gitignoreに記述されているディレクトリ(モデルとか、特徴量とか、データセットとか)
├── nb           <---- jupyter lab で作業したノートブック
├── nb_download  <---- ダウンロードした公開されているkagglenb
├── image        <---- imageを保存
└── src          <---- .ipynb 以外のコード
```

## Basics
### Description(DeepL)
アルツハイマー病などの神経変性疾患や脳腫瘍などの神経疾患は、世界中で死亡や障害の主な原因となっています。しかし、これらの致命的な疾患が治療にどの程度反応するかを定量化することは困難です。その方法の一つとして、光学顕微鏡で神経細胞を観察する方法があります。しかし、顕微鏡画像の中で個々の神経細胞をセグメント化することは、困難で時間のかかる作業です。コンピュータビジョンを用いて神経細胞を正確に分割すれば、何百万人もの患者を治療するための効果的な新薬の発見につながる可能性がある。

現在のソリューションでは、特に神経細胞の精度に限界があります。細胞インスタンスのセグメンテーションモデルを開発するための社内研究では、神経芽腫細胞株SH-SY5Yが、テストした8種類の異なるがん細胞の中で、常に最も低い精度のスコアを示しています。これは、神経細胞が非常にユニークな不規則で凹んだ形態をしているため、一般的に使用されているマスクヘッドではセグメント化が難しいためと考えられます。

ザルトリウスは、ライフサイエンス研究およびバイオ医薬品業界のパートナーです。科学者やエンジニアが、ライフサイエンスやバイオプロセスを簡素化し、進歩を加速させることで、より良い治療法や安価な医療の開発を可能にしています。また、この分野のパイオニアや第一人者にとっては、魅力的でダイナミックなプラットフォームとなっています。より多くの人々の健康につながる技術的なブレークスルーという共通の目標のために、創造的な頭脳が集まっています。

このコンペティションでは、神経疾患の研究によく使われる神経細胞の種類を示す生物学的画像から、関心のある対象物を検出し、明確に描き出します。具体的には、位相差顕微鏡画像を用いて、神経細胞のセグメンテーションを行うためのモデルを学習・検証していただきます。成功したモデルは、高いレベルの精度でこれを行うことができます。

成功すれば、確実な定量的データの収集により、神経生物学の研究を促進することができます。研究者は、このデータを使って、病気や治療条件が神経細胞に与える影響をより簡単に測定できるようになるかもしれません。その結果、これらの主要な死因や障害を持つ何百万人もの人々を治療するための新しい薬が発見されるかもしれません。

### Evaluation
この競争は、異なるIoU（intersection over union）しきい値における平均平均精度で評価されます。提案されたオブジェクトピクセルのセットと真のオブジェクトピクセルのセットのIoUは次のように計算されます。

![\begin{align*}
IoU(A, B)&=\frac{A \cap B}{A \cup B}
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0AIoU%28A%2C+B%29%26%3D%5Cfrac%7BA+%5Ccap+B%7D%7BA+%5Ccup+B%7D%0A%5Cend%7Balign%2A%7D%0A)

この指標は，IoUのしきい値の範囲を走査し，各ポイントで平均精度値を計算します．閾値の範囲は0.5から0.95で，ステップサイズは0.05です:`(0.5，0.55，0.6，0.65，0.7，0.75，0.8，0.85，0.9，0.95)`。つまり，閾値が0.5の場合，グランドトゥルースオブジェクトとの積和が0.5以上であれば，予測オブジェクトが「ヒット」したと考えられる．

各閾値において，予測オブジェクトをすべてのグランドトゥルースオブジェクトと比較した結果の真陽性（TP），偽陰性（FN），および偽陽性（FP）の数に基づいて精度値が計算される。

![\begin{align*}
\frac{TP(t)}{TP(t)+FP(t)+FM(t)}
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0A%5Cfrac%7BTP%28t%29%7D%7BTP%28t%29%2BFP%28t%29%2BFM%28t%29%7D%0A%5Cend%7Balign%2A%7D%0A)

真陽性とは，1つの予測オブジェクトが，閾値以上のIoUを持つグランドトゥルースオブジェクトと一致した場合にカウントされます。偽陽性は，予測されたオブジェクトに関連する地上の真実のオブジェクトがないことを示します．偽陰性は，関連する予測オブジェクトを持たない地上の真実のオブジェクトを示します．そして，1枚の画像の平均精度は，各IoU閾値における上記精度値の平均として計算されます。

![\begin{align*}
\frac{1}{ \mid thresholds \mid } \Sigma \frac{TP(t)}{TP(t)+FP(t)+FM(t)}
\end{align*}
](https://render.githubusercontent.com/render/math?math=%5Cdisplaystyle+%5Cbegin%7Balign%2A%7D%0A%5Cfrac%7B1%7D%7B+%5Cmid+thresholds+%5Cmid+%7D+%5CSigma+%5Cfrac%7BTP%28t%29%7D%7BTP%28t%29%2BFP%28t%29%2BFM%28t%29%7D%0A%5Cend%7Balign%2A%7D%0A)
 
最後に，競争指標が返すスコアは，テストデータセット内の各画像の平均精度の平均値です．

### Data
このコンテストでは、画像中の神経細胞を分割しています。学習用のアノテーションはランレングス符号化されたマスクとして提供され、画像はPNG形式です。画像の数は少ないですが、アノテーションされたオブジェクトの数は非常に多くなっています。隠れたテストセットはおよそ240枚の画像です。

注：予測値は重ならないようになっていますが、トレーニングラベルは（重なっている部分も含めて）完全に提供されます。これは、モデルに各オブジェクトの完全なデータを提供するためです。予測値の重複を取り除くことは、競技者の課題です。

#### Files
train.csv - すべてのトレーニングオブジェクトのIDとマスク。テストセットにはこのようなメタデータは提供されません。
* id - オブジェクトのユニークな識別子
* annotation - 識別されたニューロンセルのランレングスエンコードされたピクセル
* width - 入力画像の幅
* height - ソース画像の高さ
* cell_type - 細胞の種類。
* plate_time - プレートが作成された時間
* sample_date - サンプルが作成された日付
* sample_id - サンプルの識別子
* elapsed_timedelta - サンプルの最初の画像が撮影されてからの時間

sample_submission.csv - 正しいフォーマットで作成されたサンプル提出用ファイル

train - PNG形式の訓練用画像

test - PNG形式のテスト画像．一部のテスト画像はダウンロードできますが，残りの画像は投稿時にノートPCからのみアクセスできます．

train_semi_supervised - 半教師付きアプローチのための追加データを使用したい場合に提供されるラベルなしの画像です。

LIVECell_dataset_2021 - LIVECellデータセットのデータのミラーです。LIVECellは、このコンペティションの前身となるデータセットです。SH-SHY5Y細胞株の追加データに加えて、転送学習に興味があるかもしれない、コンペティションのデータセットではカバーされていない他のいくつかの細胞株のデータがあります。

## Log
### 2021/11/09 ~ 2021/11/
設置予定

## Reference
* [Sartorius - Starter Baseline Torce U-net](https://www.kaggle.com/julian3833/sartorius-starter-baseline-torch-u-net)
* [[TRAIN]Sartorius Segmentation EDA+EffDET[TF]](https://www.kaggle.com/julian3833/sartorius-starter-baseline-torch-u-net)
* [Sartorius-starter](https://www.kaggle.com/drcapa/sartorius-starter?scriptVersionId=78458095)
* [Beginner_Starter](https://www.kaggle.com/kaitohonda/beginner-starter)
* [ Sartorius - Starter Torch Mask R-CNN [LB=0.273]](https://www.kaggle.com/julian3833/sartorius-starter-torch-mask-r-cnn-lb-0-273)






