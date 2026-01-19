在 Palantir Foundry 中，处理 Excel 文件（.xlsx）通常需要比处理 CSV 或 Parquet 等标准格式更多的自定义逻辑，因为 Foundry 不会自动解析这种复杂的文件类型。
以下是基于 Palantir Developers 教程整理的详细指南，介绍如何在 Code Repositories 中将原始 Excel 文件解析为可用的 Foundry 数据集。
1. 数据导入与准备 (Data Import)
首先，你需要将原始 Excel 文件导入 Foundry 平台：
• 上传文件：通过拖放功能（drag and drop）将 Excel 文件（例如 colors1.xlsx）上传。
• 数据集类型：选择 "Upload to a dataset without a schema"（上传到无架构数据集）。这一点至关重要，因为它允许数据集包含任意原始文件，而不是试图立即将其强制转换为表格格式。
• 重命名：为了方便后续引用，可以将数据集重命名（例如命名为 favorite colors）。
2. 代码库设置与依赖导入 (Code Repository Setup)
在 Code Repository 中编写转换逻辑时，需要进行以下调整：
• 转换类型选择：不能使用标准的 transform_df，而必须切换到 transform 装饰器。
    ◦ 原因：transform_df 用于将一个数据集转换为另一个数据集，而 transform 则用于将原始文件（raw files）转换为数据集。
• 定义输入输出：定义输出变量，并通过输入数据集的资源标识符（RID）来定义输入源。
• 导入必要的包：
    ◦ 从 pyspark.sql 导入 Row 对象。
    ◦ 从 pyspark.sql.types 导入 StringType, StructType, StructField 以构建架构。
    ◦ 导入 xlrd 包，这是用于读取 Excel 文件的关键库。
3. 读取文件系统 (Reading the Filesystem)
在转换函数内部，逻辑流程如下：
• 获取文件列表：定义一个变量（如 file_status）来获取输入源中的文件列表。如果源中包含多种文件类型，可以使用 glob 进行筛选。
• 锁定目标文件：
    ◦ 教程中通过断言确保只有一个文件，并将其定义为 latest_file。
    ◦ 提示：如果你有多个文件需要处理，可以将后续的代码逻辑包裹在一个 for 循环中，对每个文件进行迭代。
• 打开文件：通过访问源文件系统（source filesystem）来读取文件。
    ◦ 关键点：使用 latest_file.path 获取路径，并使用 rb (read binary) 模式打开文件，因为 Excel 是非文本文件。
    ◦ 使用 xlrd 打开工作簿（workbook）。
4. 解析 Excel 内容 (Parsing Logic)
读取工作簿后，需要提取数据并将其转换为 PySpark 可以识别的格式：
• 选择工作表：指定要读取的工作表（Sheet），例如 sheet1。
• 提取表头：通常通过获取第一行（索引为 0）来提取表头（headers）。
• 遍历行数据：
    ◦ 创建一个空列表 rows 用于存储中间数据。
    ◦ 使用 for 循环处理剩余的行。
    ◦ 在循环中，使用导入的 Row 对象 将表头和当前行的数据打包（zip）并生成对象，然后追加到 rows 列表中。这一步是为了让 PySpark 稍后能自动将其识别并转换为 DataFrame。
5. 定义架构与输出 (Schema & Output)
最后一步是将解析出的数据写入 Foundry 数据集：
• 创建架构 (Schema)：
    ◦ PySpark 中的架构是由 StructField 组成的 StructType。
    ◦ 你需要根据 Excel 的列（如 name, age, color）手动定义架构，例如将它们都定义为 StringType。
• 生成 DataFrame：
    ◦ 调用 ctx.spark_session.createDataFrame，传入之前生成的 rows 列表和你定义的 schema。
• 写入结果：
    ◦ 使用 out.write_dataframe() 将生成的 DataFrame 写入输出变量。
完成代码后，点击 Build 运行转换。构建完成后，原本的 Excel 文件就会被格式化为标准的 Foundry 数据集，可以像其他数据表一样被查询和使用。