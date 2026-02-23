# edge-audit-platform

## Overview
監査ログ風イベントを受信し、非同期に解析して結果を保存するPoC（Go + Redis Streams + Python）

## MVP Goal
HTTPで送信した監査ログ風JSONを、Goが受信してRaw保存し、Redis経由でPythonが判定し、判定結果を保存できること。

## In Scope (MVP)
- GoでHTTP受信
- Goで event_id / received_at / payload_hash を付与した Raw Event を生成
- Raw EventのJSONL保存
- Redis Streamsへの投入
- Pythonでルールベース判定
- FindingのJSONL保存

## Out of Scope (MVP)
- LLM (Gemini API)
- Blockchain (Polygon Amoy)
- 5G simulator (UERANSIM)
- OCI / GCP deployment
- Grafana Cloud monitoring
- GitHub Actions CI/CD
- Terraform / Ansible

## Repository Structure
```text
edge-audit-platform/
├─ apps/
│  ├─ go-ingestor/
│  └─ py-analyzer/
├─ deploy/
│  └─ docker-compose.yml
├─ schemas/
│  ├─ raw_event.schema.json
│  └─ finding.schema.json
├─ docs/
│  ├─ mvp-scope.md
│  ├─ architecture.md
│  └─ runbook.md
└─ .github/
   └─ workflows/
```