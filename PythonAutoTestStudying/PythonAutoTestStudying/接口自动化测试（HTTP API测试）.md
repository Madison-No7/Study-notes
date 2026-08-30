 Python+5大模块+一个框架（pytest）
# 一、接口测试
## 1.何为接口测试
根据接口文档，结合业务逻辑设计出测试用例，通过测试不同情况下的入参与之对应的出参信息来判断接口是否符合或满足相应的功能性、安全性要求。

## 2.接口测试的几个要素
首先要从接口文档中得到一下信息：
- 请求地址                          --request url
- 请求方法（get、post）   --request method
- 请求头（header）           --request header
- 请求体                             --request body
- 返回参数说明
**返回的http状态码说明：**

| **状态码**     | **类别**            | **含义**                        | **接口测试核心场景与断言点**                                         |
| ----------- | ----------------- | ----------------------------- | -------------------------------------------------------- |
| ==**200**== | ==**2xx 成功**==    | ==**OK**==                    | ==请求成功，服务端已正常返回数据（最常见的成功响应）。==                           |
| **201**     | 2xx 成功            | **Created**                   | 资源创建成功（常见于 `POST` 创建用户、新增文章、下单成功）。                       |
| **204**     | 2xx 成功            | **No Content**                | 请求已成功处理，但响应体无内容（常见于 `DELETE` 删除操作）。                      |
| **301**     | **3xx 重定向**       | **Moved Permanently**         | 永久重定向，目标资源已分配新 URL（常用于 HTTP 强制跳转 HTTPS）。                 |
| ==**302**== | ==3xx 重定向==       | ==**Found**==                 | ==临时重定向（常用于未登录时自动拦截并跳转至登录页）。==                           |
| **304**     | 3xx 重定向           | **Not Modified**              | 资源未修改，客户端可直接使用本地缓存（常用于静态资源、减小带宽开销）。                      |
| ==**400**== | ==**4xx 客户端错误**== | ==**Bad Request**==           | ==请求参数错误（如 JSON 格式损坏、必填字段缺失、参数类型不匹配）。==                  |
| ==**401**== | ==4xx 客户端错误==     | ==**Unauthorized**==          | ==未授权/身份未认证（未携带 Token/Cookie、Token 已过期或签名错误）。==          |
| ==**403**== | ==4xx 客户端错误==     | ==**Forbidden**==             | ==已识别身份但无权访问（权限不足，如普通用户调用管理员接口/水平越权被拦截）。==               |
| ==**404**== | ==4xx 客户端错误==     | ==**Not Found**==             | ==请求的 URL 路径不存在、接口未注册，或请求的具体资源 ID 在数据库中不存在。==            |
| **405**     | 4xx 客户端错误         | **Method Not Allowed**        | 请求方法不支持（如接口只支持 `POST`，但客户端使用了 `GET` 发起请求）。               |
| **415**     | 4xx 客户端错误         | **Unsupported Media Type**    | 请求体数据格式不支持（如接口需要 `application/json`，但传了 `form-data`）。    |
| **429**     | 4xx 客户端错误         | **Too Many Requests**         | 请求过多被限流（常用于测试接口的防刷、速率限制或防重放策略）。                          |
| ==**500**== | ==**5xx 服务端错误**== | ==**Internal Server Error**== | ==后端服务内部代码异常（如空指针 `NullPointerException`、未捕获的 SQL 报错）。== |
| **502**     | 5xx 服务端错误         | **Bad Gateway**               | 网关错误（Nginx/网关正常工作，但背后的后端微服务挂掉或未启动）。                      |
| **503**     | 5xx 服务端错误         | **Service Unavailable**       | 服务不可用（后端服务超载或停机维护，熔断降级机制触发）。                             |
| **504**     | 5xx 服务端错误         | **Gateway Timeout**           | 网关超时（Nginx/网关等待后端接口响应超时，通常因数据库慢查询、死锁或代码死循环）。             |
## 3.为什么要进行接口测试
- 防止一些坏人绕过前端检查，直接利用接口非法获取后端信息。
- 前端页面是多变的，只要将接口测好，后端代码不用变
- 可以发现很多在页面上操作发现不了的BUG
前端页面通常只展示部分必要字段（如仅展示“用户名”和“头像”）。
很多接口在底层直接 `SELECT *` 并将实体类完整序列化为 JSON 返回，其中包含了身份证号、密码哈希、内部状态码甚至他人隐私。在页面上看不见，但在接口 Response 抓包中一览无余。
- 检查系统的异常处理能力
页面交互很难主动输入特殊格式（如将数字字段传成数组、传入 `null` 等）。接口测试能直接探测服务端对于非预期输入等边缘场景的**容错与异常兜底能力**。
## 4.如何执行接口测试
基于文档设计测试用例（粗粒度）+结合设计测试用例的方法设计更细粒度的，利用postman/apifox接口测试工具对测试用例测试。
![[postman界面介绍.jpg]]
### 接口用例的设计方法：
1. 接口通过性测试
2. 参数组合性测试
对入参的参数进行组合，校验出参情况
3. 接口安全
   a. 接口参数伪造测试：如购物时，在接口传参时，伪造商品的价格。
   b. 越权：账户信息要互相隔离
   c. 隐私数据安全：检查登录接口上传的用户信息是否加密，以及密码复杂度校验测试。
4. 异常验证：不按照接口文档上的参数输入，检验接口对异常情况的校验。



# 二、接口自动化测试
## 1.何为接口自动化测试

通过编写自动化脚本,代替人工向后端服务端（API / RPC）批量发送网络请求，并自动对==响应状态码==、==业务数据==、==数据结构==以及==数据库状态==进行断言校验的自动化测试手段。
## 2.为何要接口自动化测试
自动化测试帮助测试人员有效提高了测试质量和效率。
==**接口自动化 vs. 手工接口测试（如 Postman 单点调试）**：==

| **对比维度** | **手工接口测试（Postman / 抓包调试）** | **接口自动化测试（Pytest ）**                                                       |
| -------- | -------------------------- | -------------------------------------------------------------------------- |
| **触发方式** | 测试人员手动点击 “Send” 并肉眼比对数据    | 代码一键批量运行，或由 **Git 提交 / CI 流水线自动触发**                                        |
| **执行速度** | 慢（单人每天几十条用例）               | **极快**（百级、千级接口用例可在几分钟内执行完毕）                                                |
| **测试场景** | 单接口单点参数校验为主                | **支持复杂多接口业务链路串联**（如：注册 $\rightarrow$ 登录 $\rightarrow$ 充值 $\rightarrow$ 扣款） |
| **回归成本** | 每次发版上线都需要重复人工点一遍           | **零边际成本**，随时随地跑全量自动化回归门                                                    |
| **数据校验** | 只能看返回的 JSON                | 自动比对 JSON 并**直连数据库/Redis 校验事务一致性**                                         |
## 3.[[接口自动化测试流程.canvas]]
![[Pasted image 20260828143620.png]]
## 4.接口自动化脚本编写示例
首先新建一个项目（使用虚拟环境作为自动化脚本的测试环境），然后再新建一个Python文件：
![[Pasted image 20260819183016.png]]

由于自动化脚本需要向后端发送HTTP请求，因此需要用到Python的第三方request库，用于发送HTTP请求，安装：`pip install requests==2.31.0`。

![[Pasted image 20260819183633.png]]
![[Pasted image 20260819183758.png]]
```PYTHON
import requests  
  
#requests返回的是Response对象，包含了服务器返回的所有信息  
r=requests.get('https://baidu.com')  
print(r)  #<Response [200]>
```
![[Pasted image 20260819205211.png]]
## Response对象提供的属性和方法：
  
