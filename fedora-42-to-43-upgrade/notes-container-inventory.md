---
name: Container Inventory
description: All Docker containers in /opt/containers — images, ports, RUNBOOK status, backup timers
type: project
originSessionId: ede1e2fc-66bc-4f7d-96c3-643bbfd91d37
---
All containers are Docker Compose-based, live in /opt/containers/, managed via systemd services.
Kasm Workspaces is at /opt/kasm (v1.18.1) — NOT in /opt/containers.

## Containers with RUNBOOK.md already

| Container | Image | Notes |
|-----------|-------|-------|
| convertx | ghcr.io/c4illin/convertx:latest | has backup service/timer |
| filestash | machines/filestash:latest | custom local image |
| garage | dxflrs/garage:v2.2.0 | S3-compatible object store; has backup |
| gitea | docker.gitea.com/gitea:1.25.4 | port 2223 SSH, has backup |
| glean | ghcr.io/leslieleung/glean-backend + web + admin | postgres:16, redis:8-alpine |
| home_file_server | machines/filestash:latest | same image as filestash |
| kroki | yuzutech/kroki + mermaid/bpmn/excalidraw | has backup |
| openbao | openbao/openbao:latest | vault-like; has backup |
| rsshub | diygod/rsshub:latest + redis:alpine | has backup |
| woodpecker-ci | woodpeckerci/woodpecker-server:v3 + agent:v3 | agent was restarting (exit 1) as of 2026-04-25 |

## Containers MISSING RUNBOOK.md (need to create)

| Container | Image | Port | Notes |
|-----------|-------|------|-------|
| actualbudget | actualbudget/actual-server:latest | 5007→5006 | personal finance; backup timer exists |
| excalidraw | excalidraw/excalidraw:latest | 5000:80 | simple, stateless |
| karakeep | ghcr.io/karakeep-app/karakeep:release | 3000 | bookmark manager; meilisearch + chrome; label:disable SELinux |
| n8n | n8nio/n8n:latest | 21712→5678 | workflow automation; label:disable SELinux |
| pastebooks | ghcr.io/kevinpinscoe/pastebooks:latest + mysql:8.4 | 8080 | user's own app; backup timer exists |
| wikijs | ghcr.io/requarks/wiki:2 + postgres:16-alpine | 3001→3000 | backup timer exists |
| youtrack | jetbrains/youtrack:2025.3.132953 | 9000→8080 | backup timer exists |

## Active backup timers (systemd)
backupmysql, glean-purge, karakeep-backup, kroki-backup, n8n-backup, garage-backup, gitea-backup, convertx-backup, openbao-backup, rsshub-backup, woodpecker-ci-backup, wikijs-backup, actualbudget-backup, pastebooks-backup, youtrack-backup, rss-feed-pinboard-recent

## Containers NOT in /opt/containers
- **Kasm Workspaces 1.18.1** at /opt/kasm — kasm_proxy, kasm_rdp_https_gateway, kasm_rdp_gateway, kasm_agent, kasm_manager, kasm_api, kasm_guac, kasm_db (kasmweb/postgres:1.18.1)
- **incident-dev-postgres** and **incident-valkey** — dev containers, referenced by image ID only
