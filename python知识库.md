# Python 学习知识库（完整版）

> 零基础到进阶的系统化学习手册。共 7 章 + 速查表，建议按顺序推进。

## 总目录

| 章节 | 内容 | 建议时长 |
|---|---|---|
| 00 环境搭建与工具链 | 安装 Python、VS Code、虚拟环境、pip 换源、常见坑 | 1 天 |
| 01 基础语法 | 变量、数据类型、运算符、流程控制、字符串 | 3–5 天 |
| 02 数据结构与函数 | 列表、元组、字典、集合、推导式、函数、类型注解 | 4–6 天 |
| 03 面向对象与高级特性 | 类、继承、魔术方法、dataclass、迭代器、生成器、装饰器 | 5–7 天 |
| 04 文件 I/O、异常与模块 | 读写文件、pathlib、异常体系、模块与包、logging | 3–5 天 |
| 05 常用能力与生态应用 | JSON、正则、日期、命令行、网络、并发、测试 | 5–7 天 |
| 06 学习路线与项目实战 | 项目清单、方向选择（Web/数据/自动化/AI/爬虫）、8 周计划 | 长期 |
| 99 速查表 | 内置函数、常用片段、报错原因对照、Pythonic 原则 | 随时查 |

## 学习方法

1. 每个知识点都亲手敲一遍，看懂不等于会写。
2. 先跑通再理解，能运行、能改出不同结果，就有了直觉。
3. 报错从 Traceback 最后一行读起。
4. 每章做 5–10 个练习，两章后做 1 个综合小项目。
5. 本手册基于 Python 3.11+（3.8 以下无 :=、match）。

---
# 00 · 环境搭建与工具链

## 1. 安装 Python

### Windows
1. 官网 https://www.python.org/downloads/ 下载 3.12.x 安装包。
2. **务必勾选** `Add python.exe to PATH`（最常见的坑）。
3. 安装后验证：

```powershell
python --version      # Python 3.12.x
pip --version
where python          # 查看装了几个 python
```

> Windows 上如果 `python` 命令打开微软商店，去「设置 → 应用 → 应用执行别名」，关掉 `python.exe` / `python3.exe` 的别名。



### Linux（Debian/Ubuntu）
```bash
sudo apt update && sudo apt install python3 python3-pip python3-venv
```

## 2. 编辑器：VS Code

必装扩展：

| 扩展 | 作用 |
|---|---|
| Python（Microsoft） | 语法、调试、语言服务 |
| Pylance | 类型检查、自动补全 |
| Jupyter | 运行 `.ipynb` 和 `# %%` 单元格 |
| Black Formatter | 保存自动格式化 |
| Ruff | 超快的 lint + 格式化 |
| Python Environment Manager | 管理虚拟环境 |

左下角选择解释器 → 指向你的虚拟环境。

推荐 `settings.json`：

```jsonc
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "ms-python.black-formatter",
  "[python]": { "editor.tabSize": 4 },
  "files.autoSave": "afterDelay",
  "python.analysis.typeCheckingMode": "basic"
}
```

## 3. 虚拟环境（重要）

**为什么**：每个项目依赖不同版本的库，全局安装会互相冲突。虚拟环境 = 项目独立的库目录。

### venv（标准库自带，够用）

```bash
# 创建（在项目目录下）
python -m venv .venv

# 激活
source .venv/bin/activate        # Linux/macOS
.venv\Scripts\Activate.ps1       # Windows PowerShell
.venv\Scripts\activate.bat       # Windows CMD

# 退出
deactivate
```

激活后命令行前面会出现 `(.venv)`。此时 `pip install` 只装进这个项目。

### uv（新项目推荐，快 10–100 倍）

```bash
# 安装 uv（Windows）
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"

uv init myproject        # 新建项目
uv venv                  # 建虚拟环境
uv add requests          # 加依赖（自动写 pyproject.toml）
uv run python main.py    # 在环境里运行
uv sync                  # 按锁文件复原环境
```

## 4. pip 使用与国内换源

```bash
pip install requests                 # 装
pip install "requests==2.31.0"       # 装指定版本
pip install -U requests              # 升级
pip uninstall requests               # 卸载
pip list                             # 已装列表
pip show requests                    # 详情
pip freeze > requirements.txt        # 导出依赖
pip install -r requirements.txt      # 导入依赖
```

清华源（加速）：

```bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple requests
```

永久配置：

```bash
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

## 5. 第一个项目结构

```
myproject/
├── .venv/
├── src/
│   └── app/
│       ├── __init__.py
│       ├── main.py
│       └── utils/
│           └── text.py
├── tests/
│   └── test_text.py
├── data/
├── .gitignore
└── README.md
```

`.gitignore` 最小集：

```gitignore
.venv/
__pycache__/
*.pyc
.env
.vscode/settings.json
```

运行方式（别在 `src` 里直接 `python main.py`，会找不到模块）：

```bash
python -m app.main          # 以模块方式运行
```

## 6. 交互式环境

- **REPL**：终端敲 `python` 进入，适合试一行代码，`exit()` 退出。
- **IPython**：`pip install ipython`，补全 + `?` 查文档 + 历史，强烈推荐。
- **Jupyter Notebook**：适合数据分析和写教程，单元格可独立重跑。

## 7. 常见坑速查

| 现象 | 原因 / 解法 |
|---|---|
| `'python' 不是内部或外部命令` | 安装没勾 Add to PATH，重装或手动加 PATH |
| 缩进报 `IndentationError` / `TabError` | 统一 4 空格，VS Code 右下角把 Tab 转 Spaces |
| `ModuleNotFoundError` | 没激活虚拟环境 / 装到别的解释器上了，用 `python -m pip install xxx` |
| 中文乱码 | 文件存 UTF-8；Windows 终端 `chcp 65001` 或用 Windows Terminal |
| 代码在 IDE 能跑，命令行报错 | 工作目录不同，用 `python -m` 方式运行 |
| 装了库还是找不到 | `python -c "import sys; print(sys.executable)"` 看用的是哪个解释器 |

## 8. 本章练习

1. 装好 Python + VS Code，创建 `.venv`，装 `requests`，用 `pip freeze` 导出 `requirements.txt`。
2. 写 `hello.py` 打印当前 Python 版本、系统平台、`sys.path` 的前 3 项。
3. 故意制造一次 `ModuleNotFoundError`，然后修复它（体会虚拟环境的作用）。

---

---
# 01 · Python 基础语法

## 1. 变量与命名

Python 是**动态类型**：变量是名字，值才有类型，赋值即创建。

```python
age = 18            # int
name = "小明"        # str
pi = 3.14           # float
ok = True           # bool
nothing = None      # NoneType
a, b = 1, 2         # 多重赋值
a, b = b, a         # 交换，不需要临时变量
```

命名规范（PEP 8）：

| 对象 | 风格 | 例子 |
|---|---|---|
| 变量 / 函数 | snake_case | `user_name`, `get_total()` |
| 类 | PascalCase | `HttpClient` |
| 常量 | 全大写 | `MAX_RETRY = 3` |
| 私有约定 | 前缀下划线 | `_internal` |

禁止用 `list`、`dict`、`str`、`id`、`type` 等内置名当变量名（会覆盖内置功能）。

注释：

```python
# 单行注释
x = 1  # 行尾注释

"""这是文档字符串 docstring，写在函数/类/模块开头第一行"""
```

## 2. 基本数据类型

```python
# 整数 int：无上限
big = 10 ** 100
hex_n, oct_n, bin_n = 0xFF, 0o17, 0b1011
sep = 1_000_000          # 下划线分隔，可读性

# 浮点数 float：有精度问题！
print(0.1 + 0.2)          # 0.30000000000000004
print(round(2.675, 2))    # 2.67（不是 2.68）
# 精确小数用 decimal
from decimal import Decimal
print(Decimal("0.1") + Decimal("0.2"))   # 0.3

# 复数
z = 3 + 4j

# 布尔：True / False，注意首字母大写
# bool 是 int 的子类：True == 1
```

### 类型查看与转换

```python
type(3)               # <class 'int'>
isinstance(x, (int, float))   # 推荐写法

