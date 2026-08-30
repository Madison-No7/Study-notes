Python 包（Package）就是一个包含了多个 Python 模块（文件）的文件夹。
## **Python包长什么样？**
```
my_package/                 # 这是一个包（文件夹）
├── __init__.py             # 包的标识文件（即使是空文件也可以）
├── user_module.py          # 模块 A（包含处理用户的函数）
├── payment_module.py       # 模块 B（包含处理支付的函数）
└── sub_package/            # 子包（嵌套的文件夹）
    ├── __init__.py
    └── helper.py
```
**`__init__.py` 的作用**：告诉 Python “**请把这个文件夹当成一个包来对待**”，允许你通过 `import` 去导入里面的文件。

## **区分库、包、文件？**

|**概念**|**英文**|**解释**|**形象比喻**|
|---|---|---|---|
|**模块**|Module|一个独立的 `.py` 文件|零件（如一颗螺丝）|
|**包**|Package|包含多个 `.py` 模块和 `__init__.py` 的**文件夹**|工具盒（装了多样零件）|
|**库**|Library|泛指实现某种完整功能的代码集合（可能由多个包和模块组成）|工具箱/维修车间（如 `Selenium`、`Pandas`）|
## Python包的来源？
### 1）内置的包
例如处理 JSON 的 `json` 包、处理时间的 `datetime` 包：
```
import json

data = json.dumps({"name": "Gemini"})
```
### 2）第三方包（需要用 `pip` 安装）
比如编写自动化脚本的`selenium`、爬虫用的 `requests`包等，需要先在终端/命令行安装：
```
pip install requests
```
然后在代码里通过点号 `.` 层级引用导入：
```
import requests  # 导入整个包

# 或者从某个包的子模块中导入具体的类或函数：
from selenium.webdriver.common.by import By
```