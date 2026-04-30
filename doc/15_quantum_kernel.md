以下は **フラットな Markdown** です。そのまま
`doc/15_quantum_kernel.md` として保存できます。

---

# doc/15_quantum_kernel.md

# 第15回 量子カーネル法を実装する

## はじめに

前回は：

* 量子機械学習とは何か
* 特徴マップ
* 量子分類器
* 量子ニューラルネット
* 量子カーネル法の概念

を扱いました。

今回は：

**量子カーネル法を実装する**

ことで：

量子機械学習の最も重要な実践手法の一つ

を理解します。

---

# カーネル法とは何か？

まず：

古典機械学習における

カーネル法

を整理します。

カーネル法とは：

データを高次元空間に写像して

分類を容易にする方法

です。

例：

```text
低次元では分離できないデータ
→
高次元では分離できる
```

---

# SVMとカーネル

代表的な例：

SVM（サポートベクターマシン）

です。

SVMでは：

データ間の類似度

を計算します。

通常：

K(x, y)

という関数を使います。

これが：

カーネル関数

です。

---

# 量子カーネルとは何か？

量子カーネルでは：

類似度を

量子状態の内積

として計算します。

つまり：

```text
K(x, y) = ⟨ψ(x)|ψ(y)⟩
```

になります。

ここで：

|ψ(x)⟩

は：

特徴マップによって生成された

量子状態です。

---

# なぜ量子カーネルが強力なのか？

理由：

量子状態空間は

指数的に大きい

からです。

つまり：

古典的には扱えない特徴空間

を自然に扱えます。

---

# 特徴マップを作る

まず：

データを量子状態へ変換します。

例：

```python
from qiskit.circuit.library import ZZFeatureMap

feature_map = ZZFeatureMap(feature_dimension=2, reps=2)
```

ここで：

feature_dimension

は：

入力データの次元です。

---

# ZZFeatureMapとは何か？

ZZFeatureMapは：

量子カーネル法でよく使われる

特徴マップです。

特徴：

* エンタングルメントを含む
* 非線形特徴空間を作る
* 分類性能が高い

という利点があります。

---

# データを準備する

今回は：

簡単な2次元データを使います。

例：

```python
import numpy as np

X_train = np.array([
    [0.1, 0.2],
    [0.2, 0.1],
    [0.9, 0.8],
    [0.8, 0.9]
])

y_train = np.array([0, 0, 1, 1])
```

これは：

2クラス分類問題

です。

---

# 量子カーネルを作る

次に：

QuantumKernel

を使います。

```python
from qiskit_machine_learning.kernels import QuantumKernel
from qiskit_aer import AerSimulator

backend = AerSimulator()

quantum_kernel = QuantumKernel(
    feature_map=feature_map,
    quantum_instance=backend
)
```

これで：

量子カーネル関数

が定義されました。

---

# カーネル行列を計算する

次に：

カーネル行列を作ります。

```python
kernel_matrix = quantum_kernel.evaluate(X_train)

print(kernel_matrix)
```

これは：

データ間の類似度行列

です。

---

# カーネル行列とは何か？

カーネル行列とは：

すべてのデータペアについて：

```text
K(x_i, x_j)
```

を並べた行列です。

つまり：

分類器が使う特徴情報

になります。

---

# SVMと組み合わせる

次に：

scikit-learnのSVM

と組み合わせます。

```python
from sklearn.svm import SVC

svm = SVC(kernel="precomputed")

svm.fit(kernel_matrix, y_train)
```

これで：

量子カーネルSVM

が完成しました。

---

# 新しいデータを分類する

テストデータを用意します：

```python
X_test = np.array([
    [0.05, 0.1],
    [0.95, 0.85]
])
```

カーネル行列を計算：

```python
kernel_test = quantum_kernel.evaluate(
    X_test,
    X_train
)
```

分類：

```python
predictions = svm.predict(kernel_test)

print(predictions)
```

これで：

分類結果が得られます。

---

# 何が起きているのか？

内部では：

次の処理が行われています：

① データを量子状態へ変換
② 状態の重なりを計算
③ 類似度行列を生成
④ SVMで分類

つまり：

量子回路が特徴抽出器

として働いています。

---

# なぜ量子回路が特徴抽出になるのか？

理由：

量子回路は

非線形変換

を自然に作れるからです。

古典では：

カーネル関数を設計する必要があります。

量子では：

回路がそれを自動生成します。

---

# 実機で量子カーネルは使えるのか？

はい、可能です。

ただし：

注意点があります：

* ノイズの影響が大きい
* 測定回数が必要
* 回路深さを制限する必要がある

つまり：

NISQ向け設計

が重要になります。

---

# 量子カーネル法の応用例

応用分野：

異常検知
金融分類
創薬データ分析
材料探索
画像分類（研究段階）

などがあります。

---

# 量子ニューラルネットとの違い

量子カーネル：

特徴抽出＋古典分類器

量子ニューラルネット：

回路自体を学習

つまり：

役割が異なります。

---

# 開発者にとっての意味

量子カーネル法は：

最も実装しやすい

量子機械学習手法です。

理由：

古典SVMと統合できる

からです。

つまり：

既存のML知識が活かせます。

---

# まとめ

今回は：

* 量子カーネルとは何か
* ZZFeatureMap
* QuantumKernel
* カーネル行列
* 量子SVM分類

を扱いました。

これで：

量子回路を

特徴抽出器として使う方法

が理解できました。

次回は：

変分量子分類器（VQC）

を実装しながら：

学習可能な量子モデル

を作っていきます。
