# 第01回 Python量子開発環境を構築する

## はじめに

本シリーズ：

**開発者のための量子アルゴリズム実践入門  
― PythonとQiskitで学ぶNISQアルゴリズム ―**

では、

量子アルゴリズムを

**実際にコードで動かす**

ことを目的に進めていきます。

第1回ではまず：

量子プログラミング環境を構築し  
最初の量子回路を実行する

ところまで進みます。

ゴールはこちらです：

```

Hadamardゲートを適用して
50% / 50% の測定結果を得る

```

つまり：

量子重ね合わせを自分のPCで再現します。

---

# この回でできるようになること

本記事を終えると：

- Python仮想環境を作れる
- Qiskitをインストールできる
- 最初の量子回路を書ける
- シミュレータで実行できる

ようになります。

---

# なぜQiskitを使うのか？

量子SDKはいくつか存在します：

| SDK | 開発元 |
|-----|--------|
| Qiskit | IBM |
| Cirq | Google |
| PennyLane | Xanadu |

本シリーズでは：

**Qiskit**

を使用します。

理由：

- 情報量が多い
- 実機実行までつながる
- NISQアルゴリズムとの相性がよい
- Python開発者に自然

---

# 仮想環境を作る

まずPython仮想環境を作成します。

プロジェクトディレクトリを作成：

```

mkdir quantum-algorithms
cd quantum-algorithms

```

仮想環境を作成：

```

python3 -m venv venv

```

有効化：

Mac / Linux：

```

source venv/bin/activate

```

Windows：

```

venv\Scripts\activate

```

成功すると：

```

(venv)

```

と表示されます。

---

# Qiskitをインストールする

次にQiskitをインストールします。

```

pip install qiskit

```

あわせて可視化用ライブラリも追加：

```

pip install matplotlib numpy scipy

```

確認：

```

python

```

Pythonコンソールで：

```

import qiskit

```

エラーが出なければ成功です。

終了：

```

exit()

```

---

# 最初の量子回路を書く

ファイルを作成します：

```

first_circuit.py

````

内容はこちら：

```python
from qiskit import QuantumCircuit
from qiskit_aer import AerSimulator
from qiskit.compiler import transpile
from qiskit.visualization import plot_histogram
import matplotlib.pyplot as plt

qc = QuantumCircuit(1, 1)

qc.h(0)

qc.measure(0, 0)

simulator = AerSimulator()

compiled_circuit = transpile(qc, simulator)

job = simulator.run(compiled_circuit, shots=1000)

result = job.result()

counts = result.get_counts()

print(counts)

plot_histogram(counts)
plt.show()
````

---

# コードの意味を理解する

この回路は：

```
|0⟩
↓
H
↓
measure
```

という構造になっています。

順番に見ていきます。

---

## 量子回路を作る

```
qc = QuantumCircuit(1, 1)
```

意味：

```
量子ビット：1
古典ビット：1
```

測定結果を保存するため：

古典ビットが必要です。

---

## Hadamardゲートを適用する

```
qc.h(0)
```

これは：

```
|0⟩ → 重ね合わせ状態
```

に変換します。

つまり：

```
|0⟩ + |1⟩
```

の状態になります。

---

## 測定する

```
qc.measure(0, 0)
```

意味：

```
量子ビット0 → 古典ビット0へ記録
```

---

## シミュレータを使う

```
simulator = AerSimulator()
```

これは：

量子コンピュータの代わりに

PC上で回路を実行するためのものです。

---

## 実行する

```
job = simulator.run(compiled_circuit, shots=1000)
```

shotsとは：

```
回路を何回実行するか
```

です。

今回は：

1000回測定します。

---

# 実行してみる

実行：

```
python first_circuit.py
```

結果例：

```
{'0': 497, '1': 503}
```

つまり：

```
約50%
約50%
```

になっています。

これは：

量子重ね合わせが正しく作られた

ことを意味します。

---

# なぜ50%になるのか？

Hadamardゲートは：

```
|0⟩ → 50%
|1⟩ → 50%
```

の状態を作ります。

測定すると：

ランダムにどちらかが出ます。

しかし：

回数を増やすと

```
50 : 50
```

に近づきます。

これが：

量子確率振幅の性質です。

---

# 回路を可視化する

回路を表示するには：

コードに追加：

```python
print(qc.draw())
```

出力：

```
     ┌───┐┌─┐
q_0: ┤ H ├┤M├
     └───┘└╥┘
c: 1/══════╩═
           0
```

量子回路が視覚的に確認できます。

---

# Notebook環境（おすすめ）

より理解しやすいのは：

Jupyter Notebook

です。

インストール：

```
pip install notebook
```

起動：

```
jupyter notebook
```

Notebook形式だと：

* 回路図
* ヒストグラム
* 状態ベクトル

をインタラクティブに確認できます。

---

# まとめ

今回は：

* Python仮想環境構築
* Qiskitインストール
* 最初の量子回路作成
* Hadamardゲート実行
* 測定結果の確認

まで進みました。

ここまでで：

**量子回路を自分で動かせる状態**

になっています。

次回は：

状態ベクトルを可視化しながら

量子状態そのもの

を観察していきます。

```
```
