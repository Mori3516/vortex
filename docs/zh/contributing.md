# 贡献给 Vortex (Contributing to Vortex)

## Github
Vortex 使用 Github 托管其 git 仓库。
有许多方法可以利用 Github 的功能进行协作。
因此，本文档详细介绍了向 Vortex 贡献的标准程序。
Vortex 的开发整合在此仓库 `vortex` 和任何关联的分支 (associated forks) 中。
以前，在一个名为 `vortex-dev` 的私有仓库中进行了积极的工作。
`vortex-dev` 现已正式弃用并完全合并到此公共仓库 `vortex` 中。
如果你重返此项目并拥有旧版本的 Vortex，你可以使用发布分支 (releases branches) 来访问旧版本。

## 贡献流程 (Contribution Process)
为了保持 `vortex` 的条理性，直接创建分支和推送代码的权限已限制为管理员。
但是，我们强烈鼓励贡献并保持项目向前发展！以下是贡献的程序：

1. 创建一个 `vortex` 的 fork (Create a fork of `vortex`)
2. 在你的 fork 中，从 `master` 创建一个分支，简要解释你要添加的工作（即：`develop-documentation`）
3. 在你的 fork 中的新分支上进行更改。你可以根据需要创建任意数量的提交，如果你要进行多次迭代，这可能很常见
4. 由于你是 fork 的所有者，你拥有向 fork 推送提交的完全权限
4. 当你要对 fork 上的更改感到满意时，你可以使用在线界面从 fork 打开一个 PR (Pull Request)
5. 如果你最近进行了推送，你将在 Github 上自动获得创建 PR 的提示
6. 否则，你可以转到 Github 上的 fork 并手动创建 PR（待办事项）
（待办事项）：如何命名和格式化你的 PR，应在 PR 中添加什么信息，如果你参加每周会议则不必太严格*
7. Github 使用以下语义：`基础仓库 (base repository)` 从你的 `头仓库 (head repository)` 获取更改
8. 因此，你应该将 `base repository` 设置为 `vortexgpgpu/vortex` 并将 `base` 分支设置为 `master`，因为 master 分支受已审查 PR 的保护。
9. 且你应该将 `head repository` 分配给 `<your-github-username>/vortex`（代表你的 vortex fork），并将 `base` 分支分配给在步骤 2 中创建的分支
10. 现在已指定你预期的 PR，你应该审查状态。检查合并冲突，你的所有提交是否都存在，以及所有修改的文件是否合理
11. 如果步骤 10 中存在问题，你仍然可以进行 PR，只需确保结构根据步骤 7-9 正确即可
12. 一旦进行了 PR，CI 流水线 (CI pipeline) 将自动运行，测试你的更改
13. 记住，PR 是灵活的，如果你需要更改代码，你可以返回 fork 的分支进行提交并推送任何更新
14. 只要 `head repository` 的 `base` 分支是你编辑的分支，PR 将自动获得最新更改
15. 当解决所有合并冲突、进行更改并通过测试后，你可以让管理员合并你的 PR

## 什么是好的贡献？ (What Makes a Good Contribution?)
- 如果你贡献代码更改，请查阅 [testing.md](testing.md) 以确保你的测试已集成到 [CI 流水线](continuous_integration.md) 中
- 在 PR 期间，你应该考虑审阅者提供的建议。记住你一直在向打开的 PR 添加提交！
- 如果你的更改旨在修复 Github 上打开的问题 (issue)，请在 PR 本身中标记该问题