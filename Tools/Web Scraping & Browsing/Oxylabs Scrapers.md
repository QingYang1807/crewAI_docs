# Oxylabs 爬虫工具

> Oxylabs 爬虫工具允许您轻松地从各个来源获取信息。请参见下面的可用来源列表：
  - `Amazon Product`（亚马逊产品）
  - `Amazon Search`（亚马逊搜索）
  - `Google Search`（谷歌搜索）
  - `Universal`（通用）

## 安装

通过创建 Oxylabs 账户获取凭据 [点击这里](https://oxylabs.io)。

```shell  theme={null}
pip install 'crewai[tools]' oxylabs
```

查看 [Oxylabs 文档](https://developers.oxylabs.io/scraping-solutions/web-scraper-api/targets) 获取有关 API 参数的更多信息。

# `OxylabsAmazonProductScraperTool`（Oxylabs 亚马逊产品爬虫工具）

### 示例

```python  theme={null}
from crewai_tools import OxylabsAmazonProductScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsAmazonProductScraperTool()

result = tool.run(query="AAAAABBBBCC")

print(result)
```

### 参数

* `query` - 10位符号的 ASIN 代码。
* `domain` - 亚马逊的域名本地化。
* `geo_location` - "配送至"位置。
* `user_agent_type` - 设备类型和浏览器。
* `render` - 当设置为 `html` 时启用 JavaScript 渲染。
* `callback_url` - 您的回调端点 URL。
* `context` - 用于特殊需求的额外高级设置和控制。
* `parse` - 当设置为 true 时返回解析后的数据。
* `parsing_instructions` - 定义您自己的解析和数据转换逻辑，该逻辑将在 HTML 爬取结果上执行。

### 高级示例

```python  theme={null}
from crewai_tools import OxylabsAmazonProductScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsAmazonProductScraperTool(
    config={
        "domain": "com",
        "parse": True,
        "context": [
            {
                "key": "autoselect_variant",
                "value": True
            }
        ]
    }
)

result = tool.run(query="AAAAABBBBCC")

print(result)
```

# `OxylabsAmazonSearchScraperTool`（Oxylabs 亚马逊搜索爬虫工具）

### 示例

```python  theme={null}
from crewai_tools import OxylabsAmazonSearchScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsAmazonSearchScraperTool()

result = tool.run(query="headsets")

print(result)
```

### 参数

* `query` - 亚马逊搜索词。
* `domain` - Bestbuy 的域名本地化。
* `start_page` - 起始页码。
* `pages` - 要检索的页数。
* `geo_location` - "配送至"位置。
* `user_agent_type` - 设备类型和浏览器。
* `render` - 当设置为 `html` 时启用 JavaScript 渲染。
* `callback_url` - 您的回调端点 URL。
* `context` - 用于特殊需求的额外高级设置和控制。
* `parse` - 当设置为 true 时返回解析后的数据。
* `parsing_instructions` - 定义您自己的解析和数据转换逻辑，该逻辑将在 HTML 爬取结果上执行。

### 高级示例

```python  theme={null}
from crewai_tools import OxylabsAmazonSearchScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsAmazonSearchScraperTool(
    config={
        "domain": 'nl',
        "start_page": 2,
        "pages": 2,
        "parse": True,
        "context": [
            {'key': 'category_id', 'value': 16391693031}
        ],
    }
)

result = tool.run(query='nirvana tshirt')

print(result)
```

# `OxylabsGoogleSearchScraperTool`（Oxylabs 谷歌搜索爬虫工具）

### 示例

```python  theme={null}
from crewai_tools import OxylabsGoogleSearchScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsGoogleSearchScraperTool()

result = tool.run(query="iPhone 16")

print(result)
```

### 参数

* `query` - 搜索关键词。
* `domain` - 谷歌的域名本地化。
* `start_page` - 起始页码。
* `pages` - 要检索的页数。
* `limit` - 每页要检索的结果数量。
* `locale` - `Accept-Language` 标头值，用于更改您的谷歌搜索页面网页界面语言。
* `geo_location` - 结果应适应的地理位置。正确使用此参数对于获取正确数据非常重要。
* `user_agent_type` - 设备类型和浏览器。
* `render` - 当设置为 `html` 时启用 JavaScript 渲染。
* `callback_url` - 您的回调端点 URL。
* `context` - 用于特殊需求的额外高级设置和控制。
* `parse` - 当设置为 true 时返回解析后的数据。
* `parsing_instructions` - 定义您自己的解析和数据转换逻辑，该逻辑将在 HTML 爬取结果上执行。

### 高级示例

```python  theme={null}
from crewai_tools import OxylabsGoogleSearchScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsGoogleSearchScraperTool(
    config={
        "parse": True,
        "geo_location": "Paris, France",
        "user_agent_type": "tablet",
    }
)

result = tool.run(query="iPhone 16")

print(result)
```

# `OxylabsUniversalScraperTool`（Oxylabs 通用爬虫工具）

### 示例

```python  theme={null}
from crewai_tools import OxylabsUniversalScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsUniversalScraperTool()

result = tool.run(url="https://ip.oxylabs.io")

print(result)
```

### 参数

* `url` - 要爬取的网站 URL。
* `user_agent_type` - 设备类型和浏览器。
* `geo_location` - 设置代理的地理位置以检索数据。
* `render` - 当设置为 `html` 时启用 JavaScript 渲染。
* `callback_url` - 您的回调端点 URL。
* `context` - 用于特殊需求的额外高级设置和控制。
* `parse` - 当设置为 `true` 时返回解析后的数据，前提是为提交的 URL 页面类型存在专用解析器。
* `parsing_instructions` - 定义您自己的解析和数据转换逻辑，该逻辑将在 HTML 爬取结果上执行。

### 高级示例

```python  theme={null}
from crewai_tools import OxylabsUniversalScraperTool

# 确保 OXYLABS_USERNAME 和 OXYLABS_PASSWORD 变量已设置
tool = OxylabsUniversalScraperTool(
    config={
        "render": "html",
        "user_agent_type": "mobile",
        "context": [
            {"key": "force_headers", "value": True},
            {"key": "force_cookies", "value": True},
            {
                "key": "headers",
                "value": {
                    "Custom-Header-Name": "custom header content",
                },
            },
            {
                "key": "cookies",
                "value": [
                    {"key": "NID", "value": "1234567890"},
                    {"key": "1P JAR", "value": "0987654321"},
                ],
            },
            {"key": "http_method", "value": "get"},
            {"key": "follow_redirects", "value": True},
            {"key": "successful_status_codes", "value": [808, 909]},
        ],
    }
)

result = tool.run(url="https://ip.oxylabs.io")

print(result)
```