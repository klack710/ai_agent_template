# 開発ガイドライン

## 0. 前提
- Windows 11 + PowerShell 7.x + VS Code 1.85 以降  
- GitHub Copilot **Agent mode (Claude Sonnet 4)**  
- Python 3.12  
- すべての説明・コミットは日本語

---

## 1. コーディングスタイル
- **Black**  
  - `line-length` 88、4 スペース、UTF-8
- **Ruff**  
  - `extend-select = ["I", "B", "UP", "SIM"]`（`ruff.toml` で管理）
- **isort**  
  - Ruff 内蔵機能を使用（追加設定不要）
- `pre-commit run --all-files` で **Black → Ruff → mypy** を一括実行
- 連続 2 回失敗したら Copilot の自動修復を止め、**提案のみ** に切り替える

---

## 2. ブランチ運用
- `main` : 常に GREEN（CI 通過のみマージ）  
- 機能追加 : `feat/*`  
- バグ修正 : `fix/*`  
- リファクタ : `refactor/*`  
- テスト強化 : `test/*`

---

## 3. コミット規約（Conventional Commits 日本語）
- 見出し形式 `<type>: <概要>`  
  - 例 `feat: プロフィール編集画面を追加`
- 本文に **WHY / HOW / 影響範囲** を 80 桁ごとに改行して記述

---

## 4. 大規模変更のガード
- **300 行超** または **5 ファイル超** の一括変更は  
  1. GitHub Issue でタスクを分割  
  2. Copilot へ `#selection` で範囲を限定して依頼
- **500 行超** の単一ファイルを編集する場合は  
  - まずファイル分割を提案し、必要部分だけ編集

---

## 5. VS Code 推奨設定

```
{
  "editor.formatOnSave": true,
  "python.formatting.provider": "black",
  "python.linting.ruffEnabled": true,
  "terminal.integrated.shellIntegration.decorationsEnabled": false,
  "files.insertFinalNewline": true
}
```
terminal.integrated.shellIntegration.decorationsEnabled を false にすると、
PowerShell 端末がコマンド完了後にプロンプトへ戻らなくなる既知のハングを防げます。

6. レビュー観点（抜粋）
可読性

保守性

テスト容易性

性能（必要ならベンチマークで定量比較）

セキュリティ（秘密情報の管理・依存ライブラリの CVE）

7. セキュリティ運用
秘匿情報は .env に集約し、.gitignore で除外

detect-secrets を pre-commit フックに追加してコミット前に自動スキャン