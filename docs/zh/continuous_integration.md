# 持续集成 (Continuous Integration)
- 每次推送到仓库时，持续集成流水线都会运行 (Each time you push to the repo, the Continuous Integration pipeline will run)
- 该流水线包括创建正确的开发环境、构建代码和运行所有测试 (This pipeline consists of creating the correct development environment, building your code, and running all tests)
- 这是一个庞大的流水线，因此可能需要一些时间才能完成 (This is an extensive pipeline so it might take some time to complete)

## 保护 master 分支 (Protecting Master Branch)
导航到你的仓库 (Navigate to your Repository):
在 GitHub 上打开你的仓库。

点击 "Settings" (Click on "Settings"):
在仓库页面的右上角，点击 "Settings" 标签。

在左侧边栏中选择 "Branches" (Select "Branches" in the left sidebar):
在左侧边栏中，查找 "Branches" 选项并单击它。

选择分支 (Choose the Branch):
在 "Branch protection rules" 下，选择你要保护的分支。在这种情况下，选择 main 分支。

启用分支保护 (Enable Branch Protection):
选中 "Protect this branch" 复选框。

配置保护设置 (Configure Protection Settings):
你可以配置各种保护设置。一些常见的设置包括：

需要拉取请求审查才能合并 (Require pull request reviews before merging): 确保存改在合并之前经过审查。
需要通过状态检查才能合并 (Require status checks to pass before merging): 确保自动化测试和检查通过。
需要签名提交 (Require signed commits): 强制提交使用经过验证的签名进行签名。
限制谁可以推送 (Restrict Who Can Push):
你可以进一步限制谁可以直接推送到分支。你可能希望将此特权限制为特定人员或团队。

保存更改 (Save Changes):
配置保护设置后，向下滚动并点击 "Save changes" 按钮。

现在，你的主分支受到保护，必须满足特定标准才能将更改直接推送到它。贡献者需要创建拉取请求，对其更改进行审查，并在将更改合并到主分支之前满足其他指定标准。