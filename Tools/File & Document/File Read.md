# 文件读取

> `FileReadTool` 旨在从本地文件系统读取文件。

## 概述

<Note>
  我们仍在努力改进工具，因此未来可能会出现意外行为或变更。
</Note>

FileReadTool 在概念上代表了 crewai_tools 包中的一套功能，旨在促进文件读取和内容检索。
这套工具包括处理批量文本文件、读取运行时配置文件以及导入分析数据等功能。
它支持多种基于文本的文件格式，如 `.txt`、`.csv`、`.json` 等。根据文件类型，该套件提供专门的功能，
例如将 JSON 内容转换为 Python 字典以便于使用。

## 安装

要使用之前归属于 FileReadTool 的功能，请安装 crewai_tools 包：

```shell  theme={null}
pip install 'crewai[tools]'
```

## 使用示例

开始使用 FileReadTool：

```python Code theme={null}
from crewai_tools import FileReadTool

# 初始化工具以读取代理知道或学习路径的任何文件
file_read_tool = FileReadTool()

# 或者

# 使用特定文件路径初始化工具，这样代理只能读取指定文件的内容
file_read_tool = FileReadTool(file_path='path/to/your/file.txt')
```

## 参数

* `file_path`：您想要读取的文件的路径。它接受绝对路径和相对路径。确保文件存在并且您具有访问它所需的必要权限。