int("42")             # 字符串 → 整数
int("ff", 16)         # 按 16 进制解析 = 255
float("3.14")
str(3.14)
bool(0)               # False；0 / "" / [] / {} / None 都是 False
int(3.99)             # 截断 → 3（不是四舍五入！）
```

## 3. 数字运算

```python
7 / 2      # 3.5   真除，结果永远是 float
7 // 2     # 3     整除（向下取整）
-7 // 2    # -4    注意是向下，不是截断
7 % 2      # 1     取余
2 ** 10    # 1024  幂
7 % 2 == 1
divmod(7, 2)    # (3, 1)  同时得商和余
abs(-5)     # 5
round(3.14159, 3)  # 3.142
```

运算符优先级（从高到低，常用部分）：

```
**  →  一元 -x  →  * / // %  →  + -  →  比较/in/is  →  not  →  and  →  or
```

不确定就加括号。

## 4. 字符串（重点）

### 创建

```python
s1 = '单引号'
s2 = "双引号"
s3 = f"f-string，可以内嵌表达式：{1+2} {name.upper()}"
s4 = r"原始字符串，\n 不转义"
s5 = """多行
字符串"""
```

> **优先用 f-string**。3.12 起 f-string 里可以写引号嵌套和反斜杠。

### 常用方法

```python
s = "  Hello, Python World  "
s.strip()              # 去两端空白
s.lower() / s.upper()  # 大小写
s.replace("o", "0")
s.split(",")           # 切分 → 列表
",".join(["a", "b"])   # 列表 → 字符串
s.startswith("Hi") / s.endswith("!")
s.find("Python")       # 索引，找不到返回 -1
s.count("l")
s.title()              # 每个单词首字母大写
"{:>10}".format("右对齐")    # 宽度 10 右对齐
"{0:.2f} {1:%}".format(3.14159, 0.5)  # 3.14 50.00000%
```

### 索引与切片

```python
s = "abcdefgh"
s[0]      # 'a'
s[-1]     # 'h'      负索引从末尾
s[2:5]    # 'cde'    左闭右开
s[:3]     # 'abc'
s[3:]     # 'defgh'
s[::2]    # 'aceg'   步长 2
s[::-1]   # 'hgfedcba'  反转，经典写法
len(s)    # 8
```

切片公式：`s[start:stop:step]`，越界不报错（返回能取到的部分），索引越界才报错。

字符串**不可变**：`s[0] = "X"` 报错；要改就转 `list` 再 `join`。

## 5. 输入输出

```python
name = input("请输入名字：")   # 永远是 str！要数字自己转
age = int(input("年龄："))
print("结果", 1 + 1, sep="-", end="\n")   # sep 分隔符, end 结尾
```

## 6. 流程控制

### if / elif / else

```python
score = 85
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
else:
    grade = "C"

# 三元表达式
status = "成年" if age >= 18 else "未成年"
```

### for 循环

```python
for i in range(5):        # 0 1 2 3 4
for i in range(1, 10, 2): # 1 3 5 7 9
for i in range(10, 0, -1):# 倒数

for x in [10, 20, 30]:
    print(x)

for k, v in {"a": 1, "b": 2}.items():
    print(k, v)

for i, v in enumerate(["a", "b", "c"], start=1):   # 带序号
    print(i, v)

for a, b in zip([1, 2], ["x", "y"]):               # 并行遍历，短的对齐长
    print(a, b)
```

### while 循环

```python
n = 0
while n < 5:
    n += 1        # 别忘了推进条件，否则死循环

while True:
    cmd = input("> ")
    if cmd == "q":
        break     # break 跳出 / continue 跳过本次 / else 正常跑完才执行
```

### match（3.10+，结构化匹配）

```python
match command.split():
    case ["quit"]:
        break
    case ["go", direction]:
        move(direction)
    case ["pick", *items]:
        pick(items)
    case _:                 # 默认分支
        print("不懂")
```

## 7. 逻辑与真值

```python
x > 0 and x < 10       # 与
x < 0 or x > 100       # 或
not flag               # 非

# Python 可直接连写
0 < x < 10

# 短路求值：常用默认值写法
name = input_name or "匿名用户"

# and/or 返回的是操作数本身，不是 bool
print("" or "默认")     # "默认"
```

真值判断为 False 的值：`False, None, 0, 0.0, "", [], {}, set(), ()`。

判断空列表用 `if not items:`，比 `if len(items) == 0:` 更 Pythonic。

## 8. 本章易错点清单

| 错 | 对 |
|---|---|
| `if x = 5:` | `if x == 5:` |
| `input()` 结果当数字用 | `int(input())` |
| `and/or` 当其他语言的 `&&` 做条件嵌套混乱 | 用多个 `if` 或括号理清 |
| `0.1+0.2 == 0.3` 为 True | 浮点比较用 `math.isclose(a, b)` 或 `Decimal` |
| `for i in range(len(lst))` | `for item in lst:` 或 `enumerate` |
| 用 `==` 比较 `None` | 用 `is None` |
| 中文标点写进代码（全角括号、引号） | 代码里只用半角符号 |

## 9. 练习

1. BMI 计算器：输入身高体重，输出 BMI 和分级。
2. 猜数字：程序随机 1–100，循环提示「大了/小了」，统计次数。
3. 字符串处理：输入一句英文，统计每个单词长度并输出最长单词。
4. 九九乘法表（双重 for，用 f-string 对齐）。
5. 温度转换：输入 `32C` 或 `90F`，自动识别单位并转换（练习切片 + `in`）。

---


---
# 02 · 数据结构与函数

## 1. 四大容器一览

| 类型 | 字面量 | 有序 | 可变 | 重复 | 典型用途 |
|---|---|---|---|---|---|
| list | `[1,2,3]` | ✅ | ✅ | ✅ | 一组同类数据 |
| tuple | `(1,2,3)` | ✅ | ❌ | ✅ | 固定记录、函数多返回值 |
| dict | `{"a":1}` | ✅(插入序) | ✅ | 键唯一 | 键值映射、对象数据 |
| set | `{1,2,3}` | ❌ | ✅ | ❌ | 去重、成员判断、交并差 |

选择原则：默认用 `list`；需要不可变/当字典的键用 `tuple`；要按名字取值用 `dict`；要去重或频繁判断「在不在」用 `set`。

## 2. 列表 list

```python
nums = [3, 1, 4, 1, 5]

# 增删改查
nums.append(9)          # 尾部加
nums.insert(0, 99)      # 指定位置插
nums.extend([7, 8])     # 合并另一个列表
last = nums.pop()       # 删除并返回（默认尾部）
nums.remove(1)          # 删除第一个值为 1 的
del nums[0]
nums[0] = 100           # 改
1 in nums               # 查（O(n)，大列表用 set）
len(nums), max(nums), min(nums), sum(nums)
sorted(nums)            # 返回新列表，不动原表
nums.sort(reverse=True) # 原地排序
nums.clear()
```

### 切片与拷贝

```python
a = [1, 2, 3, 4, 5]
b = a            # ❌ 不是拷贝，是同一个对象！
c = a[:]         # ✅ 浅拷贝
d = list(a)      # ✅ 浅拷贝
import copy; e = copy.deepcopy(a)   # 深拷贝（嵌套时用）

a is b           # True
```

这是新手最容易踩的坑：函数里改了传入的 list，外面也变了。

### 排序的 key

```python
users = [{"n": "Bob", "age": 20}, {"n": "Amy", "age": 18}]
users.sort(key=lambda u: u["age"])
users.sort(key=lambda u: (-u["age"], u["n"]))     # 多级排序
sorted(users, key=lambda u: u["n"], reverse=True)
```

## 3. 元组 tuple

```python
point = (3, 4)
single = (5,)        # 单元素必须加逗号，否则 (5) 就是整数 5
x, y = point         # 解包
first, *rest = [1, 2, 3, 4]   # * 收集剩余 → rest = [2,3,4]

def divmod_result(n, d):
    return n // d, n % d      # 本质是返回元组
q, r = divmod_result(17, 5)
```

用途：函数返回多个值、字典的键、`namedtuple` / `dataclass` 的轻量替代。

## 4. 字典 dict

```python
user = {"name": "小明", "age": 18}

user["name"]              # 取值，不存在 → KeyError
user.get("email")         # 安全取值 → None
user.get("email", "未填") # 带默认值
user["email"] = "a@b.c"   # 新增/修改
user.setdefault("vip", False)   # 不存在才设默认值
del user["age"]
user.pop("age", None)     # 安全删除

user.keys() / user.values() / user.items()
```

### 遍历

```python
for k, v in user.items():
    print(k, v)
