# テスト運用ドキュメント

## 1. レイヤ構成
- **ユニットテスト**  
    - pytest + pytest-mocker  
    - 単一関数・クラスを検証  
- **サービステスト**  
    - pytest + SQLite in-memory  
    - ドメインサービス／リポジトリを検証  
- **E2E テスト**  
    - Playwright (Python)  
    - GUI と API を統合検証  

---

## 2. TDD サイクル
1. **Red** : 失敗する最小テストを追加  
2. **Green** : 最短の実装でテストを通す  
3. **Refactor** : 重複除去・命名整理  
       （Copilot へ *差分編集* で依頼）  
4. CI で `pytest -q` を実行し **Coverage ≥ 90 %** を維持  

---

## 3. 高速化テクニック
- `pytest -n auto` で並列実行  
- `pytest --lf` で前回失敗テストのみ再実行  
- 主要モックライブラリ  
    - Redis : `fakeredis`  
    - S3   : `moto[s3]`  
    - 外部 REST : `responses`  

---

## 4. GitHub Actions ワークフロー（抜粋）

    name: test

    on:
      push:
        branches:
          - main
          - feat/**
          - fix/**

    jobs:
      pytest:
        runs-on: windows-latest
        steps:
          - uses: actions/checkout@v4
          - uses: actions/setup-python@v5
            with:
              python-version: "3.12"
          - run: |
              python -m pip install -r requirements/dev.txt
              pre-commit run --all-files
              pytest -q --cov=src --cov-fail-under=90 -n auto

---

## 5. トラブルシューティング
- **テスト完了後に PowerShell プロンプトが返らない**  
    - Enter を 1 回押す  
    - VS Code の `terminal.integrated.shellIntegration.decorationsEnabled` 設定を確認  
- **Copilot が失敗 → 自動修正 → 再失敗のループ**  
    - 連続 2 回失敗したら自動修復を止め、**提案のみ** に切り替える
