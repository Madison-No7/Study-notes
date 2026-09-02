
# 一、* args和** kwargs是什么？
`* args` 和 `** kwargs` 是用于**处理可变数量参数**的语法机制。

| **语法**         | **参数类型**                       | **内部封装的数据结构**   | **调用方式示例**                   |
| -------------- | ------------------------------ | --------------- | ---------------------------- |
| **`*args`**    | **位置参数**（Positional Arguments） | **元组（`tuple`）** | `func(1, 2, 3)`              |
| **`**kwargs`** | **关键字参数**（Keyword Arguments）   | **字典（`dict`）**  | `func(a=1, name="zhangsan")` |
# 二、怎么用？
## 1.函数定义时，作为形参：打包
### 1）``* args``打包位置参数：
将传入的位置参数打包成==元组==。

```
def calculate_sum(*args):
    print(f"args 的类型: {type(args)}, 值为: {args}")
    print(*args)
    return sum(args)

print(calculate_sum(10, 20, 30))
# 输出: args 的类型: <class 'tuple'>, 值为: (10, 20, 30)
# 10 20 30
# 输出: 60
```
### 2）`** kwargs` 打包关键字参数
将传入的参数打包成==字典==。
```python
def show_profile(**kwargs):
    print(f"kwargs 的类型: {type(kwargs)}, 值为: {kwargs}")
    for key, value in kwargs.items():
        print(f"{key} -> {value}")

show_profile(name="张三", age=20, role="admin")
# 输出: kwargs 的类型: <class 'dict'>, 值为: {'name': '张三', 'age': 20, 'role': 'admin'}
```
## 2.函数调用时，作为实参：解包

就是将元组或字典拆散。
### 1）`*` 解包序列（列表、元组等）为独立位置参数
```
nums = [1, 2, 3]
print(*nums)  # 等价于 print(1, 2, 3)
```
### 2）`**` 解包字典为关键字参数
```
def login(user, pwd):
    print(f"登录用户: {user}, 密码: {pwd}")

data = {"user": "admin", "pwd": "123"}
login(**data)  # 等价于 login(user="admin", pwd="123")
```
## 示例：
```
def full_example(a, b=0, *args, city="Beijing", **kwargs):
    print(f"a: {a}, b: {b}")
    print(f"args: {args}")
    print(f"city: {city}")
    print(f"kwargs: {kwargs}")

full_example(1, 2, 3, 4, 5, city="Chengdu", job="Dev", level=3)
# a: 1, b: 2
# args: (3, 4, 5)
# city: Chengdu
# kwargs: {'job': 'Dev', 'level': 3}
```