
# 1.什么是pytest?
	**pytest** 是 Python 生态中最流行、功能最强大的**第三方自动化测试框架**。它既适合编写简单的单元测试，也能与selenium、requests、appinum等结合，实现Web自动化、接⼝自动化、App自动化测试。
# 2.为何选择使用它？
1. 极简的原生断言（无需复杂的 API）
2. 自动发现测试用例（Test Discovery）
	遵循==默认==命名规则自动递归寻找并运行测试，无需手动维护测试套件：
	- 文件名：`test_*.py` 或 `*_test.py`
	- 测试类名：`Test*`（且不能有 `__init__` 方法）
	- 测试函数/方法名：`test*()`
3. 参数化测试（`@pytest.mark.parametrize`）
	支持一行装饰器传入多组测试数据，自动生成独立的测试用例，非常适合接口自动化测试中**正向/逆向**用例组合。
```
	@pytest.mark.parametrize("user, pwd, expected_code", [
    ("admin", "123456", 200),
    ("", "123456", 400),
    ("admin", "", 400),
])
def test_login(user, pwd, expected_code):
    # 发送请求并验证
    ...
```
4. 丰富的插件生态
	支持数百款开源插件，开箱即用扩展功能：
	- **pytest-html / allure-pytest**：生成可视化测试报告。
	- **pytest-xdist**：多进程并发执行测试，大幅缩短测试时间。
	- **pytest-ordering / pytest-rerunfailures**：控制执行顺序、失败自动重试。
# 3.如何使用它？
**首先安装pytest工具：**`pip install pytest==8.3.2`
所有测试函数必须满足命名规则：
- 文件名：`test_*.py` 或 `*_test.py`
- 测试类名：`Test*`（且不能有 `__init__` 方法）
- 测试函数/方法名：`test*()`
满足规则，直接通过命令行参数`pytest`即可运行符合条件的用例。
![[Pasted image 20260820171858.png]]
![[Pasted image 20260820171921.png]]
## 1）pytest提供的命令行参数
  
| **命令**                      | **描述**                  | **备注**                                                            |
| --------------------------- | ----------------------- | ----------------------------------------------------------------- |
| `pytest`                    | 在当前目录及==其子目录==中搜索并运行测试。 |                                                                   |
| `pytest -v`                 | 增加==输出的详细程度==。          | 能准确输出测试用例来之哪个文件夹的哪个文件的哪个类的哪个函数                                    |
| `pytest -s`                 | 显示测试中的 `print` 语句。      |                                                                   |
| `pytest test_module.py`     | 运行指定的测试模块。              | 比如：`pytest .\test\test_01.py`<br>指定运行此路径下的test目录下的test_01模块中的测试用例 |
| `pytest test_dir/`          | 运行指定目录下的所有测试。           | ==注意：==还能指定到某个文件中的具体测试用例  ==pytest 包名/文件名::类名::方法名==              |
| `pytest -k <keyword>`       | 只运行测试名包含==指定关键字==的测试。   | 比如：pytest -k "test02"<br>只运行测试用例中包含“test02”的测试用例                  |
| `pytest -m <marker>`        | 只运行==标记为指定标记==的测试。      |                                                                   |
| `pytest -q`                 | 减少输出的详细程度。              |                                                                   |
| `pytest --html=report.html` | 生成 HTML 格式的测试报告。        | 需要安装 `pytest-html` 插件                                             |
| `pytest --cov`              | 测量测试覆盖率                 | 需要安装 `pytest-cov` 插件                                              |
#### **pytest -m < marker>**:
需要在代码中给用例打上 `@pytest.mark.<标签名>` 装饰器，适合区分冒烟测试（smoke）、回归测试（regression）、慢速用例（slow）等场景。
**测试代码示例：**
```
import pytest

@pytest.mark.smoke
def test_quick_check():
    assert True

@pytest.mark.smoke
@pytest.mark.api
def test_core_login():
    assert True

@pytest.mark.slow
def test_large_data_export():
    assert True
```
**注意：**
在给测试用例打上`@pytest.mark.<标签名>` 装饰器之前，需要在`pytest.ini`文件中显式注册你自定义的测试标签名。如果未在 ini 中注册，pytest 会发出 `PytestUnknownMarkWarning: Unknown pytest.mark.smoke - is this a typo?`（怀疑你手抖打错了标签名）。
![[Pasted image 20260823021354.png]]
注册了则不会报警告。
![[Pasted image 20260823021437.png]]
**终端执行命令：**
	组合标记运行（带 `smoke` 且带 `api`）：
