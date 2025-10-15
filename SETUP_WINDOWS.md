## Windows (PowerShell) 用 初期セットアップ

このリポジトリは古い依存関係（例: PyTorch 0.4.1, Python 3.7, CUDA 9.0）を想定しています。そのため、使える環境に応じて2つの方法を用意しました。

注意: 以下は Windows PowerShell (既定) を想定したコマンド例です。Anaconda/Miniconda を使う方法を推奨します。

---

### オプション A — 元の環境をできるだけ再現（推奨、GPU を使う場合）

目的: Python 3.7 / PyTorch 0.4.x (CUDA 9.0) に合わせて環境を作る。

前提: Miniconda/Anaconda をインストール済みであること。

1) conda 環境作成と有効化

```powershell
conda create -n medicalnet python=3.7 -y
conda activate medicalnet
```

2) CUDA と PyTorch のインストール

PyTorch 0.4.1 は古いバージョンで、Windows 用に直接入手できる環境が限られます。以下は一般的な方針です:

- GPU (CUDA 9.0) を持っていて再現したい場合は、まず NVIDIA ドライバと CUDA 9.0 をシステムにインストールしてください（NVIDIA の配布を参照）。
- conda で適切な cudatoolkit を入れてから、対応する PyTorch をインストールします（古いビルドは channel や wheel が必要になる場合があります）。

例: CPU のみで試す場合（簡易）

```powershell
pip install "torch==0.4.1" --no-deps || echo "Please install appropriate PyTorch wheel manually for Windows 0.4.1"
pip install -r .\requirements.txt
```

注意: 上の torch==0.4.1 は pip で見つからない場合があります。その場合は PyTorch の過去リリースの wheel を探すか、代替として最新の PyTorch に切り替えることを検討してください。

3) 動作確認（小テスト）

```powershell
python .\test_ci.py
```

---

### オプション B — すぐ動かす（CPUのみ、現行環境での簡易実行）

目的: ローカルの最新 Python / Windows で最小限の変更で動かしてみる。GPU や元と完全互換ではないが、CI 用の軽い確認やコードの理解には最適。

1) venv を作って有効化

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

2) 依存をインストール（Torch を最新の CPU バージョンへ置き換え）

注意: requirements.txt に古いバージョン指定があります。ここでは CPU 用の新しい PyTorch を使うコマンド例を示します。自身の Python バージョンに合わせて調整してください。

```powershell
pip install -U pip
pip install numpy nibabel scipy
# PyTorch (CPU only) の例。Windows + Python のバージョンに応じて公式サイトのコマンドを使ってください。
pip install torch torchvision --index-url https://download.pytorch.org/whl/cpu
```

3) リポジトリの依存をインストール

```powershell
# requirements.txt のうち互換しない行がある場合は手動で調整してください。
pip install -r .\requirements.txt || echo "If pip install -r failed, install required packages manually (numpy, nibabel, scipy, torch)"
```

4) 最小動作確認

```powershell
python .\test_ci.py
```

---

### トラブルシュートのヒント

- PyTorch の古いバージョンは Windows で入手困難なことがあります。GPU を使わない短い確認なら最新の CPU ビルドで進めるのが手っ取り早いです。
- SciPy の古い API (例: ndimage.interpolation.zoom) は警告や非推奨になることがありますが、多くは互換的に動きます。問題が起きたら該当行を `ndimage.zoom` に修正できます。
- エラーが発生したらエラーメッセージを教えてください。こちらで修正パッチ（互換性を保つ最小変更）を作成します。

---

### 次のステップ（推奨）

1. どちらの方法を試したいか教えてください（A: 古い環境の再現、B: 短時間で動かす）。
2. 選択を受けて、必要なら私が小さな互換修正（コード内の非推奨 API、torch.load の map_location 追加など）をリポジトリに適用します。

ファイルを追加しました: `SETUP_WINDOWS.md` — リポジトリルートに置いてあります。
