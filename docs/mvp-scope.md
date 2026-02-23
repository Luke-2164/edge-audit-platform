# MVP Scope

## 1. Purpose
このMVPの目的は、監査ログ風イベントを受信して非同期に解析する最小E2E（End-to-End）をローカル環境で成立させること。

学習目的:
- Go / Redis Streams / Python の連携を理解する
- 同期処理（受信）と非同期処理（解析）の分離を体験する
- Raw Event（原本）と Finding（判定結果）を分ける設計を学ぶ
- 将来のLLM / Blockchain / クラウド移植に備えた最小構成を作る

---

## 2. MVP Success Criteria
HTTPで送信した監査ログ風JSONを、Goが受信してRaw保存し、Redis経由でPythonが判定し、判定結果を保存できること。

---

## 3. In Scope (MVP)
このMVPで実装する範囲は以下とする。

- GoでHTTP API（POST /events）を用意し、イベントを受信する
- Goで `event_id` / `received_at` / `payload_hash` を付与した Raw Event を生成する
- Goで Raw Event を JSONL 形式でファイル保存する
- Goで Redis Streams にイベントを投入する
- Pythonで Redis Streams からイベントを読み取る
- Pythonでルールベース判定（`normal` / `anomaly`）を行う
- Pythonで Finding を JSONL 形式でファイル保存する
- Go / Python のアプリログ（標準出力）でエラー原因を追える状態にする

---

## 4. Out of Scope (MVP)
このMVPでは実装しない範囲（後で追加する候補）は以下とする。

- LLM連携（Gemini API）
- Blockchain記録（Polygon Amoy）
- 5G simulator（UERANSIM）
- OCI / GCP へのデプロイ
- Terraform / Ansible
- GitHub Actions（CI/CD）
- Grafana Cloud 監視
- Slack通知
- 高度なDB（PostgreSQL / ClickHouse など）
- Kubernetes

---

## 5. Assumptions / Preconditions
MVPを進める前提条件は以下とする。

- ローカルPCで Docker が利用できる
- Redis を docker-compose で起動できる
- Go と Python の実行環境を用意できる（または今後用意する）
- 入力データはまずHTTP JSONのみとする
- データ保存先はローカルファイル（JSONL）とする
- 監視ダッシュボードは作らず、まずはアプリログと保存ファイルで確認する

---

## 6. Data Design (MVP)
MVPで扱うデータは以下の2種類とする。

### 6.1 Raw Event（原本）
目的:
- 受信したイベントを、最低限の管理情報付きで保存する
- 後から再解析できるようにする
- Findingと `event_id` で紐づける

主な項目:
- `event_id`
- `received_at`
- `source`
- `schema_version`
- `raw_payload`
- `payload_hash`

### 6.2 Finding（判定結果）
目的:
- Pythonの判定結果を保存する
- 判定理由を残す
- Raw Eventと `event_id` で紐づける

主な項目:
- `event_id`
- `status` (`normal` / `anomaly`)
- `reason`
- `rule_version`
- `processed_at`

### 6.3 Common Key
- `event_id` は Raw Event / Finding を横断して紐づける共通キーとする

---

## 7. Definition of Done (Checklist)
以下を満たしたらMVP完了とする。

- [ ] `curl` でJSONを1件送信できる
- [ ] GoがHTTPでイベントを受信できる
- [ ] Goが `event_id` を付与できる
- [ ] Goが Raw Event を JSONL 保存できる
- [ ] Goが Redis Streams に投入できる
- [ ] Pythonが Redis Streams から読み取れる
- [ ] Pythonが `normal` / `anomaly` を判定できる
- [ ] Pythonが Finding を JSONL 保存できる
- [ ] エラー時にログで原因を追える
- [ ] 手順を `docs/runbook.md` に記載できる

---

## 8. Manual Test Scenario (Initial)
MVPの手動確認は以下の流れで行う。

1. Redis を docker-compose で起動する
2. Go ingestor を起動する
3. Python analyzer を起動する
4. `curl` でテストイベントを `/events` に送信する
5. Raw Event の JSONL ファイルに追記されていることを確認する
6. Finding の JSONL ファイルに追記されていることを確認する
7. Go / Python のログにエラーが出ていないことを確認する

---

## 9. Non-Goals / Quality Constraints (MVP)
MVP時点では以下は重視しない（将来対応）。

- 高スループット最適化
- 高可用性（冗長構成）
- 厳密なセキュリティ設計
- 本番運用レベルの監視・アラート
- 厳密なスキーマ進化管理
- 高度な異常検知精度

ただし、将来拡張しやすいように以下は意識する。
- `event_id` による追跡可能性
- `schema_version` / `rule_version` の保持
- Raw Event と Finding の責務分離

---

## 10. Future Extensions (Post-MVP)
MVP完了後の拡張候補。

- Gemini API によるLLM判定
- Slack通知
- Blockchain Committer（Polygon Amoy）
- OCI + GCP 構成への移植
- Terraform / Ansible による自動構築
- Grafana Cloud 監視
- GitHub Actions CI/CD