| **名称**                   | **类型** | **返回类型**              | **核心功能与使用说明**                                                    |
| ------------------------ | ------ | --------------------- | ---------------------------------------------------------------- |
| ==**`status_code==`**    | 属性     | `int`                 | 返回 HTTP 状态码（如 `200`, `404`, `500`），测试中最基础的断言依据。                  |
| **`ok`**                 | 属性     | `bool`                | 布尔快捷判断：状态码小于 400（即 2xx / 3xx）返回 `True`，4xx / 5xx 返回 `False`。     |
| **`reason`**             | 属性     | `str`                 | HTTP 状态短语描述（如 `'OK'`, `'Not Found'`, `'Internal Server Error'`）。 |
| ==**`text`**==           | 属性     | `str`                 | 以 Unicode ==字符串形式==返回响应体内容，底层根据 `encoding` 自动解码。                 |
| ==**`content`**==        | 属性     | `bytes`               | 以==二进制字节流==形式返回响应体，常用于下载图片、文件或音频等二进制资源。                          |
| ==**`json()`**==         | **方法** | `dict` / `list`       | 将 JSON 格式的响应体自动解析为 Python 字典或列表；格式非法时抛出异常。                       |
| ==**`headers`**==        | 属性     | `CaseInsensitiveDict` | 服务端响应头集合（大小写不敏感字典），如 `res.headers['Content-Type']`。              |
| **`cookies`**            | 属性     | `RequestsCookieJar`   | 响应携带的 Cookie 集合，可遍历或按字典方式提取键值。                                   |
| **`url`**                | 属性     | `str`                 | 请求==最终到达的实际 URL==（若发生重定向，显示重定向后的最终地址）。                           |
| **`history`**            | 属性     | `list[Response]`      | 存放重定向追踪链。若发生重定向（如 301/302），按顺序保存过程中的 Response 对象。                |
| **`elapsed`**            | 属性     | `timedelta`           | 记录从发送请求到收到响应头耗费的时间，可通过 `elapsed.total_seconds()` 获取秒数。           |
| **`encoding`**           | 属性     | `str`                 | 用于解码 `response.text` 的字符编码格式（如 `utf-8`），支持手动修改赋值。                |
| **`apparent_encoding`**  | 属性     | `str`                 | 依赖 `chardet` 库根据响应文本内容自动推断出的真实编码。                                |
| **`request`**            | 属性     | `PreparedRequest`     | 关联的发起本次请求的对象，可用于回溯实际发送的 Headers、URL 与 Body。                      |
| **`raise_for_status()`** | **方法** | `None`                | 状态码校验快捷方式：若为 4xx/5xx 则主动抛出 `HTTPError` 异常，正常则静默通过。               |
| **`iter_content()`**     | **方法** | `generator`           | ==流式分块迭代==读取响应体（配合 `stream=True`），避免大文件下载爆内存。                    |
| **`iter_lines()`**       | **方法** | `generator`           | 按==行迭代==读取响应体，适用于流式响应（SSE / 大模型流式输出 / 大日志分析）。                    |
| ==**`raw`**==            | 属性     | `HTTPResponse`        | 返回原始响应体，不进行任何处理。                                                 |
| **`close()`**            | **方法** | `None`                | 手动释放连接并归还至连接池（通常由上下文管理器 `with` 自动处理）。                            |
**注意：**
如果响应文本为json格式，必须以json打印；响应文本为html格式，必须以text。
## **requests库提供的请求方法：**

```PYTHON
#发起get请求
def get(url, params=None, **kwargs)

#发起post请求
def post(url, data=None, json=None, **kwargs)

#⽀持不同请求⽅式，method：指定请求⽅法，
#⽀持``get``, ``OPTIONS``, ``HEAD``, ``post``, ``PUT``, ``PATCH``, or ``DELETE``

def request(method, url, **kwargs)
```
请求方法的本质都是request()方法，get()、post()这些方法底层都会转去调用request()方法，因此这三个方法在发送请求时，传参无太大区别
![[Pasted image 20260819212017.png]]
## 请求方法可传递的参数如下：

1. `url` 与 `params`（接口路径与查询参数）
	- `params` 会自动将字典编码为查询字符串并拼接到 URL 末尾
![[Pasted image 20260820153107.png]]
2. `json` 与 `data`（请求体参数）
**请求头（Header）：**
- 如果发 JSON：Header 里写 `Content-Type: application/json`
- 如果发表单：Header 里写 `Content-Type: application/x-www-form-urlencoded` 或 `multipart/form-data`
**请求体（Body）**：存放真正的业务数据（JSON 文本或表单序列化串）,以字典的形式。
==注意：若Body中的格式为Json,Content-Type会自动设置为application/json==
![[Pasted image 20260820160608.png]]
![[Pasted image 20260820160547.png]]

| **对比维度**                 | **表单数据（Form Data）**                                              | **JSON 数据**                                      |
| ------------------------ | ---------------------------------------------------------------- | ------------------------------------------------ |
| **请求头 (`Content-Type`)** | `application/x-www-form-urlencoded`<br><br>`multipart/form-data` | `application/json`                               |
| **数据结构支持**               | 仅适合**扁平的键值对**（Key-Value）<br>                                     | 天然支持**多层嵌套**结构、对象数组及复杂层级关系                       |
| **数据类型**                 | 本质上全为**纯文本字符串**或二进制流<br>  <br>无法直接区分数字 `123` 与字符串 `"123"`        | 保留**原生数据类型**（Number、Boolean、String、Array、Null 等） |
| **文件与大文件上传**             | 原生支持极佳（`multipart/form-data` 分块流式传输，开销小）                         | 不适合直接传大文件（需转 Base64 编码，体积膨胀约 33%）                |
| **传输开销与编码**              | 对特殊字符进行 URL Percent 编码，简单键值对开销极小                                 | 语法结构严谨（大括号、引号），解析效率高                             |
| **主流使用场景**               | 传统 HTML 表单提交、文件上传、OAuth 2.0 授权接口                                 | 现代前后端分离架构、RESTful API、微服务间 RPC 调用                |
| **后端接收方式 (以 Spring 为例)** | `@RequestParam` 或无注解直接绑定实体/形参                                    | 必须显式声明 `@RequestBody`                            |

3. `headers` 与 `cookies`（请求头与状态凭证） 
- Headers（请求头）：请求的“身份与规则说明”
**`User-Agent`（客户端标识）**：告诉服务器发起请求的操作系统、浏览器内核及版本。
**`Authorization`（鉴权凭证）**：用于证明用户已成功登录并具备访问当前受保护接口的权限。
- Cookies（会话凭证）：保持登录与偏好状态的“临时小纸条”
**`SESSION_ID`（会话标识）**：用户登录成功后，后端生成的唯一会话 ID。每次请求带上它，后端就知道当前请求来自哪个登录用户。
**Cookie 本质上是 Headers 里的一个特定字段**
3. `files`（文件上传）
一个字典，包含要上传的文件。

4. `auth`（一个元组，包含用户名和密码，用于HTTP认证）
```PYTHON
# 自动在请求头中生成 Authorization: Basic <base64编码的用户名密码>
res = requests.get("https://httpbin.org/basic-auth/admin/123456", auth=("admin", "123456"))
```
6. `timeout`（超时控制）
一个浮点数或元组，指定请求的事件
```PYTHON
# 写法 1: 整体超时（连接 + 读取总时长限制为 3 秒）
res = requests.get("https://httpbin.org/delay/2", timeout=3.0)

# 写法 2: 元组精细化控制 (连接超时 2 秒, 读取响应超时 5 秒)
res = requests.get("https://httpbin.org/get", timeout=(2.0, 5.0))
```
7. `proxies`（代理设置）
一个字典，包含代理服务器的信息。
```PYTHON
# 将 HTTP 和 HTTPS 请求转发至本地 Fiddler/Charles 抓包端口
proxies = {
    "http": "http://127.0.0.1:8888",
    "https": "http://127.0.0.1:8888"
}

res = requests.get("http://httpbin.org/ip", proxies=proxies)
```
8. `verify`（SSL 证书验证）
一个bool值或字符串，指定是否验证SSL证书。
```
# verify=False 忽略 HTTPS 证书不受信任的报错 
res = requests.get("https://192.168.1.100:8443/api/test", verify=False)
```
### 实例：博客详情页接口