for k in user:            # 默认遍历键
    ...
```

### 实用技巧

```python
# 统计计数
from collections import Counter
Counter("abracadabra")            # {'a':5, 'b':2, 'r':2, ...}
Counter(words).most_common(3)     # 词频 Top3

# 默认容器
from collections import defaultdict
groups = defaultdict(list)
for name, dept in pairs:
    groups[dept].append(name)     # 不用先判断键存不存在

# 合并字典
merged = {**a, **b}           # b 覆盖 a
a |= b                        # 3.9+ 原地更新
```

## 5. 集合 set

```python
s = {1, 2, 3}
s.add(4)
s.discard(99)        # 不存在不报错（remove 会报错）
set([1, 2, 2, 3])    # 去重 → {1,2,3}

a, b = {1, 2, 3}, {3, 4}
a & b    # 交集 {3}
a | b    # 并集
a - b    # 差集 {1,2}
a ^ b    # 对称差 {1,2,4}
a <= b   # 子集
3 in a   # O(1)，比 list 快得多
```

空集合必须 `set()`，`{}` 是空字典。

## 6. 推导式（Comprehension）

```python
squares = [x * x for x in range(10)]
evens = [x for x in range(20) if x % 2 == 0]
matrix = [[0] * 3 for _ in range(3)]

names_lower = {n.lower(): n for n in names}          # 字典推导
uniq = {len(w) for w in words}                        # 集合推导
total = sum(x * x for x in range(10))                 # 生成器表达式，不建列表

# 嵌套展开
flat = [x for row in matrix for x in row]
```

写法规则：`[表达式 for 变量 in 可迭代对象 if 条件]`。
如果推导式套了三层以上、或表达式很长，老老实实写 `for` 循环，可读性优先。

## 7. 函数基础

```python
def greet(name, greeting="你好", *args, title=None, **kwargs):
    """文档字符串：这是 greet 函数。"""
    return f"{greeting}, {name}!"
```

参数顺序：`位置参数 → 默认参数 → *args → 关键字专用(keyword-only) → **kwargs`

```python
def f(a, b=2, /, c=3, *, d=4):
    ...
# / 之前只能位置传参；* 之后只能 key=value 传参

f(1, 2, c=5, d=6)
```

### 多返回值与解包

```python
def stats(nums):
    return min(nums), max(nums), sum(nums) / len(nums)
lo, hi, avg = stats([1, 2, 3])
```

### 可变默认参数的经典陷阱

```python
def bad(item, box=[]):      # ❌ 默认值在函数定义时创建一次，所有调用共享
    box.append(item)
    return box
bad(1); bad(2)              # [1, 2]  ← 意外

def good(item, box=None):   # ✅
    if box is None:
        box = []
    box.append(item)
    return box
```

### 传参机制：一切皆对象引用

```python
def modify(lst, num):
    lst.append(99)     # 影响外部（可变对象原地修改）
    num += 1           # 不影响外部（整数不可变，重新绑定）
```

不想影响外部：函数内部 `lst = lst[:]` 再改，或明确写在文档里「会原地修改」。

## 8. 作用域与 lambda

```python
count = 0
def inc():
    global count       # 要用 global 才能改全局变量（尽量别用）
    count += 1

def make_counter():
    n = 0
    def inc():
        nonlocal n     # 改外层函数变量 → 闭包
        n += 1
    return inc

# lambda：只能一个表达式，适合当 key/callback
sorted(words, key=lambda w: len(w))
add = lambda a, b: a + b     # 能写成 def 就别用 lambda
```

LEGB 查找顺序：**L**ocal → **E**nclosing → **G**lobal → **B**uiltin。

## 9. 类型注解（Type Hints）

```python
def area(width: float, height: float) -> float:
    return width * height

name: str = "x"
nums: list[int] = [1, 2]
user: dict[str, int] = {"a": 1}
maybe: str | None = None          # 3.10+
Pair = tuple[int, str]

from typing import Literal, Optional
def mode(x: Literal["r", "w"]) -> None: ...
```

注解**不影响运行**，但让 IDE 补全、Ruff/mypy 检查提前发现错误。新项目建议从第一天就写。

## 10. 练习

1. 学生成绩管理：用 `dict[str, list[int]]` 存成绩，求每人平均分、全班最高分对应的科目。
2. 词频统计：读一段文本，输出出现最多的 10 个词及次数（`Counter`）。
3. 手写 `chunk(lst, n)`：把列表按 n 个一组切分，`chunk([1,2,3,4,5], 2) → [[1,2],[3,4],[5]]`。
4. 用推导式实现：去掉列表中所有空字符串与 None。
5. 复现「可变默认参数」陷阱，打印出证据，然后修复它。
6. 两个列表求交集：分别用 `set`、推导式、`filter` 三种写法，比较速度。

---


---
# 03 · 面向对象与高级特性

## 1. 类与实例

```python
class Dog:
    """一个最简单的类。"""

    species = "Canis familiaris"      # 类属性：所有实例共享

    def __init__(self, name, age):    # 初始化（构造函数）
        self.name = name              # 实例属性
        self.age = age

    def bark(self) -> str:            # 实例方法，第一个参数必须是 self
        return f"{self.name}说：汪！"

d = Dog("旺财", 3)          # 实例化不需要 new 关键字
print(d.bark(), d.species)
```

核心心智模型：**方法里的 `self` 就是「当前这个对象」**，Python 要求你显式写出来。

### 封装：属性与私有约定

```python
class Account:
    def __init__(self, owner, balance=0):
        self.owner = owner
        self._balance = balance       # 单下划线：内部使用，靠自觉

    @property                        # 把方法伪装成属性（只读/带计算）
    def balance(self):
        return self._balance

    @balance.setter
    def balance(self, value):
        if value < 0:
            raise ValueError("余额不能为负")
        self._balance = value

    def deposit(self, amount):
        self._balance += amount
```

`__balance`（双下划线）是**名称改写**（name mangling → `_Account__balance`），不是真私有，只是防止子类意外覆盖。Python 哲学：*We're all consenting adults*，用 `_` 约定 + `property` 校验即可。

## 2. 继承与多态

```python
class Animal:
    def __init__(self, name):
        self.name = name
    def speak(self):                  # 基类定义接口
        raise NotImplementedError

class Cat(Animal):
    def speak(self):
        return f"{self.name}: 喵"

class Kitten(Cat):
    def __init__(self, name, playful=True):
        super().__init__(name)        # 调用父类构造，别写 Cat.__init__(self, name)
        self.playful = playful

for a in [Cat("咪咪"), Kitten("小橘")]:
    print(a.speak())                  # 多态：同一调用，不同行为

isinstance(Kitten("x"), Animal)       # True，子类也是父类的实例
```

多继承与 MRO（方法解析顺序）：

```python
class A: pass
class B(A): pass
class C(A): pass
class D(B, C): pass
D.__mro__     # (D, B, C, A, object)  C3 线性化
```

实践中**尽量避免复杂多继承**，用 Mixin（只加方法不定属性）或组合。

> 优先「组合而非继承」：`class Engine: ...` + `class Car: def __init__(self): self.engine = Engine()`。

## 3. 魔术方法（Dunder）

```python
class Vector:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __repr__(self):               # 开发者视角，尽量能重建对象
        return f"Vector({self.x}, {self.y})"

    def __str__(self):                # 用户视角
        return f"({self.x}, {self.y})"

    def __add__(self, other):         # +
        return Vector(self.x + other.x, self.y + other.y)

    def __eq__(self, other):          # ==（同时定义要 __hash__）
        return (self.x, self.y) == (other.x, other.y)

    def __hash__(self):
        return hash((self.x, self.y))

    def __len__(self):                # len()
        return 1

    def __getitem__(self, i):         # v[0]
        return (self.x, self.y)[i]

    def __iter__(self):               # for x in v
        return iter((self.x, self.y))

    def __call__(self):               # v() 像函数一样调用
        return (self.x, self.y)

    def __bool__(self):               # if v:
        return self.x != 0 or self.y != 0