```
	pytest -m "smoke and api"
```
问题来了，当我们既要详细输出，又要指定文件时，命令会变得又臭又长，而且每次运行都需要手动输入命令，如何解决？可以将要这些命令写在配置文件中，完成配置后，一键`pytest`即可。
## 2）pytest配置文件（pytest.ini）
配置文件不光可以避免输入又长又臭的命令，还可以==自定义匹配测试用例的规则==。

| **参数**             | **解释**                            |
| ------------------ | --------------------------------- |
| `addopts`          | 指定在命令行中默认包含的选项。                   |
| `testpaths`        | 以文件夹前缀匹配。                         |
| `python_files`     | 以文件前缀匹配。                          |
| `python_classes`   | 以类名前缀匹配。                          |
| `python_functions` | 以函数名前缀匹配。                         |
| `norecursedirs`    | 告诉 pytest **“哪些文件夹绝对不要钻进去找测试用例”** |
| `markers`          | 在配置文件中**显式注册你自定义的测试标签名**          |
|                    |                                   |
`pytest.ini` 文件通常位于==项目的根目录==下。通过在 `pytest.ini` 中定义配置项，可以覆盖 `pytest` 的默认行为，以满足项目的需求。
#### **为什么需要`norecursedirs`？**
因为pytest 默认会递归遍历当前项目下的每一个子文件夹,而项目中常存在庞大的依赖目录（如虚拟环境 `.venv`、构建产物 `build`、版本控制 `.git`、缓存 `__pycache__` 等）。如果不做限制，pytest 会把第三方库里的测试用例也扫进来执行，不仅导致**搜索极其缓慢**，还会引发各种**第三方库冲突和报错**。
```python
; 不要去一下目录中查找测试用例  
norecursedirs = .* venv .venv build dist __pycache__
```
#### **`markers`：**
**通俗含义**：在配置文件中**显式注册你自定义的测试标签名**（配合 `@pytest.mark.标签名` 和命令行 `pytest -m 标签名` 使用）。
**为什么需要它？**
便于**用例分类与文档化**，注册时可以写明每个标签的作用（如冒烟用例、接口用例、耗时用例），团队协作时方便通过 `pytest --markers` 查看当前环境下所有可用的标记（包含自定义标记+Pytest 内置标记）。
![[Pasted image 20260823022056.png]]
**示例：**
配置文件要在项目的根目录下创建。
![[Pasted image 20260823023434.png]]
这个配置文件的意思是：找到当前路径下apiTest开头的文件以Blog类名下的test_开头的测试用例，详细输出并打印print结果。
![[Pasted image 20260823024004.png]]
![[Pasted image 20260823024028.png]]
## 3）前后置：
为了解决使用pytest框架，测试类中不可以添加init()方法的问题。
### <mark style="background:#fdbfff">何为前后置？</mark>
在测试用例执行**之前**和**之后**自动触发的准备与清理操作。
**前置**：登录系统获取 Token、向数据库插入初始测试数据、启动浏览器驱动（WebDriver）、建立网络连接。
**后置**：==清理==测试生成的脏数据、关闭浏览器窗口、关闭数据库连接池、注销登录。
### **如何实现？**
#### 法一：setup & teardown
**函数前后操作**
setup_method和teardown_method用于每个测试方法执行前后的操作：
![[Pasted image 20260823162905.png]]
![[Pasted image 20260823163138.png]]
**类前后操作**
setup_class和teardown_class用于<font color="#e36c09">每个</font>测试类<font color="#e36c09">执行</font>的前后操作：
![[Pasted image 20260823163749.png]]
![[Pasted image 20260823163820.png]]
#### 法二：fixture
## 4）断言：
用于检查程序的状态是否符合预期。如果断言失败，Python解释器会抛出一个`AssertionError`异常；当断言失败时，可以在 `assert` 表达式后追加逗号和自定义字符串，用于更精准地指明业务失败原因。
```python
#条件必须是一个布尔表达式
#错误信息可选
assert 条件，错误信息
```
### a. 基本数据类型的断言
```python
def test_int(self):  
    assert 1==2
```