```PYTHON
#博客详情页接口  
url='http://47.108.157.13:8090/blog/getBlogDetail'  
params={  
    'blogId':58  
}  
  
header={  
    'User-Token':'eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlck5hbWUiOiJ6aGFuZ3NhbiIsImlhdCI6MTc4NzE0NTkyMywiZXhwIjoxNzg3NzUwNzIzfQ.SaqmmmwC1ZRj-qDb6tyKQ_VlmmOR-2aE9YYH-mrDoLQ'  
}  
  
r1=requests.request(method='get',url=url,params=params,headers=header)  
print(r1.status_code)  
print(r1.json())
```
![[Pasted image 20260820160301.png]]
==requests 库专注于HTTP请求的发送，而 pytest 框架则提供了测试的组织、执行和管理功能。==

## 5.[[pytest框架]]
## 6.YAML
### 1)何为YAML?
它是一种数据序列化语言，用于以人类可读的形式存储。
**核心规则：**
- **严格区分大小写**：`name` 与 `Name` 是两个不同的属性。
- **使用空格缩进表示层级**：不能使用 `Tab` 键缩进，只能使用空格（通常是 2 个空格）。
- **冒号与短横线后必须留空格**：`key: value` 和 `- item` 中的冒号、短横线后必须紧跟一个空格。
- **注释机制**：使用 `#` 表示单行注释。
YAML与Json格式的转换：

