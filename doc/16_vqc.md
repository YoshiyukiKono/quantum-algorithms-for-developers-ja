以下は **フラットな Markdown** です。そのまま
`doc/16_vqc.md` として保存できます。

---

# doc/16_vqc.md

# 第16回 変分量子分類器（VQC）を実装する

## はじめに

前回は：

* 量子カーネル法
* ZZFeatureMap
* QuantumKernel
* カーネル行列
* 量子SVM分類

を扱いました。

量子カーネル法では：

量子回路は

**特徴抽出器**

として機能しました。

今回は：

量子回路そのものを

**学習モデルとして使う**

方法を扱います。

テーマは：

**変分量子分類器（VQC: Variational Quantum Classifier）**

です。

---

# VQCとは何か？

VQCとは：

パラメータ付き量子回路

を使った分類モデルです。

構造：

入力データ
→ 特徴マップ
→ 変分回路
→ 測定
→ 出力

つまり：

量子版ニューラルネット

です。

---

# VQCの基本構造

VQCは：

次の3要素で構成されます：

① 特徴マップ
② 変分回路
③ 古典最適化

流れ：

データ入力
↓
量子回路適用
↓
測定
↓
損失関数計算
↓
パラメータ更新

です。

---

# 特徴マップを準備する

まず：

入力データを量子状態へ変換します。

例：

```python
from qiskit.circuit.library import ZZFeatureMap

feature_map = ZZFeatureMap(feature_dimension=2, reps=1)
```

ここでは：

2次元データ

を扱います。

---

# 変分回路とは何か？

変分回路とは：

学習対象となる量子回路

です。

例：

```python
from qiskit.circuit.library import RealAmplitudes

ansatz = RealAmplitudes(num_qubits=2, reps=2)
```

この回路には：

学習可能パラメータ

が含まれています。

---

# RealAmplitudesとは何か？

RealAmplitudesは：

代表的な変分回路テンプレートです。

特徴：

* 回転ゲートを含む
* エンタングルメントを含む
* 学習しやすい構造

VQCでよく使われます。

---

# データセットを用意する

簡単な分類データ：

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

# VQCを作成する

次に：

Variational Quantum Classifier

を作ります。

```python
from qiskit_machine_learning.algorithms.classifiers import VQC
from qiskit_aer import AerSimulator

backend = AerSimulator()

vqc = VQC(
    feature_map=feature_map,
    ansatz=ansatz,
    optimizer=None,
    quantum_instance=backend
)
```

これで：

学習モデルが完成しました。

---

# optimizerを設定する

通常は：

最適化アルゴリズム

を指定します。

例：

```python
from qiskit.algorithms.optimizers import COBYLA

optimizer = COBYLA(maxiter=100)
```

VQCへ適用：

```python
vqc = VQC(
    feature_map=feature_map,
    ansatz=ansatz,
    optimizer=optimizer,
    quantum_instance=backend
)
```

---

# モデルを学習する

次に：

学習を実行します。

```python
vqc.fit(X_train, y_train)
```

これで：

回路パラメータが最適化されます。

---

# 分類を実行する

学習後：

予測が可能になります。

```python
predictions = vqc.predict(X_train)

print(predictions)
```

結果：

```text
[0 0 1 1]
```

のようになります。

---

# 内部で何が起きているのか？

VQCでは：

次の処理が行われています：

① データを量子状態へ変換
② 変分回路を適用
③ 測定確率を取得
④ 損失関数計算
⑤ パラメータ更新

これを繰り返しています。

---

# 損失関数とは何か？

損失関数とは：

予測と正解のズレ

を測る関数です。

例：

分類問題：

```text
loss = predicted − true
```

この値を：

最小化

します。

---

# なぜ変分回路が分類できるのか？

理由：

量子回路は

非線形変換

を作れるからです。

つまり：

線形分離できない問題

にも対応できます。

---

# カーネル法との違い

量子カーネル：

特徴抽出のみ

VQC：

モデル自体を学習

つまり：

VQCの方が柔軟です。

---

# VQCのメリット

メリット：

回路構造を調整できる
特徴抽出と分類を同時に行う
非線形問題に強い

つまり：

表現能力が高い

モデルです。

---

# VQCの課題

一方：

課題もあります：

ノイズの影響
局所最適解
計算コスト

つまり：

設計が重要になります。

---

# 実機でVQCは動くのか？

はい、可能です。

ただし：

回路を浅くする必要があります。

理由：

NISQ環境では

深い回路が不安定

だからです。

---

# VQCの応用分野

応用例：

異常検知
金融分類
材料探索
創薬研究
画像認識（研究段階）

などがあります。

---

# 開発者にとっての意味

VQCは：

量子機械学習の中核モデル

です。

理由：

回路設計
最適化
特徴抽出

すべてを統合している

からです。

---

# まとめ

今回は：

* 変分量子分類器（VQC）
* 特徴マップ
* 変分回路
* COBYLA最適化
* 学習と予測

を扱いました。

これで：

量子回路を

**学習可能モデルとして使う方法**

が理解できました。

次回は：

量子回帰モデル

を実装しながら：

連続値予測への応用

を扱います。