### b.数据结构断言（列表list [ ]、元组tuple( )、字典dict、集合set{ }）
列表与集合的区别：

|**特性**|**列表 (list)**|**集合 (set)**|
|---|---|---|
|**定义语法**|方括号 `[1, 2, 3]`|花括号 `{1, 2, 3}` 或 `set()`|
|**元素唯一性**|**允许重复**（可包含相同元素）|**元素唯一**（自动去重）|
|**是否有序**|**有序**（保留插入顺序）|**无序**（概念上不保证顺序，不支持基于位置的排序）|
|**索引与切片**|**支持**（如 `lst[0]`, `lst[1:3]`）|**不支持**（不能通过下标访问，如 `s[0]` 会报错）|
|**元素类型限制**|任意对象（可变、不可变均可）|**仅支持可哈希对象（Hashable）**，如数字、字符串、元组（不能放列表/字典）|
|**底层实现**|**动态数组**（连续内存空间）|**哈希表**（Hash Table）|
|**查找复杂度**|**$O(n)$**（元素越多，查找越慢）|**$O(1)$**（平均常数时间复杂度，查询极快）|
```python
@pytest.mark.smoke  
class Test_assert:  
    def test_str(self):  
        str='hello'  
        assert 'hell'==str  
  
    def test_list(self):  
        expect_list=[1,3.14,'heihei']  
        actually_list=[1,3.14,'heihei']  
        assert expect_list==actually_list  
  
    def test_tuple(self):  
        expect_list = (1,23.14,'haha')  
        actually_list = (1,23.14,'haha')  
        assert expect_list == actually_list  
  
    def test_dict(self):  
        expect_list = {  
            'username':'xiaoming',  
            'password':'123456'  
        }  
        actually_list ={  
            'username':'xiaoming',  
            'password':'123456'  
        }  
        assert expect_list == actually_list  
  
    def test_set(self):  
        expect_list = {1,3.14,'huhu'}  
        actually_list = {1,3.14,1,'huhu'}  
        assert expect_list == actually_list
```
![[Pasted image 20260823171649.png]]
### c.函数断言
```python
def divide(a,b):  
    assert b!=0,'除数不能为零'  
    return a/b  
@pytest.mark.funcTest  
class functionTest:  
    def test_func(self):  
        print(divide(10,5))  
        print(divide(5,0))
```
![[Pasted image 20260823172551.png|700]]
### d.接口返回值断言
![[Pasted image 20260823173401.png]]
![[Pasted image 20260823173509.png]]
## 5)参数化
**参数化（Parametrization）** 是一种用不同的测试数据多次运行同一个测试函数的机制。通过 `@pytest.mark.parametrize` 装饰器，可以实现**测试逻辑与测试数据的分离**，避免为每组输入重复编写相似的测试用例。
### a.单参数多组输入
![[Pasted image 20260823180348.png|700]]
![[Pasted image 20260823180418.png]]
(pytest 会将该函数自动拆解为 3 个独立的测试用例分别执行，如 `test_valid_user_id[101]`、`test_valid_user_id[102]` 等)
### b.多参数与输入输出预期（最常用）
![[Pasted image 20260823181255.png]]
![[Pasted image 20260823181323.png]]
### c.自定义用例标识（`ids` 参数）
默认情况下，pytest 报告中显示的用例后缀由传入的参数值拼接而成。
![[Pasted image 20260823183439.png]]
使用 `ids` 可以指定清晰易懂的用例别名(优先使用英文)：
```python
@pytest.mark.parametrize(
    "username, password, expected_code",
    [
        ("admin", "123456", 200),
        ("admin", "wrong_pwd", 401),
    ],
    ids=["login_success", "wrong_password"]
)
def test_login(username, password, expected_code):
    assert mock_login(username, password) == expected_code
```
测试中报告会显示：
```python
test_demo.py::test_login[login_success] PASSED
test_demo.py::test_login[wrong_password] PASSED
```
**注意：**
**一一对应**：`ids` 列表的长度必须与传入的数据行数**严格保持一致**（有 2 组数据， `ids` 列表就要包含 2 个字符串元素）。
### d.笛卡尔积组合参数化（多装饰器叠加）
如果在一个测试函数上叠加多个 `@pytest.mark.parametrize`，pytest 会自动对参数集进行**笛卡尔积全排列组合**：
```python
@pytest.mark.parametrize("role", ["admin", "guest"])
@pytest.mark.parametrize("method", ["GET", "POST"])
def test_permission(role, method):
    # 共会生成 2 x 2 = 4 条独立的测试用例：
    # 1. role='admin', method='GET'
    # 2. role='admin', method='POST'
    # 3. role='guest', method='GET'
    # 4. role='guest', method='POST'
    pass
```
### e. 动态参数化（读取外部数据源）
在真实自动化测试中，参数化常与外部数据文件（如 JSON、YAML、Excel 或数据库查询结果）结合使用：
```python
import pytest
import yaml

def load_test_cases():
    with open("test_data.yaml", "r", encoding="utf-8") as f:
        return yaml.safe_load(f)

# 动态载入外部测试数据
@pytest.mark.parametrize("case", load_test_cases())
def test_from_yaml(case):
    res = send_request(case["url"], case["payload"])
    assert res.status_code == case["expected_code"]
```
## 6)fixture
### 什么是fixture?
fixture是 pytest 中最核心、最强大的机制，用于替代传统测试框架中死板的 `setUp` 和 `tearDown`。本质上，Fixture 是一个**利用装饰器 `@pytest.fixture` 声明的函数**，用于为测试用例提供**前置准备、数据注入、后置清理**等依赖项。
### 如何使用fixture?
#### a.作为函数参数直接注入
测试函数只需在形参中写上 fixture 所修饰的函数名，pytest 会自动执行 fixture 并将其返回值传入：
```python
import pytest

@pytest.fixture
def user_token():
    # 前置：生成 Token
    return "token_abc123"

def test_get_user_info(user_token):
    # 直接使用注入进来的 user_token
    assert user_token.startswith("token_")
```
#### b.fixture嵌套
Fixture 本身也可以像测试用例一样接收其他 Fixture。这种模式最适合用于==构建依赖链条==（如：<font color="#e36c09">初始化浏览器 - 打开页面 - 登录并返回页面对象</font>）：
```python
import pytest

@pytest.fixture
def base_user():
    """底层 Fixture：创建测试账号"""
    print("\n[底层前置] 创建基础用户")
    user = {"username": "admin", "role": "superadmin"}
    yield user
    print("\n[底层后置] 删除测试用户")

@pytest.fixture
def auth_header(base_user):
    """上层 Fixture：依赖 base_user 生成请求头"""
    print(f"\n[上层前置] 为用户 {base_user['username']} 生成 Auth Header")
    header = {"Authorization": f"Bearer token_for_{base_user['username']}"}
    yield header
    print("\n[上层后置] 清理请求头缓存")

# 测试用例只请求最顶层的 auth_header 即可
def test_user_api(auth_header):
    print(f"--> [执行用例] 发送接口请求，使用 Headers: {auth_header}")
    assert "Authorization" in auth_header
```
![[Pasted image 20260824151052.png]]
#### c.yield
使用 `yield` 关键字可以将 fixture 一分为二：
- **`yield` 之前**：执行前置操作（如启动浏览器、初始化数据库连接、登录）。
- **`yield` 之后**：执行后置操作（如关闭浏览器、清理测试脏数据），无论测试用例断言成功与否都会执行。
```python
@pytest.fixture  
def db_connection():  
    print("\n[前置] 建立数据库连接...")  
    conn = {"db": "mysql_test", "status": "connected"}  
  
    yield conn  # 将连接对象传递给用例  
  
    print("\n[后置] 回滚事务并关闭连接...")  
  
def test_query(db_connection):  
    print(f"--> 执行查询，当前连接状态: {db_connection['status']}")  
    assert db_connection["status"] == "connected"  
    assert db_connection['db']=='mysql_test'
```
#### d.请求多个fixture
一个测试用例可以**同时请求多个 Fixture**，也可以让 **Fixture 自身再依赖其他 Fixture**。这种设计让复杂的测试环境搭建变得像“搭积木”一样自由灵活。
**前后置执行顺序与栈机制（先进后出 LIFO）相同：**
- 一旦 pytest 确定了 fixture 的线性顺序，它将运行每个 fixture 直到它返回或 yield，然后继续执行列表中的下一个 fixture 做同样的事情。
- 测试完成后，pytest 将逆向遍历 fixture 列表，对于每个 yield 的 fixture，运行 yield 语句之后的代码。

