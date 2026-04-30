以下は **フラットな Markdown** です。そのまま
`doc/07_qft.md` として保存できます。

---

# doc/07_qft.md

# 第07回 量子フーリエ変換（QFT）

## はじめに

前回は：

* Grover探索アルゴリズム
* オラクル
* Diffusion演算子
* 干渉による確率増幅

を扱いました。

Groverでは：

正解状態の確率を増幅する

という量子計算の特徴を見ました。

今回は：

**量子フーリエ変換（QFT）**

を実装します。

QFTは：

Shorアルゴリズム
位相推定アルゴリズム
周期探索アルゴリズム

の核心にある

最重要量子回路

です。

---

# フーリエ変換とは何か？

フーリエ変換とは：

周期構造を見つける変換

です。

例えば：

時間領域の信号

を

周波数領域

に変換します。

量子フーリエ変換は：

状態ベクトルに対して

同じ操作を行います。

---

# 古典フーリエ変換との違い

古典フーリエ変換：

N log N

量子フーリエ変換：

(log N)²

の計算量になります。

つまり：

指数的高速化

が可能です。

---

# QFTが何をするのか？

QFTは：

状態

|x⟩

を

次の状態に変換します：

(1/√N) Σ exp(2πixk/N)|k⟩

重要なのは：

位相情報を取り出せる

という点です。

---

# なぜQFTが重要なのか？

量子アルゴリズムの多くは：

周期を見つける問題

に帰着できます。

例えば：

Shorアルゴリズム

は：

周期探索問題

です。

つまり：

QFTは

量子アルゴリズムの中心技術

です。

---

# QFT回路の基本構造

QFTは：

次のゲートで構成されます：

Hadamard
制御位相回転
SWAP

これだけです。

つまり：

基本ゲートの組み合わせ

として実装できます。

---

# 2量子ビットQFTを実装する

まず：

2量子ビットQFT

を書いてみます。

コード：

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(2)

qc.h(1)
qc.cp(3.141592653589793/2, 0, 1)

qc.h(0)

qc.swap(0, 1)

qc.draw("text")
```

これが：

最小のQFT回路

です。

---

# 制御位相回転とは何か？

このゲート：

cp(θ)

は：

制御ビットが1のときだけ

位相を回転させます。

今回の例：

θ = π/2

つまり：

90度回転

です。

---

# Qiskitの標準QFTを使う

Qiskitには：

QFT回路が用意されています。

コード：

```python
from qiskit import QuantumCircuit
from qiskit.circuit.library import QFT

qc = QuantumCircuit(3)

qc.append(QFT(3), [0, 1, 2])

qc.draw("text")
```

これで：

3量子ビットQFT

が生成されます。

---

# 状態ベクトルを確認する

実際に：

状態変化を確認します。

コード：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector
from qiskit.circuit.library import QFT

qc = QuantumCircuit(3)

qc.x(0)

qc.append(QFT(3), [0, 1, 2])

state = Statevector.from_instruction(qc)

print(state)
```

これで：

位相情報が展開された状態

を確認できます。

---

# SWAPが必要な理由

QFTでは：

ビット順序が逆転します。

つまり：

入力：

q0 q1 q2

出力：

q2 q1 q0

になります。

これを補正するため：

SWAP

を使います。

---

# QFTの直感的理解

Groverでは：

確率振幅を増幅しました。

QFTでは：

位相構造を展開します。

つまり：

Grover：

確率操作

QFT：

位相操作

です。

量子計算では：

位相が情報

になります。

---

# 位相がなぜ重要なのか？

量子状態：

a|0⟩ + b|1⟩

ではなく：

a|0⟩ + e^(iθ)b|1⟩

の

θ

が重要です。

QFTは：

この位相θを

測定可能な形

に変換します。

---

# QFTの応用例

QFTは：

次のアルゴリズムで使われます：

Shorアルゴリズム
位相推定アルゴリズム
周期探索
量子シミュレーション

つまり：

量子アルゴリズムの基盤

です。

---

# 逆QFTとは何か？

QFTには：

逆変換があります。

inverse QFT

です。

コード：

```python
from qiskit.circuit.library import QFT

QFT(3).inverse()
```

これは：

位相推定アルゴリズム

で使われます。

---

# まとめ

今回は：

* フーリエ変換の意味
* QFT回路構造
* 制御位相回転
* SWAPの役割
* 位相情報の展開

を扱いました。

ここまでで：

量子アルゴリズムのもう一つの柱

である

位相操作

を理解しました。

次回は：

VQE（変分量子固有値法）

を実装しながら：

NISQ時代の代表アルゴリズム

を体験していきます。
