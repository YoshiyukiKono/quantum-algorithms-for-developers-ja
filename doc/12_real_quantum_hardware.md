以下は **フラットな Markdown** です。そのまま
`doc/12_real_quantum_hardware.md` として保存できます。

---

# doc/12_real_quantum_hardware.md

# 第12回 実機で量子回路を実行する

## はじめに

前回は：

* NoiseModel
* pauli_error
* ReadoutError
* FakeBackend
* 実機ノイズの再現

を扱いました。

ここまでで：

理想シミュレータ
ノイズ付きシミュレータ

の両方を扱えるようになりました。

今回はいよいよ：

**実際の量子コンピュータで回路を実行する**

方法を解説します。

量子アルゴリズム学習において：

ここが大きな節目です 🚀

---

# なぜ実機で実行するのか？

シミュレータでは：

理想状態

を再現できます。

しかし実機では：

* ノイズがある
* 接続制約がある
* 実行待ち時間がある
* 測定誤差がある

つまり：

現実の量子計算

を体験できます。

---

# 実機利用の全体像

量子コンピュータは：

クラウド経由

で利用します。

代表例：

* IBM Quantum
* IonQ
* Rigetti
* Quantinuum

本シリーズでは：

**IBM Quantum**

を使用します。

理由：

* Qiskitと統合されている
* 無料枠がある
* 情報が豊富
* 教育用途に適している

---

# IBM Quantumアカウントを作成する

まず：

以下のサイトにアクセスします：

[https://quantum.ibm.com](https://quantum.ibm.com)

アカウントを作成すると：

APIトークン

が取得できます。

このトークンを使って：

Pythonから接続します。

---

# APIトークンを設定する

Qiskitから接続するには：

APIキーを保存します。

コード：

```python
from qiskit_ibm_runtime import QiskitRuntimeService

QiskitRuntimeService.save_account(
    channel="ibm_quantum",
    token="YOUR_API_TOKEN"
)
```

これで：

ローカル環境から接続できます。

---

# サービスに接続する

保存したアカウントを使って：

接続します。

```python
from qiskit_ibm_runtime import QiskitRuntimeService

service = QiskitRuntimeService()
```

これで：

利用可能なバックエンド一覧

にアクセスできます。

---

# 利用可能な量子コンピュータを確認する

次のコードを実行します：

```python
service.backends()
```

結果例：

```
ibm_osaka
ibm_kyoto
ibm_brisbane
...
```

これが：

現在利用可能な実機です。

---

# バックエンドを選択する

例：

```python
backend = service.backend("ibm_osaka")
```

これで：

実機が選択されます。

---

# 実機で回路を実行する

まず：

簡単な回路を書きます。

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(1, 1)

qc.h(0)

qc.measure(0, 0)
```

次に：

実機で実行します。

```python
from qiskit_ibm_runtime import Sampler

sampler = Sampler(backend=backend)

job = sampler.run([qc])

result = job.result()

print(result)
```

これで：

実機実行が開始されます。

---

# 実行には待ち時間がある

重要な違い：

シミュレータ：

即時実行

実機：

キュー待ちあり

数秒〜数分

かかる場合があります。

これは：

複数ユーザーで共有しているためです。

---

# 実行結果を確認する

結果例：

```
{'0': 0.48, '1': 0.52}
```

理想状態：

50% / 50%

でした。

実機では：

少しズレます。

これが：

ノイズの影響です。

---

# なぜ結果がズレるのか？

理由：

* ゲート誤差
* 測定誤差
* デコヒーレンス
* 接続制約

などです。

つまり：

実機では完全な計算はできません。

しかし：

これが現実の量子計算です。

---

# transpileとは何か？

実機では：

回路をそのまま実行できません。

理由：

量子ビット配置が固定されている

からです。

そのため：

transpile

を使います。

例：

```python
from qiskit import transpile

compiled = transpile(qc, backend)
```

これで：

実機向け回路

に変換されます。

---

# なぜ変換が必要なのか？

量子コンピュータでは：

すべての量子ビットが

直接接続されているわけではありません。

例：

```
q0 ─ q1
     │
     q2
```

このような構造になっています。

そのため：

回路を再配置する必要があります。

---

# 実機向け最適化

transpileでは：

次の最適化が行われます：

* ゲート削減
* 深さ削減
* 接続制約対応

つまり：

実行可能な回路

に変換されます。

---

# 実機実行の流れまとめ

実機実行は：

次の手順になります：

1 アカウント作成
2 APIキー保存
3 backend選択
4 transpile
5 実行
6 結果取得

これが：

基本ワークフローです。

---

# 実機でGroverを試すとどうなるか？

シミュレータ：

100% 正解

実機：

例：

```
{'11': 0.72, '01': 0.09, '10': 0.11, '00': 0.08}
```

つまり：

正解が最も多いが

完全ではない

結果になります。

これが：

NISQ時代の現実です。

---

# 実機を使うメリット

実機を使うことで：

次が理解できます：

ノイズの影響
回路深さの重要性
接続制約の存在
最適化の必要性

つまり：

理論から実践へ

移行できます。

---

# 実機実行のコツ

安定した結果を得るには：

* 浅い回路を使う
* 量子ビット数を減らす
* shotsを増やす
* backendを選ぶ

ことが重要です。

---

# まとめ

今回は：

* IBM Quantum接続
* backend選択
* Sampler実行
* transpileの役割
* 実機ノイズの観察

を扱いました。

ここまでで：

量子アルゴリズムを

**実機で動かせる状態**

になりました。

次回は：

ハイブリッド量子古典アルゴリズム

を整理しながら：

NISQ時代の量子計算の中心モデル

を理解していきます。