```python
import pytest

@pytest.fixture
def db():
    print("\n[前置 1] 初始化数据库连接...")
    conn = {"db": "mysql_main"}
    yield conn
    print("\n[后置 1] 关闭数据库连接...")

@pytest.fixture
def token():
    print("\n[前置 2] 模拟用户登录，生成 Token...")
    t = "bearer_xyz_789"
    yield t
    print("\n[后置 2] 注销登录 Token...")

# 测试用例同时请求 db 和 token
def test_create_order(db, token):
    print(f"--> [执行用例] 使用 {token} 在数据库 {db['db']} 中创建订单")
    assert True
```
![[Pasted image 20260824150148.png]]
**文件的读写测试：**
```python  
@pytest.fixture  
def file_read():  
    #打开文件  
    print('[前置read]创建读文件句柄')  
    fo=open('test.txt','r',encoding='utf-8')  
    yield fo  
    #关闭文件  
    print('\n[后置read]释放读文件句柄')  
    fo.close()  
  
@pytest.fixture  
def file_write():  
    #打开文件  
    print('\n[前置write]创建写文件句柄')  
    fo=open('test.txt','w',encoding='utf-8')  
    yield fo  
  
#写入文件并读取文件内容  
def test_wrfile(file_write,file_read):  
    file_write.write('pytest框架')  
    print('向文件中写入\n释放写文件句柄')  
    file_write.close()  
    r=file_read.read()  
    print(f'从文件中读取到的字段：{r}')  
    assert r=='pytest框架'
```
![[Pasted image 20260824153703.png]]
#### e.带参数的fixture
```python
pytest.fixture(scope='', params='', autouse='', ids='', name='')
```
##### I. `scope` 参数用于控制 fixture 的作用范围，决定了 fixture 的生命周期。
可选值有：
- `function`（默认）：每个测试函数都会调用一次 fixture。
- `class`：在同一个测试类中共用这个 fixture。
- `module`：在同一个测试模块中共用这个 fixture。（一个文件里）
- `session`：整个测试会话中共用这个 fixture。常结合conftest配置文件使用
<font color="#ff0000">**function:**</font>
```python
@pytest.fixture(scope='function')  
def func01():  
    print('\n初始化')  
    yield  
    print('\n清理')  
  
  
class Test:  
    def test_01_2(self,func01):  
        print('hahaha')  
  
    def test_01_3(self,func01):  
        print('hahaha')  
  
    def test_01_4(self,func01):  
        print('hahaha')
```
![[Pasted image 20260824161553.png]]
<font color="#ff0000">**class:**</font>
```python
@pytest.fixture(scope='class')  
def func01():  
    print('\n初始化')  
    yield  
    print('\n清理')  
  
  
class Test:  
    def test_01_2(self,func01):  
        print('hahaha')  
  
    def test_01_3(self,func01):  
        print('hahaha')  
  
    def test_01_4(self,func01):  
        print('hahaha')
```
![[Pasted image 20260824161804.png]]
**当测试用例放在不同的文件时，需要将fixture放在配置文件里，实现多个文件共享。**
**规则：**
- `conftest.py` 是一个单独存放的夹具配置文件，名称是固定的不能修改
- 你可以在项目中的不同目录下创建多个 `conftest.py` 文件，每个 `conftest.py` 文件都会对其所在<font color="#ff0000">目录及其子目录下的测试模块生效</font>
- 在不同模块的测试中需要用到 `conftest.py` 的前后置功能时，不需要做任何的 import 导入操作
- 作用：可以在不同的 `.py` 文件中使用同一个 `fixture` 函数
**项目结构：**
![[Pasted image 20260824163857.png]]
**conftest.py文件：**
![[Pasted image 20260824163940.png|700]]
**test_01.py:**