```

| 类别 | 常用 |
|---|---|
| 构造/销毁 | `__init__` `__new__` `__del__` |
| 表示 | `__str__` `__repr__` `__format__` |
| 运算 | `__add__` `__sub__` `__mul__` `__radd__` `__iadd__` |
| 比较 | `__eq__` `__lt__` `__hash__` |
| 容器 | `__len__` `__getitem__` `__setitem__` `__contains__` `__iter__` |
| 上下文 | `__enter__` `__exit__` |
| 描述符 | `__get__` `__set__` |

## 4. dataclass：少写 80% 样板

```python
from dataclasses import dataclass, field

@dataclass(order=True)              # order=True 自动生成 <, >
class Person:
    name: str
    age: int
    email: str = ""
    tags: list[str] = field(default_factory=list)   # 可变默认值必须这样写

p = Person("小明", 18)
print(p)                 # Person(name='小明', age=18, email='', tags=[])
p == Person("小明", 18)  # True，自动 __eq__
```

需要不可变：`@dataclass(frozen=True)`；需要 slots 省内存：`@dataclass(slots=True)`。

替代方案：`NamedTuple`（不可变、可解包）、`pydantic.BaseModel`（带校验，Web 开发常用）。

## 5. 迭代器协议

```python
class CountDown:
    def __init__(self, n):
        self.n = n
    def __iter__(self):               # 可迭代对象 → 返回迭代器
        return self
    def __next__(self):               # 无值时抛 StopIteration
        if self.n <= 0:
            raise StopIteration
        self.n -= 1
        return self.n + 1

for x in CountDown(3):
    print(x)            # 3 2 1

it = iter([1, 2])       # iter() 拿迭代器
next(it)                # 1
next(it, None)          # 耗尽时给默认值，不抛异常
```

`for` 循环的本质：`it = iter(obj)` → 反复 `next(it)` → 捕获 `StopIteration` 结束。
可迭代 = 实现了 `__iter__` 或 `__getitem__`；迭代器 = 实现了 `__next__` 且 `__iter__` 返回自身。

## 6. 生成器（Generator）

**yield 版本，函数变成惰性数据流：**

```python
def fib():
    a, b = 0, 1
    while True:                 # 无限序列，内存占用 O(1)
        yield a
        a, b = b, a + b

g = fib()
[next(g) for _ in range(10)]    # 取前 10 个

def read_lines(path):           # 大文件逐行处理，不一次读入
    with open(path, encoding="utf-8") as f:
        for line in f:
            yield line.rstrip("\n")
```

执行特点：调用生成器函数**不会执行任何代码**，返回生成器对象；每次 `next()` 运行到下一个 `yield` 并暂停，保留全部局部状态。

### 生成器表达式 vs 列表推导

```python
sum([x*x for x in range(10**7)])   # ❌ 先建 700MB 列表
sum(x*x for x in range(10**7))     # ✅ 边算边丢，O(1) 内存
```

### 实用工具

```python
from itertools import count, cycle, islice, chain, groupby, product, permutations, combinations

list(islice(count(10), 5))          # [10,11,12,13,14]
list(chain([1,2],[3,4]))            # 拼接多个可迭代
list(combinations("ABC", 2))        # ('A','B') ('A','C') ('B','C')
list(product([1,2], "ab"))          # 笛卡尔积
# groupby 必须先排序！
for k, grp in groupby(sorted(data, key=lambda x: x["type"]), key=lambda x: x["type"]):
    ...
```

## 7. 装饰器（Decorator）

本质：**接收函数、返回新函数的函数**，是「不改原函数代码就加功能」的语法糖。

```python
import time
from functools import wraps

def timing(func):
    @wraps(func)                      # 保留原函数的 __name__/__doc__
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = func(*args, **kwargs)
        print(f"{func.__name__} 用了 {time.perf_counter()-start:.4f}s")
        return result
    return wrapper

@timing                              # 等价于 slow = timing(slow)
def slow():
    time.sleep(1)
```

### 带参数的装饰器（三层）

```python
def retry(times=3, exceptions=(Exception,)):
    def deco(func):
        @wraps(func)
        def wrapper(*a, **kw):
            for i in range(times):
                try:
                    return func(*a, **kw)
                except exceptions:
                    if i == times - 1:
                        raise
                    time.sleep(0.5 * (i + 1))
        return wrapper
    return deco

@retry(times=3, exceptions=(ConnectionError,))
def fetch(url): ...
```

### 标准库装饰器必须会

```python
@property                      # 方法 → 只读属性
@staticmethod                  # 不需要 self 的工具方法
@classmethod                   # cls 参数，常用于工厂方法
@functools.cache               # 缓存（3.9+）；@lru_cache(maxsize=128) 可淘汰
@functools.total_ordering      # 只写 __eq__ 和 __lt__ 就补齐其他比较
@contextlib.contextmanager     # 生成器 → 上下文管理器
```

类装饰器与 `__call__` 装饰器也常用（如 `@dataclass`）。多个装饰器**从下往上应用**：

```python
@a
@b
def f(): ...     # 等价 f = a(b(f))，运行时 a 的 wrapper 在外层
```

## 8. 异常与上下文（承上启下）

```python
class BankError(Exception):            # 自定义异常基类
    pass
class InsufficientFunds(BankError):
    def __init__(self, need, have):
        super().__init__(f"余额不足：需要 {need}，只有 {have}")
        self.need, self.have = need, have
```

异常处理的完整写法见第 04 章。

## 9. 本章练习

1. 实现 `Matrix` 类：支持 `+`、`*`（数乘与矩阵乘）、`==`、`__str__` 打印成表格。
2. 用 `dataclass` 重写学生成绩管理，加 `average` 的 `@property`。
3. 写生成器 `flatten(nested)`，把任意深度嵌套列表拉平。
4. 写 `@cache`（手写版，不用 lru_cache）给递归 `fib` 加速，对比耗时。
5. 写 `@require_type(int)` 装饰器，参数类型不符抛 `TypeError`。
6. 用 `contextmanager` 实现 `@contextlib.suppress` 的简易版和「计时块」`with timer("加载"): ...`。

---


---
# 04 · 文件 I/O、异常与模块

## 1. 读写文件

```python
# 文本：永远显式指定 encoding！
with open("notes.txt", "r", encoding="utf-8") as f:
    content = f.read()              # 一次读全部（小文件）

with open("notes.txt", "r", encoding="utf-8") as f:
    for line in f:                  # ✅ 大文件首选：逐行迭代，省内存
        print(line.rstrip())

with open("out.txt", "w", encoding="utf-8") as f:   # w 会清空原文件！
    f.write("第一行\n")
    f.writelines(["a\n", "b\n"])    # writelines 不加换行，自己写 \n

with open("log.txt", "a", encoding="utf-8") as f:   # a 追加
    f.write("新记录\n")
```

### 打开模式

| 模式 | 含义 | 文件不存在 | 文件存在 |
|---|---|---|---|
| `r` | 只读（默认） | 报错 | 从头读 |
| `w` | 只写 | 创建 | **清空** |
| `a` | 追加写 | 创建 | 追加到末尾 |
| `x` | 独占创建 | 创建 | **报错**（防覆盖） |
| `rb` / `wb` | 二进制读写 | 见下 | 图片、zip、序列化数据 |
| `r+` | 读写 | 报错 | 位置指针控制 |

```python
# 二进制：复制图片
with open("a.png", "rb") as src, open("b.png", "wb") as dst:
    for chunk in iter(lambda: src.read(1024 * 1024), b""):
        dst.write(chunk)
```

`newline=""` 处理 CSV；`os.fsync` / `f.flush()` 强制落盘（日志实时可见）。

### pathlib（现代写法，推荐）

```python
from pathlib import Path

p = Path("data") / "notes.txt"       # 用 / 拼路径，跨平台
p.exists() / p.is_file() / p.is_dir()
p.mkdir(parents=True, exist_ok=True) # 建目录
p.read_text(encoding="utf-8")        # 一行读完
p.write_text("hello", encoding="utf-8")
p.write_bytes(b"\x00\x01")
p.name / p.stem / p.suffix / p.parent   # 文件名 / 无后缀名 / .txt / 父目录
p.with_suffix(".md")

list(p.glob("**/*.py"))              # 递归找文件（** 通配）
sorted(Path(".").rglob("*.log"))
p.resolve() / p.relative_to(root)   # 绝对路径 / 相对路径
```

## 2. 异常处理

### 基本结构

```python
try:
    n = int(input("输入数字："))
    result = 10 / n
except ValueError:                        # 捕获特定类型
    print("不是合法整数")
except ZeroDivisionError as e:
    print(f"不能除以零：{e}")