| **数据类型**   | **YAML**                                                                                                                 | **JSON**                                                                                                            |
| ---------- | ------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------- |
| **简单标量值**  | `key: value`                                                                                                             | `{"key": "value"}`                                                                                                  |
| **整数和浮点数** | `int_key: 123` / `float_key: 123.456`                                                                                    | `{"int_key": 123, "float_key": 123.456}`                                                                            |
| **布尔值**    | `bool_key: true`                                                                                                         | `{"bool_key": true}`                                                                                                |
| **字符串**    | `string_key: "This is a string"`                                                                                         | `{"string_key": "This is a string"}`                                                                                |
| **列表**     | `list_key:`<br>`- item1`<br>`- item2`<br>`- item3`                                                                       | `{"list_key": ["item1", "item2", "item3"]}`                                                                         |
| **映射（字典）** | `map_key:`<br>`sub_key1: sub_value1`<br>`sub_key2: sub_value2`                                                           | `{"map_key": {"sub_key1": "sub_value1", "sub_key2": "sub_value2"}}`                                                 |
| **嵌套结构**   | `nested_key:`<br>`list_key:`<br>`- item1`<br>`- item2`<br>`map_key:`<br>`sub_key1: sub_value1`<br>`sub_key2: sub_value2` | `{"nested_key": {"list_key": ["item1", "item2"], "map_key": {"sub_key1": "sub_value1", "sub_key2": "sub_value2"}}}` |
格式转换工具：json转yaml
[https://www.jashtool.com/json/to-yaml](https://www.jashtool.com/json/to-yaml)
### 2)为什么要有YAML呢？
在YAML流行之前，行业内主要使用 **XML**、**JSON** 和 **INI** 来存储配置或数据，XML太冗长繁琐（视觉噪音极重）,**JSON 对人类编辑极不友好**(充斥着大量的{}、[ ]、“”且无法注释)，INI 表达能力严重不足（无法表达复杂的树状数据结构）。
YAML常用作**系统配置文件**，要求存储的是什么类型的数据，读取出来之后就是什么类型的数据。
如果用传统的JSON来存储`data={"name":"zhangsan"}`,存储的格式可能是这样的:
```txt
{
	"name":"zhangsan"
}
```
解析出来很麻烦，于此YAML应运而生，避免了XML、JSON、INI的缺点，还符合人类的阅读习惯，**因此 YAML 成为了现代云原生架构、CI/CD 自动化流水线（GitHub Actions、GitLab CI）以及自动化测试数据维护的首选格式。**
### 3)如何使用YAML?
yaml 文件常用作配置文件(*.yaml/yml)，可以使用 PyYAML 库来读取和写入 YAML 文件。
```python
pip install PyYAML==6.0.1
```
#### PyYAML 库的核心操作:读取和写入
##### a. 读取（Load / 反序列化）操作
将 YAML 格式的字符串或文件流解析为 Python 原生数据结构（字典、列表等）。
- `yaml.safe_load(stream)`（最常用、最推荐）
```python
with open("data.yaml", "r", encoding="utf-8") as f:
	#读取的是文档中所有的数据
	#，并将其解析为对应的 Python 原生对象（如字典 `dict`、列表 `list` 等）
    data = yaml.safe_load(f)
```

-  `yaml.safe_load_all(stream)`（多文档读取）
	**作用：**解析包含多个使用 `---` 分隔的 YAML 文档。返回一个生成器（Generator），需要遍历读取。
```python
with open("multi_doc.yaml", "r", encoding="utf-8") as f:
    # 遍历获取每一个 YAML 文档对象
    for doc in yaml.safe_load_all(f):
        print(doc)
```

##### b. 写入（Dump / 序列化）操作
将 Python 原生数据结构转换为 YAML 格式文本并输出到字符串或写入文件
- `yaml.safe_dump(data, stream=None, **kwds)`（最常用、最推荐）
**作用**：安全序列化 Python 对象为标准 YAML 文本。若 `stream=None` 则返回字符串；若传入文件句柄 `stream=f` 则直接写入文件。
**关键配置参数**：
- `allow_unicode=True`：保证中文字符直接显示，不被转义成 `\uXXXX` 编码。
- `sort_keys=False`：保持 Python 字典原有的键值顺序，不按字母强制排序。
- `indent=2`：设置层级缩进空格数（默认为 2）。
```python
data = {"name": "张三", "age": 18}

# 1. 写入文件
with open("output.yaml", "w", encoding="utf-8") as f:
    yaml.safe_dump(data, stream=f, allow_unicode=True, sort_keys=False)

# 2. 转换为字符串
yaml_str = yaml.safe_dump(data, allow_unicode=True)
```

- `yaml.safe_dump_all(documents, stream=None, **kwds)`（多文档批量写入）
**作用**：接收一个包含<font color="#ff0000">多个数据对象的列表</font>，并将其以多文档格式（各文档之间自动添加 `---` 分割）写入文件或转换为字符串。
```python
docs = [{"env": "test", "host": "127.0.0.1"}, {"env": "prod", "host": "192.168.1.1"}]
with open("multi_env.yaml", "w", encoding="utf-8") as f:
    yaml.safe_dump_all(docs, stream=f, allow_unicode=True)
```
##### c.清空 YAML 文件
以覆盖写入模式 `"w"` 打开并立即关闭（最常用、最简洁）:
**原理**：Python 的 `"w"` 模式会在打开文件的瞬间将文件大小**截断为 0 字节**（清空所有旧内容）。
```python
def clear_yaml(filepath):
    """清空指定 YAML 文件中的所有内容"""
    with open(filepath, "w", encoding="utf-8") as f:
        pass  # 什么都不写，直接关闭文件即完成清空
```
**读取和写入yaml文件：**
```python
import yaml  
  
class YamlUtil:  
    #追加写入  
    @staticmethod  
    def write_yaml(filepath, data):  
        """安全追加写入 YAML 文件，支持中文并规范格式"""  
        with open(filepath, "a", encoding="utf-8") as f:  
            # allow_unicode=True: 保持中文正常显示  
            # sort_keys=False: 保持字典原本的键值顺序  
            yaml.safe_dump(data, stream=f, allow_unicode=True, sort_keys=False)  
  
    #读取  
    @staticmethod  
    def read_yaml(filepath):  
        with open(filepath,'r',encoding='utf-8') as f:  
            data=yaml.safe_load(f)  
            return data  
  
    #清空yaml文件  
    @staticmethod  
    def clear_yaml(filepath):  
        with open(filepath,'w',encoding='utf-8') as f:  
            pass  
  
  
def test_yaml():  
    data={  
        "map_key": {  
        "sub_key1": "sub_value1",  
        "sub_key2": "sub_value2"  
        }  
    }    #写入json数据  
    YamlUtil.write_yaml('test.yaml',data)  
  
    #读取出来  
    data=YamlUtil.read_yaml('test.yaml')  
    print(data)
```

## 7.JSON Schema
### 1)何为JSON Schema？
就是用于校验接口返回的json是否符合预期的。
### 2)为什么有 JSON Schema？
我们之间对接口返回值校验有两种方法：
- 一是提前将接口应该返回的json数据保存到一个变量中，然后利用`assert json_data == 接口返回值` 判断接口响应是否符合预期的。但是当接口返回的json数据很多时，这样做将不是一个好的选择，因为这样会导致几乎整个文件都是json数据了。

- 二是只校验关键字段的值，但是这样会漏掉一些重要的字段/值的校验。
这两个方法对于接口有大量返回值的校验都不现实和不安全。
正确的校验方法：
- 接口需要返回的字段是否都存在
- 保证数据的类型是正确的
因为对于一个接口，返回字段对应的值每次可能是不同的，但是同一个接口返回的字段名一定是不变的~
因此 JSON Schema 是用于约束 JSON 数据**应该长什么样**，也就是定义json字段的数据类型、字段的边界情况等，以此来校验接口返回值的准确性。
### 3）如何使用 JSON Schema？
在 Python 中，通常配合官方的 **`jsonschema`** 库来完成断言校验。
#### a. 准备：安装依赖库
```python
pip install jsonschema==4.23.0
```
#### b.json转Json Schema
```python [json转Json Schema在线工具](https://www.lddgo.net/string/generate-json-schema)
    data_json={  
        "name":"zhangsan",  
        "password":"123456"  
    }  
```
根据json数据可以写出用于校验的json_shcema代码。
```python
json_schema={  
        "type":"object",  
        "required":[],  
        "properties":{  
            "name":{  
                "type": "string"  
            },  
            "password":{  
                "type":"string"  
            }  
        }    
    }    
```
从上面的代码对比可见，Json schema从多个方面对Json数据进行校验，如`“type”、“requires”、“properties”`等以确保其正确性和一致性。
> json 数据转JSON Schema 有一点麻烦，可以使用在线工具帮助转换：[json 转 json schema在线工具](https://www.lddgo.net/string/generate-json-schema "Json转Json Schema")
> 工具转化可能会出现错误，还需自己检查一遍。
#### c.示例：校验博客列表页返回的 json 数据
接口返回的json数据：
```josn
{  
    "code": 200,  
    "errMsg": null,  
    "data": [  
        {            
	        "id": 264,  
            "title": "测试标题",  
            "content": "测试内容",  
            "userId": 1,  
            "updateTime": "2026-08-25 14:04"  
        },  
        {            
	        "id": 263,  
            "title": "测试标题",  
            "content": "测试内容",  
            "userId": 1,  
            "updateTime": "2026-08-24 14:01"  
        },  
        {            
	        "id": 262,  
            "title": "自动化测试java.text.SimpleDateFormat@d84b8760",  
            "content": "##在这里写下一篇博客",  
            "userId": 1,  
            "updateTime": "2026-08-23 19:51"  
        }  
    ]
}
```
利用在线工具将json数据转成json_schema校验数据。
```python
import requests  
from jsonschema.validators import validate  
def test_schema():  
    URL="http://47.108.157.13:8090/blog/getList"  
  
    header={  
        'user-token':'eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlck5hbWUiOiJ6aGFuZ3NhbiIsImlhdCI6MTc4NzE0NTkyMywiZXhwIjoxNzg3NzUwNzIzfQ.SaqmmmwC1ZRj-qDb6tyKQ_VlmmOR-2aE9YYH-mrDoLQ'  
    }  
  
    r=requests.request(method='get',url=URL,headers=header)  
  
    json_schema={  
        "type": "object",  
        "properties": {  
            "code": {  
                "type": "integer"  
            },  
            "errMsg": {  
                "type": "null"  
            },  
            "data": {  
                "type": "array",  
                "items": {  
                    "type": "object",  
                    "properties": {  
                        "id": {  
                            "type": "integer"  
                        },  
                        "title": {  
                            "type": "string"  
                        },  
                        "content": {  
                            "type": "string"  
                        },  
                        "userId": {  
                            "type": "integer"  
                        },  
                        "updateTime": {  
                            "type": "string"  
                        }  
                    }                
                }           
	        }        
	    }    
	}  
	
    validate(instance=r.json(),schema=json_schema)
```
![[Pasted image 20260825173328.png]]
以上代码中使用到了`json schema`库中的`validate`函数：
![[Pasted image 20260825175210.png]]
充当“质检员”，检查你的数据（instance）是否符合你制定的规则（schema）。
对于format_checker参数是可选的，是一个格式校验器。JSON Schema 支持对特定格式（如 `email`, `date-time`, `ipv4`）进行校验。如果不传此参数，默认不会校验这些高级格式。
如果数据符合规则，它什么都不会说（即没有返回值）；如果数据不符合规则，或者你的规则本身写得有问题，它就会抛出异常。
#### d.详细的校验规则
##### `I.`数据类型
`type`关键字用于指定json数据中各字段的数据类型。
JSON Schema中常用的数据类型：

| **type**  | **解释**                |
| --------- | --------------------- |
| `string`  | 字符串类型，用于文本数据。         |
| `number`  | 数字类型，用于表示浮点数。         |
| `integer` | 整数类型，用于表示整数。          |
| `boolean` | 布尔类型，值为 true 或 false。 |
| `object`  | 对象类型，用于嵌套的 JSON 对象。   |
| `array`   | 数组类型，用于列表或集合。         |
| `null`    | 空值类型。                 |
示例：
```
{  
    "type": "object",  
    "properties": {  
        "name": {  
            "type": "string"  
        },  
        "password": {  
            "type": "string"  
        }
    }
}
```
`properties` 就用来定义这个对象里**预期会包含哪些键（key）**，以及**每个键对应的值（value）必须符合什么规则**。
但是`properties` 只管它认识的字段，不认识的字段它默认放行。比如你传入的数据是 `{"name": "zhangsan", "age": 18}`，虽然 `"age"` 没有在 `properties` 中定义，默认情况下校验依然会**通过**。
##### `II.`数值约束 (`"type": "integer"` 或 `"number"`)
- `minimum` 和 `maximum`：指定数值的最小值和最大值。(min<= x <=max)
- `exclusiveMinimum` 和 `exclusiveMaximum`：指定数值必须严格大于或小于某个值（不包含等于）。(min < x <max)
```python
def test_schema02():  
    data_json={  
        "data":[  
            {                
	            "name": "zhangsan",  
                "age": 15  
            },  
            {                
	            "name":"lihua",  
                "age":18  
            }  
        ]    
    }    
    json_schema={  
        "type":"object",  
        "properties":{  
            "data":{  
                "type":"array",  
                "items":{  
                    "type":"object",  
                    "properties":{  
                        "name":{  
                            "type":"string"  
                        },  
                        "age":{  
                            "type":"integer",  
                            "minimum": 0,  
                            "maximum": 100  
                        }  
                    }                
                }            
            }        
        }    
    }  
    validate(data_json,json_schema)
```
对json字段加上严格的校验规则，限制json中age字段的值的范围在0<=age<=100。
##### `III.`字符串约束（"type": "string"`)
- pattern:使用[[正则表达式规则]]来校验字符串是否符合特定的模式。
- **`minLength` / `maxLength`**：限制字符串的最小与最大长度。
示例：
```python
def test_schema03():  
    data_json={  
        "name":"zhangsan",  
        "age":18,  
        "telephone":"12345678911",  
        "email":"12345689@163.com"  
    }  
  
    data_schema={  
        "type":"object",  
        "properties":{  
  
            "name":{  
                "type":"string",  
                "pattern":r"^\w{1,8}$"  
            },  
            "age":{  
                "type":"integer"  
            },  
            "telephone":{  
                "type":"string",  
                "pattern":r"^\d{11}$"  
            },  
            "email":{  
                "type":"string",  
                "pattern":r"^\S{6,50}@(163|qq|gmail|outlook|126)\.com$"  
            }  
        }    
    }  
    validate(data_json,data_schema)
```
Json Schema的Pattern默认采用的是部分匹配（类似于正则的**search**）,即从头到尾开始匹配，匹配成功返回第一个成功匹配的对象，匹配失败返回None,注意只匹配一次。
**注意**：在普通的 Python 字典字符串中，`"\w{1,7}"` 中的 `\w` 可能会被识别为**普通字符转义**。如果遇到像 `"\b"` 或 `"\t"` 这类恰好在 Python 和正则中都存在的字符，普通字符串 `"\b"` 会被 Python 解释为 **退格键**，导致正则表达式丢失“单词边界”的原意。建议使用原始字符串 `r"..."` 或写成双反斜杠 `"\\w{1,7}"`。
##### `IV.`数组约束(`"type": "array"`)
- `minItems` 和 `maxItems`：指定数组的最小和最大长度。
- `uniqueItems`：确保数组中的元素是唯一的，默认是False,即允许重复。
- `items`：定义数组中每个元素的类型和约束。
```python
def test_schema04():  
    data_json = {  
        "data":[1,2,3,4,5],  
        "text":"hehe"  
    }  
  
    data_schema = {  
        "type": "object",  
        "properties": {  
            "data":{  
                "type":"array",  
                "minItems":1,  
                "maxItems":5,  
                "uniqueItems":True,  
                "items":{  
                    "type":"number"  
                }  
            },            
            "text":{  
                "type":"string"  
            }  
        }   
    }  
    validate(data_json, data_schema)
```
##### `V.`对象约束(`"type": "object"`)
- **`properties`**：定义对象中每个属性（键）的校验规则。
- **`required`**：定义必须存在的字段列表（数组类型，如 `["id", "name"]`）,缺少报错。
- `additionalProperties`：控制是否允许对象中存在未在 properties 中定义的额外属性，默认为True。
- **`minProperties` / `maxProperties`**：限制对象中最少/最多的键值对数量。
**注意**：配置在哪一层级，就限制那一层级。
```python

```

##### `VI.`依赖关系
`dependentRequired` 可以定义属性之间的依赖关系。例如，如果某个属性存在，则必须存在依赖的属性；如果某个属性不存在，依赖的属性存在与否都行。同样配置在哪一层级，就限制那一层级。
```python
{
  "type": "object",
  "properties": {
    "creditCard": { "type": "string" },
    "billingAddress": { "type": "string" },
    "telephone":{"type":"string"}
  },
  "dependentRequired": {
    "creditCard": ["billingAddress","string"]
  }
}
```
## 8.logging日志模块
### 1）何为logging？
它是Python标准库中的一个模块，提供了灵活的日志记录功能。
作用： 
- 程序调试
- 了解软件程序运行情况是否正常
- 软件程序运行故障分析与问题定位
### 2）使用logging
#### a.日志等级划分解释：
value值越大，日志等级越高。
CRITICAL > ERROR > WARNING > INFO > DEBUG > NOTEST
 ![[Pasted image 20260827023130.png]]
#### b.使用logging模块
默认情况下，只输出warning及之前的日志信息。
```python
import logging  
#日志默认情况下，只输出waring及之前的日志  
logging.debug("这是debug")  
logging.info("这是info")  
logging.warning("这是warning")  
logging.error("这是error")  
logging.critical("这是critical")
```
![[Pasted image 20260827194126.png]]
##### `I.`logging.basicConfig(）全局配置日志文件
logging模块中提供了basicConfig方法，可以配置日志输输出到文件？以及输出的日志文件名？日志文件的打开方式？等等
```python
logging.basicConfig(filename,filemode,level,format)
```
- <font color="#ff0000">filename</font>:指定日志文件的文件名。所有会显示的日志都会存放到这个文件中去
- 
```python
logging.basicConfig(filename='my_log.log',encoding='utf-8')
```
**注意**：还检查一下，日志文件的编码格式，防止写入文件，编码错误。
- <font color="#ff0000">filemode</font>:文件的打开方式，默认是a,追加写入。
- <font color="#ff0000"> level</font>:指定日志显示级别，默认显示warning之前的日志
- <font color="#ff0000">format</font>：指定日志信息的输出格式,有格式表
![[Pasted image 20260827025204.png]]
**示例**：
```python
logging.basicConfig(filename='my_log.log',encoding='utf8',filemode='w',level=logging.INFO,format="[%(asctime)s]\t[%(levelname)s]\t[%(message)s]")

logging.debug("这是debug")  
logging.info("这是info")  
logging.warning("这是warning")  
logging.error("这是error")  
logging.critical("这是critical")
```
![[Pasted image 20260827195621.png]]
##### `II.`日志记录器对象（logger）
在 Python 中，直接使用全局模块级的 `logging.info()` / `logging.basicConfig()` 虽然简单，但在实际的软件测试工程（尤其是自动化测试框架）中，直接操作全局 `logging` 会带来严重的工程缺陷。
软件测试选择实例化并使用专门的**日志记录器对象*。
为什么？

| **维度**       | **全局 logging（模块级调用）**                                   | **日志记录器对象 (Logger)**                                          |
| ------------ | ------------------------------------------------------- | ------------------------------------------------------------- |
| **模块归属与溯源**  | 统一归为根记录器 `root`，无法清晰区分日志来自哪个模块/类                        | 配合 `__name__` 自动携带模块层级路径，定位问题一目了然                             |
| **第三方库日志隔离** | 无法精细隔离，设置全局级别容易导致三方库海量日志刷屏                              | 可针对业务代码开启 `DEBUG`，同时将三方库（如 `requests`、`urllib3`）压制为 `WARNING` |
| **多通道分流能力**  | `basicConfig` 只能统一配置单套全局输出规则                            | 单个 Logger 可挂载多个不同级别和格式的 Handler（如终端输出简讯、文件落盘详细日志）             |
| **层级继承与冒泡**  | 无命名空间层级概念                                               | 支持树状继承，==子 Logger 自动继承父级配置==，便于统一管控子模块                        |
| **配置覆盖与冲突**  | `logging.basicConfig()` 具有“==先到先得、仅生效一次==”的特性，易被依赖库抢占失效 | 独立实例与 Handler 挂载，彻底规避配置被静默忽略或覆盖的风险                            |
###### 创建 Logger 对象(getLogger())
使用工厂函数 `logging.getLogger(name)` 来创建或获取对象,它符合==**单例管理机制**==：使用相同的 `name` 多次调用 `getLogger(name)`，返回的是**同一个 Logger 对象实例**。
###### logger对象的四大组件
- **Logger（记录器）**：入口控制器，接收日志请求并初筛级别,logger的默认日志级别是WARNING。
- **Handler（处理器）**：决定日志输出到哪里（控制台StreamHandler、本地文件FileHandler、轮转文件RotatingFileHandler等）。
- **Formatter（格式器）**：决定每条日志的文本排版和字段。
- **Filter（过滤器）**：提供比日志级别更细粒度的条件过滤。
以上组件都是通过logging调用。
###### logger对象中常用方法

| **方法**                       | **功能说明**                          |
| ---------------------------- | --------------------------------- |
| `logger.setLevel(level)`     | 设置当前记录器的最低日志阈值（如 `logging.DEBUG`） |
| `logger.addHandler(hdlr)`    | 为记录器挂载一个输出处理器（Handler）            |
| `logger.removeHandler(hdlr)` | 移除指定的处理器                          |
| `logger.debug(msg)`          | 记录调试级别的日志                         |
| `logger.info(msg)`           | 记录正常业务/运行日志                       |
| `logger.warning(msg)`        | 记录警告信息                            |
| `logger.error(msg)`          | 记录错误信息                            |
| `logger.critical(msg)`       | 记录严重崩溃/系统级故障日志                    |
**示例**：
```python
import logging  
  
def get_custom_logger(name):  
    #1.创建一个日志记录器对象  
    logger=logging.getLogger(name)  
    #设置当前记录器的最低日志阈值  
    logger.setLevel(logging.DEBUG)  
    #2.定义日志输出格式  
    formatter = logging.Formatter(  
        fmt="%(asctime)s [%(levelname)s] [%(name)s:%(lineno)d]: %(message)s",  
        datefmt="%Y-%m-%d %H:%M:%S"  
    )  
  
    #3.决定日志输出的去向  
    # 创建一个filehandler对象，指定日志文件名，将该对象挂载到日志记录器对象上  
    #就能将日志输出到指定的日志文件中 
    #fileHandler类似于创建文件句柄的操作，它的底层回去调用open()方法，所以函数参数与open()差不多 
    #mode默认是a
    file_handler=logging.FileHandler('test.log',encoding='utf-8',mode='w')  
    file_handler.setLevel(logging.INFO)  
    file_handler.setFormatter(formatter)  
  
  
    #创建一个控制台Handler对象  
    console_handler=logging.StreamHandler()  
    #可以限制日志的级别，格式等  
    console_handler.setLevel(logging.ERROR)  
    file_handler.setFormatter(formatter)  
  
    #4.挂载 Handler(挂载上后，日志记录器会使用挂载的处理器处理数据)  
    logger.addHandler(file_handler)  
    logger.addHandler(console_handler)  
  
    return logger  
  
if __name__ == "__main__":  
    log = get_custom_logger("UserModule")  
    log.info("用户模块初始化成功")  
  
    try:  
        result = 10 / 0  
    except ZeroDivisionError:  
        log.exception("捕获到计算异常：")
```
**终端**:
![[Pasted image 20260827213421.png]]
**日志文件**：
![[Pasted image 20260827213502.png]]
## 9.测试报告 allure
### 1)何为allure?
**Allure 是一个开源的测试报告生成框架（Test Report Framework）**,简单来说就是负责把自动化测试执行产生的数据，转换成可视化的HTML测试报告。
自动化测试流程：
![[Pasted image 20260828100811.png]]
### 2）为什么需要Allure?
用pytest执行的测试：
- 测试结果信息太少
- 不好给别人查看
- 无法形成测试历史
而用Allure生成的测试报告非常丰富：
- 可视化展示，提供饼状图、柱状图、测试趋势图等等。
- 展示测试执行过程
- 支持失败截图
- 支持日志记录
- 支持测试分类
- 支持历史趋势
并且Allure支持几乎所有的测试框架，所有用其他测试框架执行自动化测试后，也可以使用Allure生成测试报告。
### 3）使用Allure?
#### a.安装Allure：
1. 安装`allure-pytest`包：
```python
pip install allure-pytest==2.13.5
```
![[Pasted image 20260828102333.png]]验证是否安装成功：在PyCharm终端输入`allure --version`,出现对应的版本，即为安装成功。
![[Pasted image 20260828104759.png]]
如果在终端输入命令后，提示找不到，则需要修改PyCharm中命令行环境：
![[Pasted image 20260828110156.png]]
修改并应用后，关闭并重新打开PyCharm,在终端输入：`allure --version`即可显示allure对应版本了。

2. 下载Windows版Allure:
- 下载链接：[allure-2.30.0.zip](https://github.com/allure-framework/allure2/releases/download/2.30.0/allure-2.30.0.zip)
- 解压：
![[Pasted image 20260828103518.png]]
解压后将allure对应的bin目录下的路径复制下来：
![[Pasted image 20260828103722.png]]
- 将复制的路径添加到系统环境变量：
键盘按win+R,输入`sysdm.cpl`回车。
![[Pasted image 20260828104416.png]]
- 确认是否安装成功：
打开cmd命令行工具，输入allure --version,出现对应的版本，即为安装成功。
![[Pasted image 20260828104649.png]]
#### b.使用
1. 运行自动化，并指定测试报告放置的路径。
```python
pytest --alluredir=allure-results
```
![[Pasted image 20260828115629.png]]
可见当前项目下自动生成allure-results文件夹，存放了报告相关文件。
> [!tip]
> 每次想要生成测试报告都要在终端输入相关的命令，有些麻烦，可以在`pytest.ini`文件中配置测试报告放置路径，只需输入`pytest`就可生成测试报告了
```python
addopts=-vs --alluredir allure-results
```
2. 查看测试报告
**法一**：启动一个本地服务器在浏览器中展示测试报告
终端执行命令：`allure serve [options] <allure-results>`，自动在浏览器打开测试报告
- `--host`：指定服务器监听的主机地址，默认为 localhost。
- `--port`：指定服务器监听的端口号，默认为 0（自动选择空闲端口）
- `--clean-alluredir`：清除上一次生成的测试报告
```python
#不指定端口号和主机地址，将随机生成一个端口号
allure serve .\allure-results\

#指定端口号,端口号必须是空闲的，不能被占用
allure serve --port 8787 .\allure-results\

#自动清除上一次生成的测试报告
allure serve .\allure-results\ --clean-alluredir
```
![[Pasted image 20260828120935.png]]
**法二**：最终生成的测试报告为html文件，在当前路径下，可自行选择浏览器打开
终端执行命令：`allure generate 测试结果文件夹 -o html测试报告文件夹`
![[Pasted image 20260828121801.png]]
![[Pasted image 20260828121932.png]]
![[Pasted image 20260828122022.png]]
> [!warning]
> 如果历史生成的json文件不清空，生成的测试报告会整合历史的运行情况，并集成在一个测试报告中。
比如我们再次运行同样的测试用例，生成的测试结果文件夹会叠加上原来的测试数据，allure-results下的文件数量就会翻倍。

![[Pasted image 20260828123810.png]]
查看测试报告，每个测试用例中会显示重试的次数。
![[Pasted image 20260828123943.png]]
如果不想让生成的测试报告保留原来的测试数据，可以在`pytest.ini`配置中，设置`assopts= -vs --alluresir allure-results --clean-alluredir`,其中`--clean-alluredir`就是将原来生成的json文件（测试数据）清空。
当重复执行同样的测试用例，并生成测试结果后，执行`allure generate allure-results -o allure-report`,会报这样的错：
![[Pasted image 20260828124908.png]]
在命令后加上`--clean`就好了。
# 三、接口自动化实战
## 1.需求分析
理解业务需求，若是针对未参与的项目实施接口自动化，应与业务人员、产品经理等沟通，了解接口所支持的业务场景和业务逻辑。根据业务需求，明确接口需要实现的具体功能，如数据的获取、修改、删除等操作，以及接口的输入输出要求。分析接口之间的依赖关系，确定接口的调用顺序和依赖条件。
## 2.挑选接口
博客系统中接口较少，可以针对所有的接口实施自动化测试。
若是大型项目，可参考第二章节中接口挑选的几要素去挑选，先对挑选出来的接口自动化测试，后面再编写剩下接口的自动化脚本。

1) 用户登录接口

| 字段           | 值                                                                            |
| ------------ | ---------------------------------------------------------------------------- |
| 接口名称         | 用户登录                                                                         |
| URL          | [http://47.108.157.13:8090/user/login](http://47.108.157.13:8090/user/login) |
| Method       | POST                                                                         |
| 描述           | 使用固定账号登录，返回后续调用所需的 user-token                                                |
| 请求头          |                                                                              |
| 请求体          | 见下方示例                                                                        |
请求体(JSON数据)：
```python
{
    "userName":"zhangsan",
    "password":"123456"
}
```

**成功示例，返回值**：
```python
{
    "code": 200,
    "errMsg": null,
    "data": {
        "id": 1,
        "token": "eyJhbGciOiJIUzI1NiJ9.eyJpZCI6MSwidXNlck5hbWUiOiJ6aGFuZ3NhbiIsImlhdCI6MTc4NzkwMDA1MywiZXhwIjoxNzg4NTA0ODUzfQ.WoIYwGCuQGKMcmodmlhbccWxIZEUhTbhHH5FzdRE4XM"
    }
}
```
 2) 获取博客列表

| 字段                 | 值                                                                                |
| ------------------ | -------------------------------------------------------------------------------- |
| 接口名称               | 获取博客列表                                                                           |
| URL                | [http://47.108.157.13:8090/blog/getList](http://47.108.157.13:8090/blog/getList) |
| Method             | GET                                                                              |
| 描述                 | 获取全部博客文章列表，按创建时间倒序                                                               |
| 请求头                | header：{’User-Token‘:'登录接口返回的token'}                                             |
| 请求体                |                                                                                  |

**成功示例，返回值**：
```python
{
    "code": 200,
    "errMsg": null,
    "data": [
        {
            "id": 321,
            "title": "测试标题",
            "content": "测试内容",
            "userId": 1,
            "updateTime": "2026-08-28 14:01"
        },
        {
            "id": 320,
            "title": "测试无正文博客",
            "content": "##在这里写下一篇博客",
            "userId": 1,
            "updateTime": "2026-08-28 08:38"
        }
    ]
}
```
 3) 新增博客

| 字段                 | 值                                             |
| ------------------ | --------------------------------------------- |
| 接口名称               | 新增博客                                          |
| URL                | http://47.108.157.13:8090/blog/addBlog        |
| Method             | POST                                          |
| 描述                 | 发布一篇新博客，返回是否成功                                |
| 请求头                | header：{’User-Token‘:'登录接口返回的token'}          |
| 请求体                | 见下方示例                                         |

请求体（JSON格式数据）：
```python
{

  "userId": "1",
  "title": "博客标题",
  "content": "博客内容"

}
```
**成功示例，返回值**：
```python
{
    "code": 200,
    "errMsg": null,
    "data": true
}
```
 4) 获取博客详情

| 字段                 | 值                                            |
| ------------------ | -------------------------------------------- |
| 接口名称               | 获取博客详情                                       |
| URL                | http://47.108.157.13:8090/blog/getBlogDetail |
| Method             | GET                                          |
| 描述                 | 根据博客 ID 查看单篇文章详情                             |
| 请求头                | header：{’User-Token‘:'登录接口返回的token'}         |
| Params 参数          | blogId=获取文章的ID                               |


**成功示例，返回值**：
```python
{

    "code": 200,
    "errMsg": null,
    "data": {
        "id": 323,
        "title": "呵呵",
        "content": "##你好",
        "userId": 1,
        "updateTime": "2026-08-28 15:56"
    }
}
```
5) 获取当前登录用户信息

