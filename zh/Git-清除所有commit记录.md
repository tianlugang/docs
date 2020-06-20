# 清除 git commit 记录
删除.git文件夹可能会导致git存储库中的问题。如果要删除所有提交历史记录，但将代码保持在当前状态，可以按照以下方式安全地执行此操作：

1. 尝试运行  `git checkout --orphan latest_branch`
2. 添加所有文件 `git add -A`
3. 提交更改 `git commit -am "commit message"`
4. 删除分支`git branch -D master`
5. 将当前分支重命名`git branch -m master`
6. 最后，强制更新存储库。`git push -f origin master`

## 删除指定的 commit 记录
1. 运行 `git log`， 选择指定的 `commit_id`
2. 运行 `git reset --hard $commit_id`
3. 提交 `git push origin HEAD --force`