except (TypeError, IndexError) as e:      # 多类型
    print("其他错误", e)
except Exception as e:                    # 兜底（不要写裸 except:）
    print("未知错误", type(e).__name__, e)
else:
    print("没出错才执行这里")              # 常用于「读完了再处理」
finally:
    print("无论如何都执行")               # 常用来关连接、释放资源
```

### 抛出与重新抛出

```python
def set_age(age):
    if not isinstance(age, int):
        raise TypeError(f"age 必须是 int，收到 {type(age).__name__}")
    if age < 0:
        raise ValueError("age 不能为负")

try:
    risky()
except KeyError as e:
    raise RuntimeError("缺少必要字段") from e     # 保留原始 traceback 链
```

### 最佳实践（重要）

| 该做 | 不该做 |
|---|---|
| 捕获具体异常类型 | `except: pass`（吞掉所有错误，调试地狱） |
| 范围尽量小，只包住可能出错的那几行 | 整个函数包一个大 try |
| 用 `with` 管理资源 | 手动 `f.close()` + try/finally |
| EAFP：先做，出错再处理（Python 风格） | 到处 `if os.path.exists()` 再读（还有竞态） |
| 业务错误自定义异常类 | 用返回值 `-1`/`None` 表示错误 |

```python
# EAFP 示例
try:
    value = d["key"]
except KeyError:
    value = default

# with 的底层就是 try/finally
with open(path, encoding="utf-8") as f:      # 即使中途抛异常也会关闭
    data = f.read()
```

`warnings.warn("旧接口", DeprecationWarning, stacklevel=2)` 比 print 提示更专业。

## 3. 模块与包

```python
import math                     # 全名调用 math.sqrt(2)
import numpy as np              # 约定别名
from os.path import join        # 直接引入函数名
from typing import *            # ❌ 别用 * ，命名空间污染

math.pi, math.sqrt(2), math.ceil(1.2)
dir(math)                       # 看模块里有什么
help(math.lcm)                  # 看文档
```

### 关键机制

```python
# 1. 搜索顺序：内置 → sys.path（当前目录、PYTHONPATH、site-packages）
import sys; print(sys.path)

# 2. 模块只执行一次，之后从 sys.modules 缓存取

# 3. __name__ == "__main__"：既当库又当脚本
def main():
    ...
if __name__ == "__main__":      # 只有直接运行本文件才执行
    main()
```

### 包的目录结构

```
mypkg/
├── __init__.py          # 标记为包；可在此 re-export 常用名字
├── core.py
└── io/
    ├── __init__.py
    └── loader.py
```

```python
# mypkg/__init__.py
from .core import parse          # 相对导入（点号 = 当前包）
__all__ = ["parse"]              # 控制 from mypkg import * 暴露什么
```

使用：`from mypkg.io.loader import load`。

> 相对导入只能在包内用，不能在直接运行的脚本顶层用（`ImportError: attempted relative import`）。入口文件用绝对导入。

### 循环导入解法

1. 重新划分职责，把公共部分提到第三个模块（最好）。
2. 把 import 移到函数内部（延迟导入）。
3. 只 `import module` 不 `from module import name`，靠属性访问延迟绑定。
4. 类型注解用 `from __future__ import annotations` 或字符串前向引用。

## 4. 标准库精选（先记名字，用到再查）

| 模块 | 干什么 |
|---|---|
| `os` / `sys` / `pathlib` | 系统、路径、进程信息 |
| `json` / `csv` / `sqlite3` / `configparser` | 数据存取 |
| `re` | 正则 |
| `datetime` / `time` / `calendar` | 时间 |
| `collections` / `itertools` / `functools` | 数据结构与函数工具 |
| `random` / `secrets` | 随机（安全场景用 secrets） |
| `hashlib` / `base64` / `hmac` | 摘要与编码 |
| `logging` | 日志 |
| `argparse` / `click`(三方) | 命令行 |
| `unittest` / `pytest`(三方) / `doctest` | 测试 |
| `typing` / `dataclasses` / `enum` / `abc` | 类型与抽象 |
| `subprocess` / `shutil` / `tempfile` / `glob` | 调外部程序与文件批处理 |
| `concurrent.futures` / `threading` / `asyncio` | 并发 |
| `pickle` / `copy` / `zipfile` / `tarfile` | 序列化与归档（pickle 别处理不可信数据） |

```python
# enum 替代魔法数字
from enum import Enum, auto
class Color(Enum):
    RED = 1
    GREEN = auto()
Color.RED.name, Color.RED.value

# abc 抽象基类：强制子类实现
from abc import ABC, abstractmethod
class Shape(ABC):
    @abstractmethod
    def area(self): ...
```

## 5. 日志（生产代码必备）

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(name)s:%(lineno)d - %(message)s",
    handlers=[logging.StreamHandler(),
              logging.FileHandler("app.log", encoding="utf-8")],
)
log = logging.getLogger(__name__)

log.debug("细节")          # DEBUG < INFO < WARNING < ERROR < CRITICAL
log.info("用户 %s 登录", user)     # ✅ 用 %s 惰性格式化，别用 f-string
try:
    risky()
except Exception:
    log.exception("炸了")  # 自动带上 traceback
```

不要用 `print` 做真正的日志：无法分级、无法重定向、没有时间戳、无法关闭。

## 6. 本章练习

1. 写 `count_words(path)`：统计文件词频并输出 Top20，文件不存在时给出友好提示。
2. 用 `pathlib` 写一个目录整理脚本：把指定目录里的文件按扩展名分到子文件夹。
3. 实现简易配置读取：`x.ini` 用 `configparser`，`config.json` 用 `json`，统一成 dict 接口。
4. 设计一个异常体系：`AppError` → `ValidationError` / `NotFoundError`，在函数中正确抛出与捕获。
5. 把自己写的工具函数打包成 `mypkg`，在另一个脚本里 `import` 使用，并写 `if __name__ == "__main__"` 入口。
6. 给前几章任意程序加上 `logging`，替换所有 `print`。

---


---
# 05 · 常用能力与生态应用

## 1. JSON

```python
import json

data = {"name": "小明", "tags": ["a", "b"], "age": 18}

s = json.dumps(data, ensure_ascii=False, indent=2, sort_keys=True)   # dict → 字符串
# ensure_ascii=False 才能正常输出中文，几乎必写
obj = json.loads(s)                                                  # 字符串 → dict

with open("data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False, indent=2)                 # 直接写文件
with open("data.json", encoding="utf-8") as f:
    data = json.load(f)

# 自定义对象序列化
from dataclasses import asdict
json.dumps(asdict(person), ensure_ascii=False)

class PersonEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, set):
            return list(o)
        return super().default(o)
json.dumps({"s": {1, 2}}, cls=PersonEncoder)
```

对照表：`dict↔object`、`list↔array`、`str↔string`、`int/float↔number`、`True↔true`、`None↔null`。
JSON 没有日期类型，用 ISO 字符串（`datetime.isoformat()`）。

## 2. 正则表达式 re

```python
import re

re.search(r"\d+", text)          # 找第一个，返回 Match 或 None
re.findall(r"\d+", text)         # 找全部 → 列表
re.finditer(r"\d+", text)        # 找全部 → 迭代器（有位置信息）
re.match(r"^abc", text)          # 只从开头匹配
re.fullmatch(r"\d{11}", text)    # 整串必须匹配（校验首选）
re.sub(r"\s+", " ", text)        # 替换
re.split(r"[,;，；]", line)       # 按多种分隔符切
re.compile(r"\d+").search(text)  # 重复使用就预编译
```

### Match 对象

```python
m = re.search(r"(?P<ymd>\d{4})-(\d{2})-(\d{2})", s)
if m:
    m.group(0)          # 整个匹配
    m.group(1)          # 第 1 个捕获组
    m["ymd"]            # 命名组（推荐，可读性好）
    m.groups()          # 所有组
    m.start(), m.span() # 位置
```

### 语法速记

