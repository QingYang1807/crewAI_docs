# PDF 文本写入工具

> `PDFTextWritingTool` 用于在 PDF 的特定位置写入文本，支持自定义字体。

# `PDFTextWritingTool`

## 描述

在 PDF 页面的精确坐标处写入文本，可选择嵌入自定义 TrueType 字体。

## 参数

### 运行参数

* `pdf_path` (字符串，必需)：输入 PDF 的路径。
* `text` (字符串，必需)：要添加的文本。
* `position` (元组[整数, 整数]，必需)：`(x, y)` 坐标。
* `font_size` (整数，默认 `12`)
* `font_color` (字符串，默认 `"0 0 0 rg"`)
* `font_name` (字符串，默认 `"F1"`)
* `font_file` (字符串，可选)：`.ttf` 文件的路径。
* `page_number` (整数，默认 `0`)

## 示例

```python 代码主题={null}
from crewai import Agent, Task, Crew
from crewai_tools import PDFTextWritingTool

tool = PDFTextWritingTool()

agent = Agent(
    role="PDF 编辑器",
    goal="注释 PDF",
    backstory="文档专家",
    tools=[tool],
    verbose=True,
)

task = Task(
    description="在 ./sample.pdf 的第 1 页的 (72, 720) 位置写入 'CONFIDENTIAL'",
    expected_output="确认消息",
    agent=agent,
)

crew = Crew(
    agents=[agent], 
    tasks=[task],
    verbose=True,
)

result = crew.kickoff()
```

### 直接使用

```python 代码主题={null}
from crewai_tools import PDFTextWritingTool

PDFTextWritingTool().run(
  pdf_path="./input.pdf",
  text="CONFIDENTIAL",
  position=(72, 720),
  font_size=18,
  page_number=0,
)
```

## 提示

* 坐标原点在左下角。
* 如果使用自定义字体（`font_file`），请确保它是有效的 `.ttf` 文件。