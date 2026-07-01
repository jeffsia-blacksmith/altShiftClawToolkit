# Actions

此目錄收錄 altShiftClawToolkit Issue 處理流水線所使用的 Composite GitHub Actions。每個 Action 皆可獨立被其他 Workflow 引用。

## Actions 一覽

| Action | 用途 |
|---|---|
| [`commit-push-issue-branch-action`](commit-push-issue-branch-action/) | 將 Issue Workflow 產生的變更提交並 force-push 至對應的 `issue-<N>` branch |
| [`error-handler-action`](error-handler-action/) | 解析 Copilot CLI JSONL 日誌、辨識錯誤類型，並將摘要寫入結果檔 |
| [`update-comment-action`](update-comment-action/) | 將結果檔內容更新（PATCH）至指定的 GitHub Issue 留言 |