| 写法 | 含义 |
|---|---|
| `\d \w \s` | 数字 / 单词字符 / 空白 |
| `\D \W \S` | 取反 |
| `.` `^` `$` | 任意字符（非换行）/ 开头 / 结尾 |
| `[]` `[^]` | 字符类 / 取反，`[.-]` 里的 `-` 放两端 |
| `* + ? {3,5}` | 0+ / 1+ / 0-1 / 次数 |
| `a|b` `(abc)+` | 或 / 分组 |
| `(?P<name>...)` | 命名组 |
| `(?:...)` | 只分组不捕获 |
| `(?=...)` `(?!...)` | 前瞻（断言，不消耗字符） |
| `(?<=...)` `(?<!...)` | 后顾 |
| 贪婪 vs 惰性 | `.*` 尽量多 / `.*?` 尽量少 |

```python
re.findall(r"\d", "12a34", flags=re.I)        # 忽略大小写
re.findall(r"a", s, flags=re.M)               # ^$ 按行
re.X                                    #  verbose：允许注释和空白，复杂正则必备
re.sub(r"(\w+)@(\w+)", r"\2/\1", s)           # 反向引用 \1（字符串里注意 r""）
```

> 提示：写正则时先想清楚「要匹配什么」，从最简单开始逐步加，用 https://regex101.com 验证。
> 简单包含判断（`"abc" in s`）不要用正则。

## 3. 日期时间

```python
from datetime import datetime, date, time, timedelta, timezone

now = datetime.now()                       # 本地时间
utc = datetime.now(timezone.utc)           # ✅ 带时区的 UTC（推荐存这个）
d = date(2026, 11, 4)

# 解析与格式化
s = "2026-11-04 12:08:00"
dt = datetime.strptime(s, "%Y-%m-%d %H:%M:%S")   # 字符串 → datetime
out = dt.strftime("%Y年%m月%d日 %A")              # datetime → 字符串
dt.isoformat()                                    # 标准格式
datetime.fromisoformat("2026-11-04T12:08:00+08:00")
```

格式符：`%Y 四位年 %m 月 %d 日 %H 24时 %M 分 %S 秒 %f 微秒 %j 年内第几天 %A 星期全名 %b 月份缩写 %z 时区偏移`

```python
# 时间运算
later = now + timedelta(days=7, hours=3)
delta = now - datetime(2026, 1, 1)
delta.total_seconds()             # ✅ 要秒数用这个，别用 .seconds（会丢天数）

# 时间戳
ts = now.timestamp()
datetime.fromtimestamp(ts, tz=timezone.utc)

# 计时
import time
start = time.perf_counter()       # 测耗时首选
time.sleep(1)                     # 挂起
time.monotonic()                  # 单调时钟

# 时区（3.9+ 标准库）
from zoneinfo import ZoneInfo
beijing = datetime.now(ZoneInfo("Asia/Shanghai"))
```

> 新手常见错误：`now.strftime("%Y-%m-%d")` 与 `strptime` 搞混（**p** = **p**arse 解析，**f** = **f**ormat 格式化）。

## 4. 命令行参数 argparse

```python
# cli.py
import argparse
from pathlib import Path

p = argparse.ArgumentParser(description="文件统计工具")
p.add_argument("path", type=Path, help="要统计的目录")            # 位置参数
p.add_argument("-n", "--top", type=int, default=10, help="显示前 N")
p.add_argument("-v", "--verbose", action="store_true")
p.add_argument("--ext", nargs="*", default=["py"], help="扩展名，可多个")
p.add_argument("--format", choices=["text", "json"], default="text")
args = p.parse_args()

print(args.path, args.top, args.verbose)
```

运行：`python cli.py ./src -n 5 --verbose --ext py md --format json`
自动获得 `--help`、类型转换、报错提示。复杂 CLI 可转 `typer` / `click`。

## 5. 网络请求

```python
import requests

r = requests.get(
    "https://api.github.com/repos/python/cpython",
    params={"per_page": 5},
    headers={"User-Agent": "my-app"},
    timeout=10,                       # ✅ 永远设 timeout，否则可能永久卡住
)
r.raise_for_status()                  # 4xx/5xx 抛异常（否则 r.json() 可能拿到错误体）
data = r.json()
print(r.status_code, r.headers["Content-Type"])

requests.post(url, json={"a": 1})     # JSON 体
requests.post(url, data={"a": 1})     # 表单
requests.get(url, stream=True)        # 大文件流式下载
with open("file", "wb") as f:
    for chunk in r.iter_content(8192):
        f.write(chunk)
```

现代异步方案：`httpx`（API 类似 requests，支持 async + HTTP/2）。

## 6. 并发：先搞清楚该用哪个

| 任务类型 | 选择 | 说明 |
|---|---|---|
| CPU 密集（计算、图像、解析大量数据） | `multiprocessing` / `ProcessPoolExecutor` | 绕开 GIL，多进程 |
| I/O 密集、数量不大 | `ThreadPoolExecutor` | 线程对 I/O 阻塞有效，简单 |
| 海量网络 I/O（爬虫、API 网关） | `asyncio` + `aiohttp`/`httpx` | 单线程协程，省资源 |
| 需要并行跑 numpy/pandas | 三方库内部已优化，别自己开进程 |

```python
# 线程池（下载多个 URL）
from concurrent.futures import ThreadPoolExecutor, as_completed

def fetch(url):
    return url, requests.get(url, timeout=10).status_code

urls = [...]
with ThreadPoolExecutor(max_workers=8) as pool:
    futures = {pool.submit(fetch, u): u for u in urls}
    for fut in as_completed(futures):        # 谁先完成先处理
        try:
            print(fut.result())
        except Exception as e:
            print("失败", futures[fut], e)
```

```python
# 进程池（CPU 密集）
from concurrent.futures import ProcessPoolExecutor

def heavy(n):
    return sum(i * i for i in range(n))

if __name__ == "__main__":              # ✅ Windows 下多进程必须有这行保护
    with ProcessPoolExecutor() as pool:
        list(pool.map(heavy, [10**7, 10**7, 10**7]))
```

```python
# asyncio
import asyncio, httpx

async def fetch(client, url):
    r = await client.get(url)
    return r.status_code

async def main():
    async with httpx.AsyncClient(timeout=10) as client:
        results = await asyncio.gather(*(fetch(client, u) for u in urls))
        print(results)

asyncio.run(main())
```

关键概念：**GIL** 让一个进程内同一时刻只有一个线程执行 Python 字节码 → 多线程不适合 CPU 并行；`await` 只在 async 函数里可用；协程不会自动并发，要 `gather`/`create_task`。

## 7. 测试

```python
# test_calc.py（pytest 约定：文件名 test_*.py，函数 test_*）
import pytest
from mypkg.calc import divide

def test_divide():
    assert divide(10, 2) == 5          # pytest 失败时自动展开左右值

@pytest.mark.parametrize("a,b,exp", [(1,1,1), (2,3,8), (0,5,0)])
def test_pow(a, b, exp):
    assert a ** b == exp

def test_zero():
    with pytest.raises(ZeroDivisionError):
        divide(1, 0)

@pytest.fixture
def db():
    conn = make_conn()
    yield conn
    conn.close()                       # 前后置（setup/teardown）
```

运行：`pytest -q` / `pytest -k "divide" -v` / `pytest --cov=src`（`pytest-cov`）

标准库版：

```python
import unittest
class TestCalc(unittest.TestCase):
    def test_divide(self):
        self.assertEqual(divide(10, 2), 5)
        self.assertRaises(ZeroDivisionError, divide, 1, 0)
```

测试原则：** arrange-act-assert **；先写失败用例再修代码；不测私有细节；覆盖率是参考不是目标。

## 8. 三个必会用的小工具

```python
# 1) 结构化数据处理：pandas 读 CSV
import pandas as pd
df = pd.read_csv("data.csv", encoding="utf-8-sig")   # -sig 处理 BOM
df.head(); df.describe(); df["col"].value_counts()
df[df["age"] > 18].groupby("city").size().sort_values(ascending=False)

# 2) Excel
df.to_excel("out.xlsx", index=False)

# 3) 脚本批处理：shutil
import shutil
shutil.copy2("a.txt", "dir/")           # 带元数据复制
shutil.move("a.txt", "b/c.txt")
shutil.rmtree("tmpdir", ignore_errors=True)
shutil.make_archive("backup", "zip", "src_dir")
shutil.disk_usage("/")
```

## 9. 本章练习