| 字段                 | 值                                          |
| ------------------ | ------------------------------------------ |
| 接口名称               | 获取当前登录用户信息                                 |
| URL                | http://47.108.157.13:8090/user/getUserInfo |
| Method             | GET                                        |
| 描述                 | 返回 token 对应用户的基本信息                         |
| 请求头                | header：{’User-Token‘:'登录接口返回的token'}       |
| Params参数           | userId=当前用户的ID                             |

**成功示例，返回值**：
```python
{
    "code": 200,
    "errMsg": null,
    "data": {
        "id": 1,
        "userName": "zhangsan",
        "githubUrl": "https://gitee.com/bubble-fish666/class-java45"
    }
}
```
 6) 获取博客作者信息

| 字段                 | 值                                                                                                  |
| ------------------ | -------------------------------------------------------------------------------------------------- |
| 接口名称               | 获取博客作者信息                                                                                           |
| URL                | [http://47.108.157.13:8090/user/getAuthorInfo](http://47.108.157.13:8090/user/getAuthorInfo)       |
| Method             | GET                                                                                                |
| 描述                 | 根据博客 ID 返回该文章的作者信息                                                                                 |
| 请求头                | header：{’User-Token‘:'登录接口返回的token'}                                                               |
| Params参数           | `blogld`=博客ID                                                                                      |

**成功示例，返回值**：
```python
{
    "code": 200,
    "errMsg": null,
    "data": {
        "id": 1,
        "userName": "zhangsan",
        "githubUrl": "https://gitee.com/bubble-fish666/class-java45"
    }
}
```
## 3.设计测试用例
针对接口设计测试用例，必须要按照==完备的接口文档==来设计，除此之外，最好也能够看到接口对应的代码，查看接口存在的不同响应，针对不同的响应来设计测试用例。
![[Pasted image 20260828202751.png]]
## 4.编写自动化测试项目
### 1)创建项目与梳理项目总体架构
1) 首先在PyCharm上创建一个空项目，选择**虚拟环境**，做好项目环境的隔离。
2) 然后下载项目所需要的包、工具。
可以当前项目下创建一个`requirement.txt`文件,将项目所需要下载的包统一写到该文件中，然后执行`pip install -r requirement.txt`,就能将文件中的包一键下载了。
![[Pasted image 20260828201501.png]]
3) 构建项目架构
由于我们需要测试6个接口，可以创建6个Python文件，每个文件写对应接口的测试用例，在将这些文件统一管理在`cases`软件包中。
![[Pasted image 20260828212109.png]] 
在自动化测试中，执行各个测试用例都会记录一些日志，以查看程序的运行状态，所以我们可以封装一个公共的日志工具类。统一将日志输出到`logs`目录下。
每个接口都需要用到requests库中的GET或POST请求，所以我们可以封装一个请求工具类。简化代码结构，方便GET与POST请求调用。
测试除登录接口外，其余接口的测试都需要用到User-Token信息，所以我们在成功测试登录接口后，要将响应信息中的User-Token数据保存下来，保存到哪里呢？我们可以创建一个`data`目录，专门用于存放公共数据，因为yaml格式的数据对Json格式的数据存储和解析十分便利，User-Token可以存放在`user_token_date.yaml`文件中。但是我们还要封装读取yaml文件和写入yaml文件的操作类，封装成一个公共的模块，方便后续调用，这是因为接口的测试中都需要用到读取和写入yaml的操作。
**综上**：我们需要封装3个工具类，分别是日志类、接口请求类、yaml数据读写类，还要创建一个data目录，存放要用到的公共数据。
![[Pasted image 20260829165604.png]]
测试用例写完后，就要使用`allure`工具生成测试结果文件夹了，所以还要创建一个allure-results目录存测试结果,最后就是查看测试报告了，所以还需创建一个allure-reports目录存放生成的HTML格式的测试报告。
![[Pasted image 20260829164233.png]]
由于每次在执行自动化测试时，都需要手动输入很多配置选项，很麻烦，所以我们将这些配置选项配置到`pytest.ini`配置文件中，这样就能`pytest`一键执行测试了。
**所以总体的项目架构就是**：
```python
BlogProject_APIAutoTest/
|
├── Cases/                                   # 所有测试脚本存放的目录--根据接口创建测试文件
|   ├── test_userLogin.py                    # 示例: 登录博客的测试用例
|   ├── test_test_getBlogDetail.py           # 示例: 编辑博客的测试用例
|   └── ...
|
├── data/                      # 存放通用数据目录
|   ├── user_token_date.yaml   # 存放用户登录凭证数据 
|   └── ...
|
├── utils/                     # 辅助工具目录
|   ├── logger_util.py         # 封装日志
|   ├── request_util.py        # 封装接口请求
|   ├── yaml_util.py           # 封装yaml操作
|   └── ...
|
├── allure-results/            # 测试结果输出目录
|   ├── xxxx.json
|   └── ...
|
├── allure-report/             # 测试报告输出目录
|   ├── index.html
|   └── ...
|
├── logs/                      # 测试日志目录
|   ├── 2024-12-20.log         # 所有日志
|   ├── 2024-12-20_info.log    # info日志
|   └── 2024-12-20_error.log   # error日志
|
└── pytest.ini                 # 运行配置文件
|
|--requirements.txt            #所要用到的第三方工具和库
```
### 2)实现日志工具类（logger_util.py）
我们要将日志专门存放在logs目录下，日志信息按日期存储在一个独立的文件中：2027-06-01.log  
关于日志，我们会关心info和error级别的日志，看看日志打印的信息和报错的信息 ,分的更细一点，按日期+日志的级别存储日志(2027-06-01_error.log)，方便更好的排查。  
存储架构：
logs:
  - 2027-06-01.log  
  - 2027-06-01_error.log  
  - 2027-06-01_info.logo  
