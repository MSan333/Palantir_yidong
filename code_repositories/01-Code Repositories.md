根据 Palantir Developers 提供的视频资料，Code Repositories 是 Palantir Foundry 中用于编写数据转换逻辑的核心工具。通过使用 Python 和 PySpark，用户可以创建从简单的单输入/单输出到复杂的多输入/多输出的数据管道。
以下是关于如何在 Foundry 中编写数据转换的详细介绍：
1. 创建和初始化代码仓库 (Code Repository)
要开始编写转换代码，首先需要在项目中创建一个代码仓库：
• 创建仓库：在项目文件夹中新建一个 Repository（仓库）。视频中建议使用 transforms 作为命名前缀（例如 transforms-flight）以遵循命名规范。
• 初始化环境：选择将仓库初始化为 "Data Transforms in Python"。这将提供一个包含启动 Python 项目的引导式代码仓库。
• 创建文件：在文件夹结构中新建一个 .py 文件，系统会自动生成一个模板转换代码，用户可以直接在此基础上进行填充。
2. 编写单输入/单输出转换 (Single Input/Output)
最基础的转换是将一个数据集作为输入并生成一个输出：
• 定义输入：需要找到源数据集（例如 flights 数据集），复制其路径并粘贴到转换函数的输入变量中。
• 恒等转换 (Identity Transform)：如果只是简单地将输入数据集的数据复制到输出，这被称为恒等转换。此时代码不需要复杂的逻辑，输入数据会被直接写入输出。
• PySpark 代码逻辑：使用 @transform_df 装饰器时，计算函数会获得一个 DataFrame。你可以直接针对它编写 PySpark 代码。例如，可以通过连接年、月、日列来创建一个新的 date 列。
3. 处理多输入与数据连接 (Multi-Input & Joins)
Foundry 允许在一个转换中引入多个数据集进行处理：
• 数据检查：在进行连接（Join）之前，可以通过点击列标题查看直方图，以确认连接键（如 carrier code）是否唯一，从而避免“连接爆炸”（join explosion）。
• 添加输入：在 @transform_df 中添加第二个输入变量（例如 carrier），并将其传递给计算函数。
• 执行连接：在函数内部编写 PySpark 代码将两个 DataFrame（如 flights 和 carrier）连接起来。
4. 编写多输出转换 (Multi-Output Transformations)
当需要一个转换生成多个输出数据集时，代码结构会有所变化：
• 使用 @transform 装饰器：不能再使用 @transform_df，必须切换到 @transform。原因在于 @transform_df 会直接将计算函数的返回值作为单一输出，而 @transform 允许更灵活的处理。
• 读写操作：
    ◦ 输入：需要显式地从输入变量中提取 DataFrame，例如使用 flights.dataframe()。
    ◦ 输出：不能直接 return DataFrame，而是需要调用输出对象的写入方法，例如 output.write_dataframe(df)。
    ◦ 需要在装饰器中为每个输出定义变量。
5. 构建、预览与调试 (Build, Preview & Debug)
• 预览 (Preview)：点击预览可以基于少量数据（如 10,000 行）快速查看计算结果，用于快速迭代开发。
• 构建 (Build)：构建操作会在完整的数据集（如 420 万行）上运行计算，并生成最终的输出数据集。
• 调试：如果构建失败（例如忘记更改输出名称导致冲突），系统会显示堆栈跟踪（stack trace）。用户应阅读这些日志以理解错误原因并进行修复。
• 数据血缘 (Data Lineage)：构建成功后，可以通过数据血缘工具查看管道结构。你可以看到转换节点及其父节点（输入）和子节点（输出），无论是简单的单输入结构还是复杂的多输出结构。
总结来说，在 Foundry 的 Code Repositories 中，用户利用 PySpark 编写转换逻辑，通过 @transform_df 处理简单的单输出任务，或使用 @transform 处理复杂的多输出任务，并利用内置的预览和血缘工具来管理数据管道。