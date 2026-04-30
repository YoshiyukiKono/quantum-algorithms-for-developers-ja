以下は **フラットな Markdown** です。そのまま
`doc/09_qaoa.md` として保存できます。

---

# doc/09_qaoa.md

# 第09回 QAOA（量子近似最適化アルゴリズム）

## はじめに

前回は：

* VQE（変分量子固有値法）
* パラメータ付き回路
* 期待値最小化
* 古典最適化との連携

を扱いました。

そこで：

量子＋古典ハイブリッド計算

という

NISQ時代の基本構造

を理解しました。

今回は：

もう一つの代表的なNISQアルゴリズム：

**QAOA（Quantum Approximate Optimization Algorithm）**

を実装します。

これは：

組合せ最適化問題

を解くための量子アルゴリズムです。

---

# 組合せ最適化問題とは何か？

組合せ最適化問題とは：

多数の候補の中から

最適な解

を選ぶ問題です。

例：

* スケジューリング
* 配送ルート最適化
* ポートフォリオ最適化
* グラフ分割
* Max-Cut問題

などがあります。

---

# Max-Cut問題とは何か？

今回は：

Max-Cut問題

を扱います。

問題：

グラフのノードを2グループに分ける

とき：

エッジを最大数切断する

分け方を探します。

例：

```
0 --- 1
```

なら：

0と1を別グループにすれば

cut = 1

になります。

---

# なぜMax-Cutを使うのか？

理由：

QAOAの基本例として

最も分かりやすい

からです。

また：

多くの最適化問題は

Max-Cutに変換できます。

---

# QAOAの基本アイデア

QAOAは：

次の流れで動きます：

1. 初期状態を準備
2. コストハミルトニアンを適用
3. ミキサーハミルトニアンを適用
4. 繰り返す
5. 測定する

ここでも：

VQEと同じく

パラメータ最適化

を行います。

---

# 初期状態を作る

まず：

全状態の重ね合わせを作ります。

コード：

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(2)

qc.h(0)
qc.h(1)
```

状態：

0.5(|00⟩ + |01⟩ + |10⟩ + |11⟩)

になります。

---

# コストハミルトニアンとは何か？

Max-Cut問題では：

次のハミルトニアンを使います：

H = Z₀Z₁

これは：

2ノードが異なるとき

エネルギーが最小

になります。

---

# コスト演算子を適用する

コード：

```python
from numpy import pi

gamma = pi / 4

qc.cx(0, 1)
qc.rz(2 * gamma, 1)
qc.cx(0, 1)
```

これが：

コストハミルトニアン

です。

---

# ミキサーハミルトニアンとは何か？

次に：

探索空間を広げる操作を行います。

これが：

ミキサー

です。

コード：

```python
beta = pi / 4

qc.rx(2 * beta, 0)
qc.rx(2 * beta, 1)
```

---

# 測定を追加する

最後に：

測定を追加します。

```python
qc.measure_all()
```

---

# 全体コード

まとめると：

```python
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
from qiskit.compiler import transpile
from numpy import pi

qc = QuantumCircuit(2)

qc.h(0)
qc.h(1)

gamma = pi / 4

qc.cx(0, 1)
qc.rz(2 * gamma, 1)
qc.cx(0, 1)

beta = pi / 4

qc.rx(2 * beta, 0)
qc.rx(2 * beta, 1)

qc.measure_all()

simulator = AerSimulator()

compiled = transpile(qc, simulator)

job = simulator.run(compiled, shots=1000)

result = job.result()

print(result.get_counts())
```

---

# 実行結果

結果例：

```
{'01': 480, '10': 520}
```

つまり：

01
10

が多く出ます。

これは：

Max-Cutの解

になっています。

---

# なぜこれで最適化できるのか？

QAOAでは：

コスト演算子

で

良い解の位相を変化させます。

次に：

ミキサー

で

状態空間を探索します。

これを繰り返すことで：

最適解の確率が増えます。

---

# QAOAの一般形

QAOAは：

次の回路を繰り返します：

U(C, γ)
U(B, β)

ここで：

C = コストハミルトニアン
B = ミキサーハミルトニアン

です。

---

# VQEとの違い

VQE：

固有値問題を解く

QAOA：

組合せ最適化問題を解く

ただし：

どちらも

変分アルゴリズム

です。

---

# QAOAが重要な理由

QAOAは：

次の分野で期待されています：

物流最適化
金融最適化
スケジューリング
ネットワーク設計

つまり：

産業応用に最も近い量子アルゴリズム

です。

---

# パラメータ最適化

実際のQAOAでは：

γ
β

を最適化します。

つまり：

VQEと同じように

古典最適化ループ

を使います。

---

# QAOAの拡張

QAOAは：

p層

まで拡張できます。

p = 1

が今回の例です。

p を増やすほど：

精度が向上します。

---

# まとめ

今回は：

* Max-Cut問題
* コストハミルトニアン
* ミキサーハミルトニアン
* 変分最適化
* p層拡張

を扱いました。

ここまでで：

NISQ時代の代表アルゴリズム

である

VQE
QAOA

の両方を実装できました。

次回は：

量子ノイズとは何か？

を扱いながら：

実機に近い量子計算

を理解していきます。