**实现**：
创建一个Logger类：
定义一个类方法，因为不需要关心任何特定的对象：
1. 创建日志记录器对象logger，方法最后要返回创建的日志对象  
2. 定义日志输出的格式formatter  
	- 因为logger的默认日志等级是WARNING,但是我们要输出INFO等级的，所以需要显示指定  
	- 检查是否创建了日志目录，没有则需要创建  
	- 对输出的文件名进行定义 
3. 日志将输出到文件，所以创建一个fileHandler文件处理器对象，负责处理日志信息，让其输出到对应的文件 ，因为我们要分日志级别输出到对应的文件，所以要创建三个文件处理器，分别是处理所有级别的日志、info级别的日志、error级别的日志  
	- 为每个文件处理器设置日志等级阈值  
	- 设置日志输出的格式  
	- 由于info.log文件中只能有info级别的日志，所以需要过滤日志信息 ，所以需要添加过滤器，分别定义日志过滤器类，继承 logging.Filter 类并重写 filter() 方法。  
4. 将创建处理器挂载到日志记录器对象上
```python
class err_filter(logging.Filter):  
    def filter(self, record)->bool:  
        return record.levelno==logging.ERROR  
  
class info_filter(logging.Filter):  
    def filter(self,record)->bool:  
        return record.levelno==logging.INFO  
  
class Logger:  
    logger=None  
    @classmethod  
    def getlog(cls):  
        if cls.logger is None:  
            #%(name)s的值就是: utils.logger_util
            cls.logger=logging.getLogger(__name__)  
            cls.logger.setLevel(logging.INFO)  
            #%(name)s 表示“记录该条日志的日志记录器（Logger）的名称”。  
            formatter=logging.Formatter("%(asctime)s %(levelname)s [%(name)s] [%(filename)s (%(funcName)s:%(lineno)d] - %(message)s")  
            Log_dir='./logs'  
            if not os.path.exists(Log_dir):  
                os.mkdir(Log_dir)  
            now=time.strftime("%Y-%m-%d")  
            all_log=Log_dir+'/'+now+'.log'  
            error_log=Log_dir+'/'+now+'_error'+'.log'  
            info_log=Log_dir+'/'+now+'_info'+'.log'  
  
            alllogs_Handler=logging.FileHandler(all_log,encoding='utf-8')  
            alllogs_Handler.setFormatter(formatter)  
  
  
            errlog_Handler=logging.FileHandler(error_log,encoding='utf-8')  
            errlog_Handler.setFormatter(formatter)  
            errlog_Handler.addFilter(err_filter())  
  
            infolog_Handler=logging.FileHandler(info_log,encoding='utf-8')  
            infolog_Handler.setFormatter(formatter)  
            infolog_Handler.addFilter(info_filter())  
  
            cls.logger.addHandler(alllogs_Handler)  
            cls.logger.addHandler(errlog_Handler)  
            cls.logger.addHandler(infolog_Handler)  
        return cls.logger
```
### 3)接口请求类
封装get与post方法：  
因为每次get或post都要打印日志信息，如果不将get或post封装起来  
每个请求时，都需要手动输入打印的日志信息，会让代码存在许多重复的地方  
因为要打印日志，需要引入日志模块  
因为要使用到get和post请求，需要引入requests模块  
**实现**：
定义Request类：  
  定义构造函数，完成logger对象的初始化  
  定义get和post请求实例方法 ,参数：url、关键字参数(header,Params...)  
  发起请求前打印日志：
