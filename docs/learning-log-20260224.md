# Learning Log 01 - MVP準備フェーズ（設計・環境構築）

- **日付**: 2026/02/24
- **プロジェクト名**: edge-audit-platform
- **フェーズ**: MVP準備（設計 / スキーマ / Redis基盤）
- **所要時間**: 1時間00分

---

## 0. このログの目的
今回の作業で学んだこと・詰まったこと・次回やることを整理し、再開しやすくする。

---

## 1. 今日やったこと（作業ログ）

### 1-1. 設計・ドキュメント
- [x] README.md を作成 / 更新
- [x] docs/mvp-scope.md を作成 / 更新
- [x] schemas/raw_event.schema.json を作成 / 更新
- [x] schemas/finding.schema.json を作成 / 更新

### 1-2. リポジトリ・Git
- [x] Gitリポジトリ作成（または確認）
- [x] git config（user.name / user.email）設定
- [x] 初回コミット

### 1-3. Docker / Redis
- [x] deploy/docker-compose.yml を作成
- [x] Docker Desktop 起動確認
- [x] WSL更新（必要な場合）
- [x] `docker compose up -d` 実行
- [x] `docker compose ps` でRedis起動確認
- [x] `docker compose down` で停止確認

---

## 2. 学んだこと（事実 / 推測 / 提案）

### 2-1. 事実（確認できたこと）
- MVPを先に定義すると、作業範囲が明確になる
- `event_id` は Raw Event と Finding を紐づける共通キー
- JSON Schema は「JSONデータのルール（設計図）」であり、データ本体ではない
- `docker compose` は composeファイルがあるディレクトリで実行する必要がある（または `-f` 指定）
- Docker Desktop が起動しない原因は WSL の更新不足だった（`wsl --update` で解消）

### 2-2. 推測（自分の理解・気づき）
- 最初に設計（MVP / スキーマ）を固定したことで、実装時の迷いが減りそう
- Raw Event と Finding を分けると、将来のLLM導入や再解析がしやすくなりそう
- トラブルの切り分け順（CLI / 実行場所 / Desktop / WSL）を覚えると、今後も役立ちそう

### 2-3. 提案（次回に向けた改善）
- コマンドは `docs/runbook.md` に必ず残す
- エラーは「どのレイヤーの問題か（Git / Docker / WSL / アプリ）」を先に分類する
- 実装は Go単体（HTTP受信→Raw保存）から始めて、Redis連携は次に回す

---

## 3. 重要概念メモ（初学者向けに自分の言葉で）

### 3-1. MVPとは
TODO: 自分の言葉で「MVP」を説明する  
（例：最小限の機能だけで、システムの一本の流れが成立している状態）

### 3-2. JSON と JSON Schema の違い
- **JSON**: データファイルの型
- **JSON Schema**: JSONデータのスキーマを定義するための型

### 3-3. Raw Event と Finding の違い
- **Raw Event**（原本）: 受信したイベントを、最低限の管理情報（event_id, received_at, hashなど）だけ付けて保存した原本データ
- **Finding**（判定結果）: Raw Event を解析して得られた判定結果（normal/anomaly）と、その理由を記録したデータ

### 3-4. `event_id` の役割
`event_id` は、Raw Event（原本）と Finding（判定結果）を紐づけるための共通キー。

これにより、
- 「この判定結果が、どの元イベントに対するものか」を追える
- 同じイベントを後から再確認・再解析しやすくなる
- 将来、LLM判定結果やDB保存を追加してもデータをつなぎやすい

---

## 4. つまずいた点（Troubleshooting）

### 4-1. 発生した問題
- 問題1: `fatal: no email was given and auto-detection is disabled`
- 問題2: `docker: The term 'docker' is not recognized ...`
- 問題3: `no configuration file provided: not found`
- 問題4: `Docker Desktop is unable to start`
- 問題5: `WSL needs updating`

### 4-2. 原因（事実ベース）
- 問題1の原因: Gitの `user.name` / `user.email` 未設定
- 問題2の原因: Dockerコマンド未認識（Docker Desktop未起動/未設定）
- 問題3の原因: composeファイルがないディレクトリで実行
- 問題4-5の原因: WSLが古く Docker Desktop が起動不可

### 4-3. 解決方法
- 問題1の解決:
  ```powershell
  git config --global user.name "YOUR_NAME"
  git config --global user.email "YOUR_EMAIL"
  ```

* 問題3の解決:

  ```powershell
  cd "C:\Users\rk060\Desktop\git-repo\edge-audit-platform\deploy"
  docker compose up -d
  ```
* 問題4-5の解決:

  ```powershell
  wsl --update
  ```

### 4-4. 再発防止メモ

* `docker compose` 実行前に「今いるディレクトリ」を確認する
* Docker Desktop不調時は GUIメッセージ（WSL needs updating）を確認する
* 初回Gitセットアップをテンプレ化しておく

---

## 5. 今回作成・更新したファイル

* `README.md`
* `docs/mvp-scope.md`
* `schemas/raw_event.schema.json`
* `schemas/finding.schema.json`
* `deploy/docker-compose.yml`
* `docs/runbook.md`（作成/未作成）
* その他: TODO

---

## 6. 実行した主要コマンド（記録）

```powershell
# Git
git config --global user.name "..."
git config --global user.email "..."
git add .
git commit -m "..."

# Docker / Redis
cd "C:\Users\rk060\Desktop\git-repo\edge-audit-platform\deploy"
docker compose up -d
docker compose ps
docker compose down

# WSL
wsl --update
```

---

## 7. 成果（できるようになったこと）

* [x] MVPの範囲を定義できる
* [x] JSON Schema の役割を説明できる
* [x] Raw Event / Finding の責務を説明できる
* [x] Docker ComposeでRedisを起動・停止できる
* [x] Docker / WSL 起因のエラーを切り分けられる