![[Pasted image 20260824164017.png]]
**test_02.py:**
![[Pasted image 20260824164029.png]]
**运行结果：**
![[Pasted image 20260824165041.png]]
**当scope== 'session'时，实现全局的前后置**：
![[Pasted image 20260824164326.png]]
**执行结果:**
![[Pasted image 20260824164937.png]]
##### II. `autouse` 参数默认为 `False`。如果设置为 `True`，则每个测试函数都会自动调用该 fixture，无需显式传入
##### III. `params` 参数用于参数化 fixture，支持列表传入。每个参数值都会使 fixture 执行一次，类似于 for 循环
```python
import pytest  
@pytest.fixture(params=['Chrome','firefox','Edge'])  
def brower(request):  
    #通过request.param获取当前值  
    driver_type=request.param  
    print(f'\n初始化{driver_type}浏览器')  
    yield driver_type  
    print(f'\n退出并关闭{driver_type}浏览器')  
  
def test(brower):  
    print(f'在{brower}下执行登录逻辑')  
    assert brower in ['Chrome','firefox','Edge']
```
![[Pasted image 20260824171841.png]]
**执行效果**： 虽然只写了一个 `test`，但 pytest 会自动生成并运行 **3 条独立的测试用例**：
- `test_login_page[Chrome]`
- `test_login_page[Firefox]`
- `test_login_page[Edge]`

