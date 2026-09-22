# installer/ — 安装器种入包（Seed Payload）

这一包是「安装一只小龙虾」时要种进**使用者 instance repo** 的档案，等价于官方安装器种入 `yh_claw` 的内容。
由 `altShiftClawAdmin` 前端 bundle 后，透过 GitHub Git Data API 一次 commit 进使用者 repo。

反推与设计说明见：[`_DOCS/altShiftClawAdmin/05_worker-package-and-workflows-reverse-engineered.md`](../../_DOCS/altShiftClawAdmin/05_worker-package-and-workflows-reverse-engineered.md)

## 内容

```
installer/
├── workflows/           → 种入 instance 的 .github/workflows/
│   ├── deploy-lobster-burger.yml          # 部署（Terraform + CF Worker + D1 + Telegram webhook）
│   ├── sync-github-app-webhook-secret.yml # 同步 GitHub App webhook secret
│   ├── templates.yml                      # 同步范本（admin「Sync Templates」/ Telegram /templates）
│   ├── skills.yml                         # 安装技能
│   ├── remove-skill.yml                   # 移除技能
│   ├── update-llm-secret.yml              # 更新 LLM provider 的 API key
│   ├── clear-memory.yml                   # 清除记忆
│   ├── autoupdate.yml                     # 更新核心（版本比对）
│   ├── check-env-permissions.yml          # 环境变数检测
│   ├── issue-1.yml                        # 执行小龙虾任务 #1
│   └── lib/{worker-name.sh, inject-workflow-env.sh}
└── templates/                             # ← 范本的唯一权威来源（AdminPage seed 与 deploy 都读这里）
    ├── .githubclaw-init                   # 初始化标记档
    ├── default/                           # runtime 预设范本（Pi Coding Agent）
    └── en/ ms/ zh-CN/                     # 各语言范本
```

## 范本来源：`installer/templates/` 与根目录 `templates/` 镜像

**`installer/templates/` 是唯一权威来源**。repo 根目录另有一个 `templates/`，那是**自动生成的兼容镜像**，
**请勿手动编辑**：

- 2026-08-05 之前安装的 instance，其 repo 内的 workflow 会在 runtime `git clone` 本 repo 并读取
  `<toolkit>/templates`。该目录一度被删除（`f641fce` 改成只留 `installer/templates`），于是这些 instance
  的模板同步步骤直接 `exit 1`；又因为该步骤排在 *Commit synced workflows* **之前**，连「把修正过的
  workflow 写回 instance」这条唯一的自愈管道也一起被切断 → 它们永远无法恢复，每天定时更新都失败。
- 所以镜像必须与 `installer/templates/` 保持**逐字节一致**。`.github/workflows/mirror-templates.yml`
  会在每次 push 自动重建并校验 `diff -rq`，有差异就自动纠正。
- **改范本只改 `installer/templates/`**，镜像会自动跟上；直接编辑根目录 `templates/` 的改动会被覆盖掉。
- 同理，`deploy-lobster-burger.yml` / `templates.yml` 现在会在 runtime 解析范本目录
  （`installer/templates` → 回退 `templates`），且目录缺失时只 `::warning::` 而不 `exit 1` ——
  这个步骤永远不该再阻断自愈管道。

## ⚙️ 设定（若换帐号 / repo 名 / core 网域要改这些）

这些 upstream 引用已从官方 `duotify/*` 改成我方预设值：

| 用途 | 目前值 | 出现档案 |
|---|---|---|
| Toolkit 来源 repo | `jeffsia-blacksmith/altShiftClawToolkit` | `skills.yml`, `templates.yml`, `deploy-lobster-burger.yml`, `issue-1.yml` |
| Core package 网域 | `jeffsia-blacksmith.github.io/altShiftClawCore` | `deploy-lobster-burger.yml`, `autoupdate.yml` |

换值：
```bash
cd installer/workflows
grep -rl 'jeffsia-blacksmith/altShiftClawToolkit' . | xargs sed -i '' 's#jeffsia-blacksmith/altShiftClawToolkit#<新 owner>/<新 repo>#g'
grep -rl 'jeffsia-blacksmith.github.io/altShiftClawCore' . | xargs sed -i '' 's#jeffsia-blacksmith.github.io/altShiftClawCore#<新 pages 网域>#g'
```

## 注意

- `deploy-lobster-burger.yml` 会抓 `worker-package.zip`（来自 altShiftClawCore GitHub Pages）并依赖 `Terraform/` output 名称 —— 部署前必须先建好 `altShiftClawCore` 并发布（见 05 待办）。
- `actions/error-handler-action@v1`、`update-comment-action@v1`（`issue-1.yml` 用）需 `altShiftClawToolkit` repo 已打 `v1` tag 才引用得到。
