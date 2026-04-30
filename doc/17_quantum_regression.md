以下は **フラットな Markdown** です。そのまま
`doc/17_quantum_regression.md` として保存できます。

---

# doc/17_quantum_regression.md

# 第17回 量子回帰モデルを実装する

## はじめに

前回は：

* 変分量子分類器（VQC）
* 特徴マップ
* 変分回路（ansatz）
* COBYLA最適化
* 学習と予測

を扱いました。

前回のテーマは：

**離散値（クラス）を予測するモデル**

でした。

今回は：

**連続値を予測するモデル**

つまり：

**量子回帰（Quantum Regression）**

を実装します。

---

# 回帰とは何か？

回帰とは：

連続値を予測する問題

です。

例：

入力：

```text
x = 0.5
```

出力：

```text
y = 0.73
```

のような：

数値そのもの

を予測します。

---

# 分類との違い

分類：

```text
A or B
```

回帰：

```text
0.73
```

つまり：

出力の型

が違います。

---

# 量子回帰の基本構造

量子回帰も：

変分量子回路

を使います。

流れ：

入力データ
↓
特徴マップ
↓
変分回路
↓
測定
↓
期待値を出力

です。

---

# なぜ期待値を使うのか？

量子回路の測定は：

確率分布

を返します。

しかし：

期待値を計算すると：

連続値

として扱えます。

例：

```text
⟨Z⟩ = 0.42
```

これを：

予測値

として使います。

---

# 量子回帰モデルの最小例

まず：

単純な量子回帰回路を作ります。

例：

```python
from qiskit import QuantumCircuit
import numpy as np

def regression_circuit(x, theta):
    qc = QuantumCircuit(1)
    qc.ry(x, 0)
    qc.rz(theta, 0)
    return qc
```

ここで：

x = 入力
theta = 学習パラメータ

です。

---

# 期待値を計算する

次に：

Z演算子の期待値

を求めます。

例：

```python
from qiskit.quantum_info import Statevector, Pauli

def predict(x, theta):
    qc = regression_circuit(x, theta)
    state = Statevector.from_instruction(qc)
    return state.expectation_value(Pauli("Z")).real
```

これが：

モデルの出力

になります。

---

# 学習用データを作る

例：

```python
X_train = np.linspace(0, np.pi, 20)

y_train = np.sin(X_train)
```

つまり：

sin関数

を近似します。

---

# 損失関数を定義する

回帰では：

平均二乗誤差（MSE）

を使います。

例：

```python
def loss(theta):
    predictions = [predict(x, theta) for x in X_train]
    return np.mean((predictions - y_train) ** 2)
```

これを：

最小化

します。

---

# パラメータを最適化する

SciPyで最適化します：

```python
from scipy.optimize import minimize

result = minimize(loss, x0=0.1)

theta_opt = result.x
```

これで：

最適パラメータ

が得られます。

---

# 学習結果を確認する

予測値：

```python
y_pred = [predict(x, theta_opt) for x in X_train]
```

これで：

sin関数の近似

ができます。

---

# なぜ量子回帰が成立するのか？

理由：

量子回路は

非線形関数

を表現できるからです。

つまり：

関数近似器

として動作します。

---

# 量子回帰の直感的理解

流れ：

入力 x
↓
回路変換
↓
量子状態生成
↓
期待値測定
↓
出力 y

これは：

ニューラルネットの構造

とよく似ています。

---

# より実用的な回帰モデルを作る

次に：

2量子ビット回路へ拡張します。

例：

```python
from qiskit.circuit.library import ZZFeatureMap, RealAmplitudes

feature_map = ZZFeatureMap(feature_dimension=1, reps=1)

ansatz = RealAmplitudes(num_qubits=2, reps=2)
```

これで：

表現能力が向上します。

---

# Qiskit Machine Learningで回帰する

Qiskitでは：

NeuralNetworkRegressor

が使えます。

例：

```python
from qiskit_machine_learning.algorithms import NeuralNetworkRegressor
```

これを：

量子ニューラルネットと組み合わせます。

---

# EstimatorQNNを使う

EstimatorQNNは：

期待値ベースの量子モデル

です。

例：

```python
from qiskit_machine_learning.neural_networks import EstimatorQNN
```

これで：

回帰モデルが構築できます。

---

# 回帰モデルを作成する

例：

```python
qnn = EstimatorQNN(
    circuit=ansatz
)

regressor = NeuralNetworkRegressor(
    neural_network=qnn
)
```

これで：

量子回帰モデル

が完成します。

---

# 学習を実行する

例：

```python
regressor.fit(X_train, y_train)
```

これで：

パラメータが更新されます。

---

# 予測する

例：

```python
predictions = regressor.predict(X_train)
```

これで：

連続値予測

ができます。

---

# 量子回帰の応用分野

応用例：

時系列予測
金融価格モデル
材料特性予測
化学エネルギー推定
センサーデータ解析

などがあります。

---

# VQCとの違い

VQC：

分類モデル

量子回帰：

連続値モデル

つまり：

出力形式

が異なります。

---

# 量子ニューラルネットとの関係

量子回帰は：

量子ニューラルネットの

基本応用

です。

つまり：

QNNの第一歩

になります。

---

# NISQ環境での注意点

回帰では：

測定回数が重要

です。

理由：

期待値の精度が

shots数

に依存するからです。

---

# 開発者にとっての意味

量子回帰は：

量子回路を

関数近似器として使う方法

です。

これは：

量子機械学習の中核技術

の一つです。

---

# まとめ

今回は：

* 量子回帰とは何か
* 期待値による出力
* MSE損失関数
* EstimatorQNN
* NeuralNetworkRegressor

を扱いました。

これで：

量子回路を使った

分類と回帰

の両方が実装できるようになりました。

次回は：

量子回路の表現能力（expressibility）

を理解しながら：

なぜ回路設計が重要なのか

を解説していきます。