- 准备开始发起get请求，url:http://....
- 接口信息是:传入的位置参数  
发起get/post请求，调用requests中的get/post方法，返回response对象r  
发起请求后打印日志：  
- 接口响应状态码:  ....
- 接口响应内容是: ....
```python
from utils.logger_util import Logger  
import requests  
  
host="http://47.108.157.13:8090/"  
class Request:  
    def __init__(self):  
        self.logger=Logger.getlog()  
  
    def get(self,url,**kwargs):  
        self.logger.info(f"准备开始发起get请求:{url}")  
        self.logger.info(f"接口信息是:{kwargs}")  
        #发起请求  
        r=requests.get(url,**kwargs)  
        self.logger.info(f"接口响应状态码:{r.status_code}")  
        self.logger.info(f"接口响应内容是:{r.json()}")  
  
    def post(self, url, **kwargs):  
        self.logger.info(f"准备开始发起post请求:{url}")  
        self.logger.info(f"接口信息是:{kwargs}")  
        # 发起请求  
        r = requests.post(url, **kwargs)  
        self.logger.info(f"接口响应状态码:{r.status_code}")  
        self.logger.info(f"接口响应内容是:{r.json()}")
```
### 4)yaml数据读写类
因为我们要想yaml文件中写入登录的User-Token数据，是JSON格式的数据  
所以写入yaml文件时，一是要传入写入的yaml文件，二是JSON数据  
yaml.safe_dump(data,stream,allow_unicode=True,sort_keys=False)  
  
因为读取ymal文件时，读取到是整个文档的数据，返回的是List或dict  
我们的目的是将用户的User-Token读取出来，读取返回的是List，所以要传入一个key,就能读取出来了。  
data=yaml.safe_load(stream)，返回 data [ key ]  
  
清空Yaml文件：
以'w'的方式打开，然后pass
```python
import yaml  
#data是json格式的数据  
def write_yaml(filename,data):  
    #'a+':追加写入 + 可以读取  
    with open('./data/'+filename+'.yaml','a+',encoding='utf-8') as f:  
        yaml.safe_dump(data,stream=f,allow_unicode=True,sort_keys=False)  
  
#读取(safe_load)  
def read_yaml(filename,key):  
    with open('./data/'+filename+'.yaml','r',encoding='utf-8') as f:  
        y=yaml.safe_load(stream=f)  
        return y[key]  
  
#清除yaml文件  
def clear_yaml(filename):  
    with open('./data/'+filename+'.yaml','w',encoding='utf-8') as f:  
        pass
```