1. 写一个「今日汇率/天气」命令行小工具：调公共 API，`argparse` 控制输出格式（text/json）。
2. 用正则从一段混排文本里提取：邮箱、手机号、URL、日期（各自写成命名组并测试）。
3. 计算距离你下次生日还有多少天，输出「距今 X 天，那年是星期 Y」。
4. 用 `ThreadPoolExecutor` 并发抓 20 个网页，与串行版比较耗时，输出统计。
5. 给 [04 篇] 的词频统计函数写 pytest 用例，含参数化和异常用例。
6. 读一个 CSV，输出各城市平均年龄 Top5，并另存为新的 Excel。

---


---
# 06 · 学习路线与项目实战

## 1. 总体路径

```
语法基础 → 数据结构/函数 → 面向对象 → 文件/异常/模块 → 调试与测试 → Git → 选方向深耕
```

打基础阶段的目标不是「学完所有知识」，而是**能独立写出 200–500 行、结构清晰、有错误处理的小工具**。

## 2. 项目清单（按难度递进）

### ⭐ 入门（学完 01–02 就能做）

| 项目 | 练到什么 |
|---|---|
| 命令行计算器（支持表达式、历史记录） | 循环、字符串解析、异常 |
| 猜数字 / 石头剪刀布 / 24 点 | 随机、条件、胜负判断 |
| 单位换算器（长度/重量/温度/货币） | dict 组织数据、函数拆分 |
| 通讯录（增删改查 + 存 JSON 文件） | 文件读写、数据校验 |
| 倒计时 / 番茄钟 | time、循环、清屏 |
| 随机密码生成器 | random、字符串、参数设计 |

### ⭐⭐ 进阶（学完 03–05）

| 项目 | 练到什么 |
|---|---|
| 批量文件重命名 / 按类型归档工具 | pathlib、`re`、dry-run 设计 |
| Markdown 转 HTML 简易解析器 | 状态机、正则、测试 |
| 学生成绩分析报表（CSV → 统计 → 图表） | pandas、matplotlib |
| 个人记账 CLI（sqlite3 存储 + 报表） | SQL、事务、argparse 子命令 |
| 网页小说/图片爬虫（含限速、断点续传） | requests、robots、异常重试、日志 |
| 简易 Web 服务器（不框架，用 socket 或 http.server） | HTTP 协议本质 |
| TODO 管理工具（JSON/SQLite 双后端） | 抽象层、接口设计 |
| 定时备份脚本（打包、按日期命名、保留 N 份） | subprocess、zipfile、logging |
| 贪吃蛇 / 2048（pygame 或终端 curses） | 游戏循环、事件、状态 |

### ⭐⭐⭐ 综合（做出可放进简历的作品）

| 项目 | 技术栈 |
|---|---|
| 博客系统 / 短链接服务 | FastAPI + SQLite + Jinja2 + 登录鉴权 |
| 数据看板：抓取 → 清洗 → 存储 → 可视化 | requests/pandas/sqlite/Streamlit |
| RSS 聚合 + 摘要推送（微信/邮件） | asyncio、feedparser、smtplib |
| 个人知识库检索（全文搜索） | Whoosh/Tantivy 或 sqlite FTS5 |
| CI 用的代码规范检查工具 | `ast`、ruff 集成 |
| 小型分布式任务队列（Redis） | redis、队列、worker 池 |

## 3. 方向选择

### 🌐 Web 后端

```
FastAPI（推荐入门，带类型注解、自动生成文档） 或 Django（全家桶，企业用得多）
  ├ 基础：HTTP、REST、状态码、Cookie/Session/JWT
  ├ 数据库：SQLAlchemy（ORM）、Alembic（迁移）、PostgreSQL
  ├ 验证与序列化：Pydantic
  ├ 异步：asyncio、Celery / ARQ / Dramatiq 做后台任务
  ├ 缓存：Redis
  ├ 部署：uvicorn + gunicorn、Nginx、Docker
  └ 测试：pytest + httpx
```

起步项目：Todo API → 博客 API → 带用户系统的短链接服务。

### 📊 数据分析 / 科学计算

```
Jupyter → NumPy（数组思维）→ pandas（核心）→ matplotlib/plotly（图）
  ├ 数据清洗：缺失值、类型转换、merge/groupby/pivot
  ├ 统计：scipy、statsmodels
  ├ 机器学习：scikit-learn → （深入）PyTorch
  ├ 大数据：polars / dask
  └ 报表：Streamlit / Dash / 直接出 Excel
```

练习素材：Kaggle 入门赛、公开 CSV（天气、股票、地铁客流）。

### 🤖 自动化办公 / 脚本

```
文件：pathlib / shutil / glob
Excel：openpyxl（读写）/ pandas（分析）/ xlsxwriter（生成）
Word：python-docx    PPT：python-pptx    PDF：pypdf / pdfplumber
邮件：smtplib + email（发）/ imaplib（收）
网页自动化：Playwright（首选）/ Selenium
桌面自动化：pyautogui（点鼠标敲键盘）
定时：Windows 任务计划程序 / crontab / APScheduler
OCR：PaddleOCR / RapidOCR
```

典型场景：每周一自动汇总报表并发邮件、批量处理扫描件、爬价格表填 Excel。

### 🧠 AI / LLM 应用

```
Python 基础 → API 调用（openai / httpx）→ 流式输出与 tool call
  ├ 编排：LangChain / LlamaIndex（或直接裸写，更可控）
  ├ 向量库：chromadb / faiss / pgvector → RAG
  ├ Agent：函数调用 + 循环 + 记忆
  ├ 部署：FastAPI + SSE/WebSocket
  └ 训练方向：PyTorch → transformers → 微调（LoRA）
```

### 🕷️ 爬虫

```
requests → 网页结构（BeautifulSoup / parsel）→ re/xpath
  ├ 反爬：headers、代理池、限速、验证码、Cookie 池
  ├ 动态页面：Playwright、抓 XHR 接口（优先找接口，别硬啃渲染）
  ├ 框架：Scrapy（规模化）
  ├ 分布式：Redis 队列 + 多机
  └ 存储：CSV → SQLite → MongoDB
```

### 其他：桌面 GUI（PySide6 / Tkinter）、嵌入式/树莓派、DevOps（Ansible）、游戏（pygame / Godot+GDScript）。

## 4. 必须同步掌握的周边技能

| 技能 | 为什么 | 学到什么程度 |
|---|---|---|
| **Git** | 代码管理、面试必备、协作 | `init/clone/add/commit/push/pull/branch/merge/revert` + 冲突处理 |
| **命令行** | 效率 | 管道、重定向、`grep`、任务计划 |
| **调试** | 摆脱 print | VS Code 断点、`breakpoint()`、看 traceback、`pdb` 基本命令 |
| **代码规范** | 可读性 | PEP 8、Ruff/Black、类型注解、`mypy` |
| **虚拟环境/打包** | 交付 | `uv`/venv、`pyproject.toml`、`pipx`、PyInstaller |
| **Docker** | 部署 | 写简单 Dockerfile、compose 起数据库 |
| **数据库 SQL** | 通用 | SELECT/JOIN/GROUP BY/索引，会用一种（SQLite/Postgres） |
| **英语** | 查文档 | 能读官方文档的错误信息与 API 说明 |

## 5. 高效学习方法

1. **敲，不抄**：示例代码自己手打一遍，删掉注释凭记忆重写一次。
2. **改，再验证**：每个例子都改一两个参数，先猜结果再运行，猜错了才真正学到东西。
3. **报错自己读 30 秒**：Traceback 从下往上读，先查最后一行错误类型；查不到再搜索/问 AI。
4. **写项目而不是刷课**：看视频有「懂了」的错觉。选一个项目，遇到不会的再回去查，学习效率最高。
5. **费曼输出**：给每章写自己的笔记版本，或写博客讲给别人听。讲不清就是没懂。
6. **刻意练结构**：把一段 200 行的面条代码重构成函数 + 类 + 模块，比写新代码更能长功力。
7. **每天 ≥ 1 小时连续敲代码**，胜过周末突击 10 小时。

## 8 周计划参考

| 周 | 内容 | 产出 |
|---|---|---|
| 1 | 00 + 01 环境 + 基础语法 | 计算器、猜数字 |
| 2 | 02 数据结构与函数 | 通讯录（存文件） |
| 3 | 03 面向对象 | 贪吃蛇或记账本类图 |
| 4 | 04 文件/异常/模块 | 文件整理脚本 + 单元测试 |
| 5 | 05 JSON/正则/日期/网络 | 天气 CLI 工具 |
| 6 | 选方向 + Git | 第一个方向的小 demo |
| 7 | 综合项目主体 | 可运行的完整程序 |
| 8 | 补测试、日志、文档、部署 | 一个 GitHub 仓库（README + 截图） |

