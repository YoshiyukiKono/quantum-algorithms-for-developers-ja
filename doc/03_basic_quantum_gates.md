
# 第03回 基本量子ゲートをコードで理解する

## はじめに

前回は：

- 状態ベクトルとは何か
- 確率振幅の意味
- 位相の重要性
- Bloch球での可視化

を学びました。

ここまでで：

```

量子状態はベクトル

```

という理解ができています。

今回は：

**量子ゲートとは何か？**

をコードと状態ベクトルの変化から理解します。

量子ゲートとは：

```

ベクトルを回転させる操作

```

です。

---

# 今回扱う量子ゲート

今回は以下を扱います：

- Xゲート
- Zゲート
- Sゲート
- Tゲート

これらは：

量子アルゴリズムの基本部品

になります。

---

# 量子ゲートは行列である

量子ゲートは：

```

行列

```

です。

つまり：

```

状態ベクトル × 行列

```

という計算が内部で起きています。

例えば：

```

|0⟩ = [1, 0]

```

にゲートを適用すると：

別のベクトルに変換されます。

---

# Xゲート（ビット反転）

まず：

Xゲート

です。

これは：

```

|0⟩ → |1⟩
|1⟩ → |0⟩

````

に変換します。

コード：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(1)

qc.x(0)

state = Statevector.from_instruction(qc)

print(state)
````

結果：

````
[0.+0.j, 1.+0.j]
```

つまり：

````

|1⟩

```

になっています。

---

# Bloch球で理解するXゲート

Bloch球では：

```

北極 → 南極

```

への回転です。

つまり：

```

Z軸まわりではなく
X軸まわりの回転

```

になります。

これは：

古典ビットのNOTに対応します。

---

# Zゲート（位相反転）

次に：

Zゲート

です。

これは：

```

|0⟩ → |0⟩
|1⟩ → −|1⟩

````

になります。

コード：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(1)

qc.x(0)
qc.z(0)

state = Statevector.from_instruction(qc)

print(state)
````

結果：

````
[0.+0.j, -1.+0.j]
```

つまり：

````

符号が変わっただけ

```

です。

しかし：

これは非常に重要です。

---

# なぜ符号だけ変えるのが重要なのか？

量子計算では：

```

位相が干渉を生む

```

からです。

例えば：

```

0.707|0⟩ + 0.707|1⟩

```

にZを適用すると：

```

0.707|0⟩ − 0.707|1⟩

```

になります。

確率は同じですが：

干渉結果が変わります。

Groverアルゴリズムは：

この操作を使っています。

---

# Sゲート（90度回転）

Sゲートは：

位相を

```

90°

```

回転させます。

変換：

```

|1⟩ → i|1⟩

````

コード：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(1)

qc.x(0)
qc.s(0)

state = Statevector.from_instruction(qc)

print(state)
````

結果：

````
[0.+0.j, 0.+1.j]
```

つまり：

````

虚数方向への回転

```

です。

---

# Tゲート（45度回転）

Tゲートは：

さらに細かい回転：

```

45°

```

です。

変換：

```

|1⟩ → e^(iπ/4)|1⟩

````

コード：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(1)

qc.x(0)
qc.t(0)

state = Statevector.from_instruction(qc)

print(state)
````

出力例：

````
[0.+0.j, 0.707+0.707j]
```

つまり：

````

複素平面で45°回転

```

しています。

---

# 位相ゲートの意味

ここまでのゲート：

| ゲート | 回転角 |
|--------|--------|
| Z | 180° |
| S | 90° |
| T | 45° |

つまり：

```

位相を細かく制御する道具

````

です。

量子アルゴリズムでは：

この回転の組み合わせで

干渉を設計します。

---

# 実験：H → Z → H を試す

次の回路を書いてみます：

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(1)

qc.h(0)
qc.z(0)
qc.h(0)

state = Statevector.from_instruction(qc)

print(state)
````

結果：

````
[0.+0.j, 1.+0.j]
``` 

つまり：

````

|1⟩

```

になります。

これは：

```

HZH = X

```id="n5x7kb"

という関係を意味します。

量子ゲートは：

組み合わせで新しい操作を作れます。

---

# なぜ基本ゲートが重要なのか？

量子アルゴリズムは：

```

基本ゲートの組み合わせ

```

で構成されています。

例えば：

- Grover
- QFT
- VQE
- QAOA

すべて同じです。

つまり：

```

ゲートを理解する = アルゴリズムを理解する

```

ということです。

---

# まとめ

今回は：

- Xゲート
- Zゲート
- Sゲート
- Tゲート
- 位相回転の意味

を扱いました。

ここまでで：

```

量子ゲート = ベクトル回転

```

として理解できるようになりました。

次回は：

2量子ビット回路

を扱います。

ここで初めて：

```

エンタングルメント

```

が登場します 🚀
