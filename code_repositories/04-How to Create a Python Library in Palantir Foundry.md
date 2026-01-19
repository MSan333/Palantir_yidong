在 Palantir Foundry 中创建一个 Python 库（Library）可以帮助您在不同的项目之间复用代码，避免简单的复制粘贴，从而更高效地构建复杂的 PySpark 管道。
以下是基于您提供的资料整理的详细创建步骤：
1. 初始化代码仓库 (Repository)
• 创建位置：首先进入您的文件夹结构（例如 shared resources 文件夹），新建一个文件夹用于存放库（例如命名为 aviation cleaning library）。
• 创建仓库：在该文件夹内创建一个新的 Repository。在创建选项中，必须将仓库类型选择为 Python Library，然后确认名称并点击“Initialize repository”进行初始化。
• 初始状态：初始化后，您将获得一个空的 Python 项目。其中 readme 文件提供了关于如何使用和发布库的说明，而隐藏的 build.gradle 文件则包含了配置信息（可以通过切换“显示隐藏文件”来查看）。
2. 设置库结构与编写代码
• 重命名包：在 source 目录下，默认的根文件夹名称可能是 my project。为了便于识别，建议将其重命名为更有意义的名称（例如 cleaner）。
• 编写代码：在重命名后的包文件夹内创建新的 Python 文件（例如 function.py），并将您的代码逻辑复制进去。
• 处理依赖：如果代码使用了外部库（如 itertools），请确保正确导入，并通过 Lint 工具检查消除错误提示。
• 提交代码：完成代码编写后，提交（Commit）更改并等待初步检查通过。
3. 发布库 (Publishing)
这是最关键的一步，仅提交代码并不会自动发布库：
• 检查日志：提交代码后，查看 library publish 的日志消息，您会发现它显示“skipped”（跳过），并提示需要创建一个标签（Tag）才能发布。
• 创建标签 (Tag)：
    1. 点击代码旁边的 Branches（分支）选项卡。
    2. 进入 Tags 页面。
    3. 创建一个新的 Tag（例如版本号 0.1）。
• 触发发布：创建标签后，系统会自动触发发布机制。等到检查通过（Checks passed），该 Python 库即被成功发布并可供其他项目使用。