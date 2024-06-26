# Classification with an Academic Success Dataset
https://www.kaggle.com/competitions/playground-series-s4e6<br>
Playground Series<br>
目的：高等教育における学生の退学リスクを予測する<br>
Private Score：0.83827<br>
最終順位：31/2739（上位2%）<br>
![image](https://github.com/sayaka513/kaggle_academic_success/assets/160210603/66d2ff7c-ed3c-4d69-a9af-45b0e803c864)<br>
<br>
## 説明
このコンペ用のtrain・testデータセットは「<a href="https://archive.ics.uci.edu/dataset/697/predict+students+dropout+and+academic+success">学生のドロップアウトと学業成績の予測データセット</a>」でトレーニングされたディープラーニングモデルから生成されており、originalデータは自由に使用可能となっている。<br>
通常コース期間終了時の学生の状況は3つのカテゴリ (中退、入学、卒業) に分類されており、これらがTargetである。<br>
評価はaccuracyで行う。<br>
<br>
## EDA
### カテゴリ変数の対応
欠損値はなく（不明値やnullは0で入力されている）、Target列以外の列は全て数値型のデータであった。
しかし、originalデータ提供先を確認し、一部の数値データが名義尺度であることを確認した。<br><br>
例：Marital statusについて、<br>
1 – single 2 – married 3 – widower 4 – divorced 5 – facto union 6 – legally separated<br><br>
このような特徴量についてはOne-Hot エンコーディングを行った。<br>
<br>
### 標準化
目視で正規分布の形に近いものはStandardScaler、<br>他、正規分布とは遠いもの・外れ値が顕著なものはRobustScalerを用いたスケーリングを行った。<br><br>

【StandardScalerを用いた特徴量の例】<br><br>
![image](https://github.com/sayaka513/kaggle_academic_success/assets/160210603/35114485-ac8b-4966-a17b-740bc88dbcd9)

<br><br>
【RobustScalerを用いた特徴量の例】<br><br>
![image](https://github.com/sayaka513/kaggle_academic_success/assets/160210603/6a6feeb5-b59c-4505-91c1-7de7a81f6925)<br>
<br>
### originalデータの使用有無について
特にTargetと相関の高い３つの特徴量について、trainデータとoriginalデータの分布が近いことをヒストグラムより確認し、サンプル数を増やすために今回は２データの結合を行い、トレーニングデータとして扱うこととした。
<br>![image](https://github.com/sayaka513/kaggle_academic_success/assets/160210603/bc2f8406-5319-41dc-8563-441f4bd69d4f)<br>
<br>
<br>
## モデルと評価
Targetは分布に偏りが発生しているため、StratifiedKFoldを用いたクロスバリデーションを行うこととした。<br><br>
![image](https://github.com/sayaka513/kaggle_academic_success/assets/160210603/038d319d-0488-455d-8bdf-679d96da1316)
<br><br>
XGBClassifier、LGBMClassifier、CatBoostClassifierにおいてクロスバリデーションを用いたRandomSearchにてハイパーパラメーターチューニングを行ったところ、83%以上のaccuracyを得た。
そのため、ベースモデルをXGBClassifier、LGBMClassifier、CatBoostClassifier、メタモデルをLogisticRegressionとし、StackingClassifierを用いて予測性能の向上を計った。

