# installer/ — 安裝器種入包（Seed Payload）

這一包是「安裝一隻小龍蝦」時要種進**使用者 instance repo** 的檔案，等價於官方安裝器種入 `yh_claw` 的內容。
由 `altShiftClawAdmin` 前端 bundle 後，透過 GitHub Git Data API 一次 commit 進使用者 repo。

反推與設計說明見：[`_DOCS/altShiftClawAdmin/05_worker-package-and-workflows-reverse-engineered.md`](../../_DOCS/altShiftClawAdmin/05_worker-package-and-workflows-reverse-engineered.md)

## 內容

```
installer/
├── workflows/           → 種入 instance 的 .github/workflows/
│   ├── deploy-lobster-burger.yml          # 部署（Terraform + CF Worker + D1 + Telegram webhook）
│   ├── sync-github-app-webhook-secret.yml # 同步 GitHub App webhook secret
│   ├── templates.yml                      # 同步範本
│   ├── skills.yml                         # 安裝技能
│   ├── remove-skill.yml                   # 移除技能
│   ├── clear-memory.yml                   # 清除記憶
│   ├── autoupdate.yml                     # 更新核心（版本比對）
│   ├── check-env-permissions.yml          # 環境變數檢測
│   ├── issue-1.yml                        # 執行小龍蝦任務 #1
│   └── lib/{worker-name.sh, inject-workflow-env.sh}
└── templates/
    ├── .githubclaw-init                   # 初始化標記檔
    └── default/                           # runtime 預設範本（Pi Coding Agent）
```

## ⚙️ 設定（若換帳號 / repo 名 / core 網域要改這些）

這些 upstream 引用已從官方 `duotify/*` 改成我方預設值：

| 用途 | 目前值 | 出現檔案 |
|---|---|---|
| Toolkit 來源 repo | `jeffsia-blacksmith/altShiftClawToolkit` | `skills.yml`, `templates.yml`, `deploy-lobster-burger.yml`, `issue-1.yml` |
| Core package 網域 | `jeffsia-blacksmith.github.io/altShiftClawCore` | `deploy-lobster-burger.yml`, `autoupdate.yml` |

換值：
```bash
cd installer/workflows
grep -rl 'jeffsia-blacksmith/altShiftClawToolkit' . | xargs sed -i '' 's#jeffsia-blacksmith/altShiftClawToolkit#<新 owner>/<新 repo>#g'
grep -rl 'jeffsia-blacksmith.github.io/altShiftClawCore' . | xargs sed -i '' 's#jeffsia-blacksmith.github.io/altShiftClawCore#<新 pages 網域>#g'
```

## 注意

- `deploy-lobster-burger.yml` 會抓 `worker-package.zip`（來自 altShiftClawCore GitHub Pages）並依賴 `Terraform/` output 名稱 —— 部署前必須先建好 `altShiftClawCore` 並發佈（見 05 待辦）。
- `actions/error-handler-action@v1`、`update-comment-action@v1`（`issue-1.yml` 用）需 `altShiftClawToolkit` repo 已打 `v1` tag 才引用得到。