## 6. 资源清单

- 官方：[Python 教程（中文）](https://docs.python.org/zh-cn/3/tutorial/)、[官方文档](https://docs.python.org/3/)
- 入门书：《Python Crash Course（Python 编程：从入门到实践）》、《Automate the Boring Stuff with Python》（自动化那本，免费在线）
- 进阶书：《Fluent Python》（二手/影印均可，进阶必读）、《Effective Python》（90 条具体建议）
- 练习：Exercism Python track、LeetCode（算法）、[Codewars](https://www.codewars.com/)、Edabit、[Python Exercises](https://www.w3resource.com/python-exercises/)
- 项目灵感：https://github.com/kelseyhightower /「100 Python Projects」类仓库
- 规范：PEP 8、Google Python Style Guide
- 工具：Ruff、Black、mypy、pre-commit、pytest、uv

---


---
# 99 · Python 速查表

> 忘了就查这页。适合打印或放在编辑器第二个标签页。

## 1. 高频内置函数

```python
len(s) type(x) id(x) dir(x) help(x) repr(x) str(x) format(x, '.2f') hash(x)
int() float() bool() list() tuple() set() dict() frozenset() bytes() enumerate()
zip() map() filter() sorted() reversed() range() all() any() sum() min() max()
abs() round() pow() divmod() iter() next() print() input() open() slice()
isinstance() issubclass() callable() getattr(obj,'x',default) setattr() hasattr()
vars() globals() locals() super() property() staticmethod() classmethod()
```

## 2. 字符串方法

| 方法 | 作用 |
|---|---|
| `strip()/lstrip()/rstrip()` | 去空白或指定字符 |
| `split(sep, maxsplit)` / `rsplit` / `splitlines` | 切 |
| `join(iterable)` | 拼 |
| `replace(old, new, n)` | 换 |
| `find/index/rfind/rindex` | 查（index 找不到报错） |
| `count` `startswith` `endswith` `in` | 判断 |
| `lower/upper/title/capitalize/casefold/swapcase` | 大小写 |
| `isdigit/isalpha/isalnum/isspace/isidentifier` | 字符类型 |
| `format` `center/ljust/rjust/zfill` | 排版 |
| `encode/decode` | 与 bytes 互转 |

f-string 常用格式：`{x:.2f}` 两位小数、`{x:%}` 百分比、`{x:,}` 千分位、`{x:>10}` 右对齐、`{x:^10}` 居中、`{d:%Y-%m-%d}` 日期、`{x!r}` repr、`{x=}` 调试打印表达式与值。

## 3. 列表 / 字典 / 集合 常用

```python
# list
append extend insert pop remove clear index count sort sorted reverse copy
lst[::-1]  del lst[i]  lst[i:j] = [...]  lst += other  x in lst
# dict
get setdefault pop popitem update keys values items clear copy |
# set
add discard remove pop update clear
a & b a | b a - b a ^ b a.issubset(b) a.isdisjoint(b)
# 排序/去重保留顺序
sorted(set(x))          # 不保序
list(dict.fromkeys(x))  # 保序去重
```

## 4. 控制流片段

```python
for i, v in enumerate(seq, 1): ...
for a, b in zip(xs, ys): ...
for line in sys.stdin: ...
while (chunk := f.read(8192)): ...      # 3.8+ 海象
if x is None / if not items / if k in d
match case 分支
try/except/else/finally
with open(p) as f, open(q, "w") as g: ...
```

## 5. 函数与类

```python
def f(a, b=1, *args, kw=2, **kwargs) -> int: ...
f(*list_arg, **dict_arg)          # 调用时解包
lambda x: x*2
class C(Base):
    attr = 1
    def __init__(self): super().__init__()
    @property
    def p(self): ...
    @staticmethod
    def s(): ...
    @classmethod
    def c(cls): ...
@dataclass
class P: name: str; age: int = 0
```

## 6. 模块与工具命令

```bash
python -m venv .venv && source .venv/bin/activate
python -m pip install -r requirements.txt
python -m pytest -q
python -m http.server 8000         # 快速起静态服务器
python -m json.tool file.json      # 格式化 JSON
python -m timeit -s "import x" "x.f()"
python -m pdb script.py
python -m py_compile file.py       # 语法检查
pip freeze > requirements.txt
ruff check --fix . && ruff format .
```

## 7. 高频小抄

```python
# 计时
import time; t=time.perf_counter(); ...; print(time.perf_counter()-t)

# 安全取嵌套字典
v = d.get("a", {}).get("b", "缺省")

# 读文件一行都不留换行
lines = [l.rstrip("\n") for l in open(p, encoding="utf-8")]

# 交换 / 反转 / 首尾
a, b = b, a ; lst.reverse() ; lst.pop(0); lst.pop()

# 字典按值排序
sorted(d.items(), key=lambda kv: kv[1], reverse=True)

# 计数与分组
Counter(items) ; itertools.groupby(sorted(items, key=k), key=k)

# 拼接多路径 / 取文件名
from pathlib import Path; Path.home()/"docs"/"a.txt"

# 环境变量与参数
os.environ.get("KEY", "") ; sys.argv ; os.getenv

# 判断脚本被直接运行还是被导入
if __name__ == "__main__": ...

# 打印进度条
for i in range(n):
    print(f"\r{i/n:6.1%}", end="", flush=True)

# 断言（调试用，别用于业务校验：python -O 会跳过）
assert x > 0, "x 必须为正"

# 调试断点
breakpoint()
```

## 8. 报错信息 → 原因速查

| 报错 | 通常是 |
|---|---|
| `SyntaxError` | 少括号/冒号、中文标点、f-string 写错 |
| `IndentationError` | 缩进不规范、Tab/空格混用 |
| `NameError: name 'x' is not defined` | 拼写错 / 还没定义 / 忘了 import |
| `TypeError: 'NoneType' object is not ...` | 函数返回 None（常见于 `lst = lst.sort()`） |
| `TypeError: object of type 'int' has no len()` | 类型搞错，未转换 |
| `TypeError: unhashable type: 'list'` | 列表/dict 当 dict 键或放进 set |
| `KeyError` / `IndexError` | 键/索引不存在，用 `get` 或先判断 |
| `ModuleNotFoundError` | 未安装 / 解释器或虚拟环境不对 |
| `AttributeError` | 方法名写错 / 对象类型不符预期 |
| `ValueError: invalid literal for int()` | `int("3.5")` 或含空格 → `int(float(s))` |
| `UnicodeDecodeError` | `encoding` 没指定或指定错（GBK vs UTF-8） |
| `FileNotFoundError` | 路径相对当前工作目录，用 `Path(__file__).parent` |
| `RecursionError` | 递归没出口 |
| `UnboundLocalError` | 函数内先赋值再使用同名全局变量 |
| `ImportError: attempted relative import` | 直接运行了包内文件 → `python -m 包.模块` |
| `PermissionError` | 文件被占用 / 无权限 / 需要管理员 |

## 9. 十条「Pythonic」原则

1. 用推导式代替 `map/filter` 的 lambda。
2. `for x in seq` 而非索引循环。
3. `with` 管资源。
4. `enumerate` / `zip` 替代手写计数器。
5. 三元、解包、切片代替样板代码。
6. 异常处理优先（EAFP），别写一堆前置判断。
7. 明确比隐性好：`import this` 读一遍 Zen of Python。
8. 函数短小、单一职责，参数超过 4 个考虑 dataclass。
9. 可变默认参数是 bug 之源。
10. 复杂逻辑写测试，简单逻辑也写测试。

## 10. 快捷键（VS Code）

| 操作 | Windows/Linux |
|---|---|
| 运行当前文件 | `Ctrl+F5` / 右键 Run Python File in Terminal |
| 调试运行 | `F5`，断点 `F9`，单步 `F10/F11` |
| 选中区域快速执行 | `Shift+Enter`（交互式窗口） |
| 格式化 | `Shift+Alt+F` |
| 注释/取消 | `Ctrl+/` |
| 上移/下移行 | `Alt+↑/↓` |
| 多光标 | `Alt+点击` / `Ctrl+D` |
| 重命名符号 | `F2` |
| 跳转定义 | `F12`，返回 `Alt+←` |

---
