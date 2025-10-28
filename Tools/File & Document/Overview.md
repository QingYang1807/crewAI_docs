# 概述

> 使用 CrewAI 的文档处理工具读取、写入和搜索各种文件格式

这些工具使您的代理能够处理各种文件格式和文档类型。从读取 PDF 到处理 JSON 数据，这些工具满足您的所有文档处理需求。

## **可用工具**

<CardGroup cols={2}>
  <Card title="文件读取工具" icon="folders" href="/en/tools/file-document/filereadtool">
    从任何文件类型读取内容，包括文本、markdown 等。
  </Card>

  <Card title="文件写入工具" icon="file-pen" href="/en/tools/file-document/filewritetool">
    将内容写入文件，创建新文档，并保存处理后的数据。
  </Card>

  <Card title="PDF 搜索工具" icon="file-pdf" href="/en/tools/file-document/pdfsearchtool">
    高效地搜索和提取 PDF 文档中的文本内容。
  </Card>

  <Card title="DOCX 搜索工具" icon="file-word" href="/en/tools/file-document/docxsearchtool">
    搜索 Microsoft Word 文档并提取相关内容。
  </Card>

  <Card title="JSON 搜索工具" icon="brackets-curly" href="/en/tools/file-document/jsonsearchtool">
    使用高级查询功能解析和搜索 JSON 文件。
  </Card>

  <Card title="CSV 搜索工具" icon="table" href="/en/tools/file-document/csvsearchtool">
    处理和搜索 CSV 文件，提取特定的行和列。
  </Card>

  <Card title="XML 搜索工具" icon="code" href="/en/tools/file-document/xmlsearchtool">
    解析 XML 文件并搜索特定元素和属性。
  </Card>

  <Card title="MDX 搜索工具" icon="markdown" href="/en/tools/file-document/mdxsearchtool">
    搜索 MDX 文件并从文档中提取内容。
  </Card>

  <Card title="TXT 搜索工具" icon="file-lines" href="/en/tools/file-document/txtsearchtool">
    使用模式匹配功能搜索纯文本文件。
  </Card>

  <Card title="目录搜索工具" icon="folder-open" href="/en/tools/file-document/directorysearchtool">
    在目录结构中搜索文件和文件夹。
  </Card>

  <Card title="目录读取工具" icon="folder" href="/en/tools/file-document/directoryreadtool">
    读取和列出目录内容、文件结构和元数据。
  </Card>

  <Card title="OCR 工具" icon="image" href="/en/tools/file-document/ocrtool">
    使用具有视觉功能的 LLM 从图像（本地文件或 URL）中提取文本。
  </Card>

  <Card title="PDF 文本写入工具" icon="file-pdf" href="/en/tools/file-document/pdf-text-writing-tool">
    在 PDF 中的特定坐标处写入文本，可选择自定义字体。
  </Card>
</CardGroup>

## **常见用例**

* **文档处理**：从各种文件格式提取和分析内容
* **数据导入**：从 CSV、JSON 和 XML 文件读取结构化数据
* **内容搜索**：在大型文档集合中查找特定信息
* **文件管理**：组织和操作文件及目录
* **数据导出**：将处理结果保存到各种文件格式

## **快速入门示例**

```python  theme={null}
from crewai_tools import FileReadTool, PDFSearchTool, JSONSearchTool

# 创建工具
file_reader = FileReadTool()
pdf_searcher = PDFSearchTool()
json_processor = JSONSearchTool()

# 添加到您的代理
agent = Agent(
    role="文档分析师",
    tools=[file_reader, pdf_searcher, json_processor],
    goal="处理和分析各种文档类型"
)
```

## **文档处理技巧**

* **文件权限**：确保您的代理具有适当的读/写权限
* **大文件**：对于非常大的文档考虑分块处理
* **格式支持**：查看工具文档了解支持的文件格式
* **错误处理**：为损坏或无法访问的文件实施适当的错误处理