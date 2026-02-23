# Runbook (MVP / Local)

## Prerequisites
- Windows + PowerShell
- Docker Desktop
- WSL (updated)

## Troubleshooting Memo
- Docker Desktop failed to start because WSL was outdated
- Fixed by running: `wsl --update`

## Redis (docker compose)

### Start
```powershell
cd "C:\Users\rk060\Desktop\git-repo\edge-audit-platform\deploy"
docker compose up -d
```

### Check status

```powershell
docker compose ps
```

### Stop

```powershell
docker compose down
```

## Expected Result

* Redis container `edge-audit-redis` is `Up`
* Port `6379` is exposed