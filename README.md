# テスト用リポジトリ (Self Hosted Runner)

このリポジトリはCIワークフローのテストを記録するものです。
検討はGitHub Copilotを使っています。

### test_repositoryと違い

シミュレーションなど、負荷の高い検証を行う場合を想定し、Self Hosted Ruunerを検証します。

### WindowsでSelf Hosted Ruunerを動かす手順

#### ランナーのセットアップ
1. GitHubリポジトリの「Settings」→「Actions」→「Runners」→「New self-hosted runner」
2. OSで「Windows」を選択
3. Archtctureで「x64」を選択
4. 表示されるコマンドをPowerShellで実行

#### コマンド例:
```
# ディレクトリ作成
mkdir actions-runner
cd actions-runner
# ダウンロード
Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.316.0/actions-runner-win-x64-2.316.0.zip -OutFile actions-runner-win-x64-2.316.0.zip
# 展開
Add-Type -AssemblyName System.IO.Compression.FileSystem ; [System.IO.Compression.ZipFile]::ExtractToDirectory("actions-runner-win-x64-2.316.0.zip", ".")
```

#### 設定（URLやTOKENはGitHubで発行されたものを使う）
```
PS C:\Users\xxx\actions-runner>  ./config.cmd --url https://github.com/masaki-kato-119/test_repository_self_hosted --token xxxxxxxx

--------------------------------------------------------------------------------
|        ____ _ _   _   _       _          _        _   _                      |
|       / ___(_) |_| | | |_   _| |__      / \   ___| |_(_) ___  _ __  ___      |
|      | |  _| | __| |_| | | | | '_ \    / _ \ / __| __| |/ _ \| '_ \/ __|     |
|      | |_| | | |_|  _  | |_| | |_) |  / ___ \ (__| |_| | (_) | | | \__ \     |
|       \____|_|\__|_| |_|\__,_|_.__/  /_/   \_\___|\__|_|\___/|_| |_|___/     |
|                                                                              |
|                       Self-hosted runner registration                        |
|                                                                              |
--------------------------------------------------------------------------------

# Authentication


√ Connected to GitHub

# Runner Registration

Enter the name of the runner group to add this runner to: [press Enter for Default]

Enter the name of runner: [press Enter for XXXXXX]

This runner will have the following labels: 'self-hosted', 'Windows', 'X64'
Enter any additional labels (ex. label-1,label-2): [press Enter to skip]

√ Runner successfully added
√ Runner connection is good

# Runner settings

Enter name of work folder: [press Enter for _work]

√ Settings Saved.

Would you like to run the runner as service? (Y/N) [press Enter for N]
```

#### Runnerを起動
```
PS C:\Users\xxx\actions-runner> ./run.cmd
        1 個のファイルをコピーしました。

√ Connected to GitHub

Current runner version: '2.328.0'
2025-09-01 04:55:25Z: Listening for Jobs
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
