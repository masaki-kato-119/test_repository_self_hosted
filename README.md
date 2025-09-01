# テスト用リポジトリ（Self Hosted Runner)

このリポジトリはCIワークフローのテストを記録するものです。
検討はGitHub Copilotを使っています。

# test_repositoryと違い

セルフホステッドランナーを使います。


1. Windowsでセルフホステッドランナーを動かす手順
① ランナーのセットアップ
1. GitHubリポジトリの「Settings」→「Actions」→「Runners」→「New self-hosted runner」
2. OSで「Windows」を選択
3. 表示されるコマンドをPowerShellで実行
例:
```
# ディレクトリ作成
mkdir actions-runner ; cd actions-runner
# ダウンロード
Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.316.0/actions-runner-win-x64-2.316.0.zip -OutFile actions-runner-win-x64-2.316.0.zip
# 展開
Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("actions-runner-win-x64-2.316.0.zip", ".")
# 設定（URLやTOKENはGitHubで発行されたものを使う）
.\config.cmd --url https://github.com/ユーザー名/リポジトリ名 --token xxxxxxxx
# 起動
.\run.cmd
```

---
#### GitHub Actionsワークフロー例（.github/workflows/ci.yml）

```yaml
name: Python CI

on:
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v4  # リポジトリのソースコードを取得

    - name: Set up Python  # Python 3.11をセットアップ
      uses: actions/setup-python@v5
      with:
        python-version: '3.11'

    - name: Install dependencies  # 依存パッケージをインストール
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Lint with pylint  # コードの静的解析
      run: PYTHONPATH=. pylint src tests

    - name: Type check with mypy  # 型チェック
      run: PYTHONPATH=. mypy src

    - name: Security check with bandit  # セキュリティチェック
      run: bandit -r src

    - name: Run tests  # テスト実行
      run: PYTHONPATH=. pytest tests
```


---

このREADMEはGitHub Copilotの支援で作成・更新しています。
