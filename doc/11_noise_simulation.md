以下は **フラットな Markdown** です。そのまま
`doc/11_noise_simulation.md` として保存できます。

---

# doc/11_noise_simulation.md

# 第11回 ノイズシミュレーションを試す

## はじめに

前回は：

* デコヒーレンス
* T1緩和
* T2緩和
* ゲート誤差
* 測定誤差

といった

量子ノイズの基本概念

を整理しました。

今回は：

**Qiskitでノイズ付き量子回路を実行する**

方法を実装します。

これにより：

理想回路
と
現実の量子回路

の違いを体験できます。

---

# なぜノイズシミュレーションが必要なのか？

これまで使用していた：

AerSimulator

は：

理想的な量子コンピュータ

として動作していました。

しかし：

実機では

必ずノイズが存在します。

そのため：

ノイズ付きシミュレーション

が重要になります。

---

# ノイズモデルとは何か？

ノイズモデルとは：

量子回路に誤差を追加する仕組み

です。

例えば：

* ゲート誤差
* 測定誤差
* 緩和時間

などを設定できます。

---

# NoiseModelを使う

まず：

NoiseModel

をインポートします。

コード：

```python id="0n9qah"
from qiskit_aer.noise import NoiseModel
```

---

# 最も簡単なノイズを追加する

今回は：

ビット反転ノイズ

を追加します。

これは：

|0⟩ → |1⟩
|1⟩ → |0⟩

が一定確率で起きる

ノイズです。

---

# ビット反転ノイズを定義する

コード：

```python id="fp0ye0"
from qiskit_aer.noise import NoiseModel, pauli_error

error = pauli_error([("X", 0.1), ("I", 0.9)])

noise_model = NoiseModel()

noise_model.add_all_qubit_quantum_error(error, ["h"])
```

意味：

Hadamardゲートのあとに

10%

の確率でX誤差を追加します。

---

# ノイズ付き回路を実行する

次に：

Hadamard回路を実行します。

コード：

```python id="n63ksl"
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
from qiskit.compiler import transpile

qc = QuantumCircuit(1, 1)

qc.h(0)

qc.measure(0, 0)

simulator = AerSimulator(noise_model=noise_model)

compiled = transpile(qc, simulator)

job = simulator.run(compiled, shots=1000)

result = job.result()

print(result.get_counts())
```

---

# 実行結果を比較する

理想状態：

{'0': 500, '1': 500}

ノイズあり：

例：

{'0': 460, '1': 540}

のようになります。

つまり：

確率が偏ります。

これが：

ノイズの影響です。

---

# 測定誤差を追加する

次に：

測定誤差

を追加します。

コード：

```python id="l6r4rw"
from qiskit_aer.noise import ReadoutError

readout_error = ReadoutError([[0.9, 0.1],
                             [0.1, 0.9]])

noise_model.add_all_qubit_readout_error(readout_error)
```

意味：

10%

の確率で

測定結果が反転します。

---

# 測定誤差の影響を見る

同じ回路を実行すると：

さらに結果が乱れます。

例：

{'0': 430, '1': 570}

つまり：

測定だけでも誤差が生まれます。

---

# 実機ノイズモデルを使う

Qiskitでは：

実機のノイズモデル

を取得できます。

例：

```python id="x2hbgt"
from qiskit_ibm_runtime.fake_provider import FakeManila
```

---

# FakeBackendを使う

FakeBackendは：

実機と同じノイズ特性

を持っています。

コード：

```python id="ibw0kw"
from qiskit_ibm_runtime.fake_provider import FakeManila
from qiskit_aer import AerSimulator

backend = FakeManila()

simulator = AerSimulator.from_backend(backend)
```

これで：

実機に近いシミュレーション

ができます。

---

# 実機ノイズ付きで実行する

コード：

```python id="ebrtps"
compiled = transpile(qc, simulator)

job = simulator.run(compiled, shots=1000)

result = job.result()

print(result.get_counts())
```

結果：

さらに偏った分布になります。

つまり：

実機に近づいた

ということです。

---

# なぜFakeBackendが重要なのか？

理由：

実機は

いつでも使えるとは限らない

からです。

そのため：

開発段階では

FakeBackend

が役立ちます。

---

# ノイズ耐性を改善する方法

ノイズの影響を減らす方法：

* 回路を浅くする
* ゲート数を減らす
* 測定回数を増やす
* ノイズモデルで検証する

これが：

NISQ時代の回路設計

です。

---

# VQEやQAOAとノイズ

VQE：

期待値平均を使う

QAOA：

確率分布を見る

つまり：

多少のノイズでも動作します。

これが：

NISQ向きアルゴリズム

と呼ばれる理由です。

---

# ノイズシミュレーションの役割

ノイズシミュレーションは：

次の用途で使います：

実機前の検証
回路最適化
パラメータ調整
アルゴリズム評価

つまり：

実機実行の前段階

です。

---

# まとめ

今回は：

* NoiseModel
* pauli_error
* ReadoutError
* FakeBackend
* 実機ノイズ再現

を扱いました。

ここまでで：

理想回路

から

現実の量子回路

への移行準備ができました。

次回は：

実機で量子回路を実行する方法

を扱います。