#####  IV. `ids` 参数与 `params` 配合使用，为每个参数化实例指定可读的标识符（给参数取名字）在 Fixture 中做参数化时，必须配合内置的 **`request`** 夹具，并通过 **`request.param`** 来获取当前轮次传入的数据项：
```python
@pytest.fixture(  
    params=[{"env": "test", "port": 8080}, {"env": "prod", "port": 443}],  
    ids=["Test_Environment", "Prod_Environment"]  
)  
def env_config(request):  
    return request.param  
  
def test_api_health(env_config):  
    assert env_config["port"] in [8080, 443]
```
![[Pasted image 20260824172306.png]]
`@pytest.mark.parametrize` 实现参数化  VS  `fixture` 实现参数化?
**parametrize 更适合<font color="#ff0000">简单场景</font>，而 fixture 更适合需要<font color="#ff0000">动态数据和资源管理的复杂场景</font>。**

| **维度**     | **Fixture 参数化 (@pytest.fixture(params=...))** | **用例参数化 (@pytest.mark.parametrize)** |
| ---------- | --------------------------------------------- | ------------------------------------ |
| **主要定位**   | **环境/依赖级别**的数据驱动（如多数据库、多浏览器、多环境节点）            | **业务/输入级别**的数据驱动（如多组登录账号密码、表单边界值）    |
| **作用范围**   | **广播生效**：任何依赖该 Fixture 的用例都会被级联放大执行           | **局部生效**：仅对当前装饰的具体测试函数有效             |
| **生命周期支持** | 每次参数切换均会完整执行对应的 `Setup` 和 `Teardown`          | 仅改变输入值，不改变前置后置依赖                     |



##### V. `name` 参数用于为 fixture 显式设置一个名称。如果使用了 `name`，则在测试函数中需要使用这个名称来引用 `fixture`（给 fixture 取名字）
默认情况（使用原函数名引用）:
```python
import pytest

@pytest.fixture
def original_database_connection():
    return "DB_Conn_Instance"

# 必须写长长的函数名
def test_db(original_database_connection):
    assert original_database_connection == "DB_Conn_Instance"
```
使用 `name` 重命名后（使用新别名引用）:
```python
import pytest

# 使用 name 重命名为简洁的 'db'
@pytest.fixture(name="db")
def original_database_connection():
    return "DB_Conn_Instance"

# 测试用例只能使用别名 'db'
def test_db(db):
    assert db == "DB_Conn_Instance"

# 注意：如果此时再用原函数名，pytest 会报错（找不到名为 original_database_connection 的 fixture）
# def test_db_fail(original_database_connection): ...
```
**应用场景：**
1. **简化冗长的内部函数名**：内部实现为了表意清晰可能起名较长（如 `setup_redis_cluster_session`），对外暴露为用例调用时可简化为 `name="redis"`。
2. **重构解耦**：当底层 Fixture 的函数实现重命名时，只要保持 `name="xxx"` 不变，所有调用该 Fixture 的上层测试用例都**无需做任何代码修改**。