---
permalink: /python/
title: "Python"
excerpt: ""
author_profile: true
toc: true
---


推荐链接

[廖雪峰的官方网站](https://liaoxuefeng.com/books/python/reg-exp/index.html)

## Grammar

### Basic Data Class

|      | Tips                   |
| ---- | ---------------------- |
| List | 有序，但查找效率非常低 |
| Dict | 有序，但并非插入顺序   |
| Set  | 无序，顺序是随机的     |

### Function

#### 函数签名

```Plain
def f(x: int, b: float):
    pass
```

#### 参数组合

定义函数时，可使用***必选参数、默认参数、可变参数、关键字参数和命名关键字参数***。

这5种参数都可以组合使用，但***必须按顺序***使用。

有多个默认参数时，调用的时候，既可以按顺序提供默认参数，也可以不按顺序提供部分默认参数。当不按顺序提供部分默认参数时，需要把参数名写上。

比如定义一个函数，包含上述若干种参数：

```python
def f1(a, b, c=0, *args, **kw):
    pass

def f2(a, b, c=0, *, d, **kw):
    pass
```

在函数调用的时候，必须先按顺序传入，再按参数名传入。

由于Python解释器会按上述顺序解析5种参数，因此，如果要传入可变参数，必须先传入默认参数。

#### 解包

对于任意函数，都可以通过类似`func(*args, **kw)`的形式调用它，无论它的参数是如何定义的。

tuple用`*`解包，dic用`**`解包。

```python
args = (1, 2, 3, 4)
kw = {'d': 99, 'x': '#'}
f(*args, **kw)
```

- `zip()`表示压缩；`zip(*)`表示解压

  ```python
  >>> a = [1,2,3]
  >>> b = [4,5,6]
  >>> zip(a,b)     # 返回一个对象
  <zip object at 0x103abc288>
  >>> list(zipped)  # list() 转换为列表
  [(1, 4), (2, 5), (3, 6)]
  >>> a1, a2 = zip(*zip(a,b))  # 与 zip 相反，zip(*) 可理解为解压
  >>> list(a1), list(a2)
  [1, 2, 3], [4, 5, 6]
  ```

#### 装饰器

装饰器**定义复杂，使用简单**

以定义一个`log`装饰器为例，其中`@functools.wraps(func)`可以保持装饰后函数的`__name__`属性不变

```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

使用装饰器只需要在需要装饰的函数定义前加`@log`，即可使得装饰后的函数在每次调用时打印函数名称

```python
@log
def my_func(*args,**kw):
	pass
```

常用装饰器

@dataclass

```python
@dataclass
class Point:
    x: float
    y: float
    z: float = 0.0

#上下两者等价
    
class Point:
    def __init__(self, x: float, y: float, z: float = 0.0):
        self.x = x
        self.y = y
        self.z = z
    
    def __repr__(self):
        return f"Point(x={self.x}, y={self.y}, z={self.z})"
    
    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return (self.x, self.y, self.z) == (other.x, other.y, other.z)
```

#### 匿名函数和偏函数

lambda在传参时传的是变量；partial在传参时传的是对象；

### Iterator

<img src="https://raw.githubusercontent.com/xuzhang0112/pic-repo/main/202407191834521.png" alt="image-20240719183328055" style="zoom: 25%;" />

| 可迭代对象     | 定义                                    | 举例                                                         |      |
| -------------- | --------------------------------------- | ------------------------------------------------------------ | ---- |
| 一般可迭代对象 | 可通过for循环遍历的对象，可通过索引访问 | 字符串、列表、字典、集合……                                   |      |
| 迭代器         | 惰性遍历，通过next访问下一个            | 一般可迭代对象创建的迭代器（使用iter创建）、生成器（使用yield创建） |      |

**for循环的本质是通过iter方法获取可迭代对象的迭代器**

### 好用的数据类型

#### namedtuple

同时具有字典的可读性和元组的不变性

```
from collections import namedtule

Point = namedtuple('Point', ['x', 'y'])
p1 = Point(1, 2)
print(p1.x)
print(p1[0])
```



```
from typing imported NamedTuple

class Point(NamedTuple):
	   x: float
	   y: float

p1 = Point(1, 2)
```



### OOP

#### 从 property 到描述符

@property可以将方法伪装成属性

描述符：property就是描述符，可以将属性的逻辑打包成一个类，方便复用

- 在访问实例属性时，优先考虑同名的数据描述符，其次是实例属性，再次是同名的非数据描述符

- ```
  def __get__(self, instance, owner):
  	  pass
  	
  def __set(self, instacne, value):
      pass
  
  def __set_name__(self, owner, name):
      pass
  ```

- 

  ***静态变量***

```Plain
Class A:
    a: List[int]
    b: Dict[float] | str
```

***方法***

- 内置方法
- 实例方法
  - 实例方法最少也要包含一个 self 参数，调用时Python会自动将对象传给self参数
  - 实例方法一般由对象直接调用，也支持使用类名调用实例方法，但此方式需要手动给 self 参数传值
- 类方法
  - 类方法需要使用`＠classmethod`修饰符进行修饰，入参必须包含cls
  - 主要用于类的初始化，或者需要对多个实例所在的类进行操作
  - 类方法推荐使用类名直接调用，当然也可以使用实例对象来调用（不推荐）
- 静态方法
  - 静态方法需要使用`＠staticmethod`修饰
  - 相当于函数，唯一的区别是静态方法定义在类命名空间中，而函数定义在程序的全局命名空间中
  - 既没有类似 self、cls 这样的特殊参数，也无法调用任何类属性和类方法
  - 既可以由类调用，也可以由对象调用

### Environment

#### pip

***换镜像源***

```sh
pip config set global.index-url  https://pypi.mirrors.ustc.edu.cn/simple  # 永久切换
```

***安装第三方库***

普通安装

```sh
pip install package # 安装到python安装目录(可修改)
pip install package -e # 安装到工作目录，便于修改
pip install -i ``https://pypi.mirrors.ustc.edu.cn/simple``  matplotlib # 临时指定镜像
pip install -r requirements.txt # 批量安装
```

- 有些库在导入时和安装时的名称是不一样的，例如hydra和hydra-core，sklearn和scikit-learn等
- 有些库没有被pypi收录，只能在GitHub上下载和安装，例如GroundingDINO
- 有些库无法安装是因为操作系统的一些底层库版本太老，例如gcc和libstdcxx-ng，CUDA版本过老等，如果没有sudo权限，可以通过conda安装一些依赖库

#### conda

安装

```sh
mkdir -p ~/miniconda3
wget [url] -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

自启动配置

```sh
source ~/miniconda3/bin/activate # 临时激活conda
conda init --all # 在bashrc中写入conda自启脚本
conda config --set auto_activate_base true # 是否自启动base
```

管理多个环境

```sh
conda create -n env_name python=3.9 #新建
conda activate env # 激活
conda deactivate # 取消激活
conda remove --name [env_name] --all #删除
```

#### uv

安装

```shell
curl -LsSf https://astral.sh/uv/install.sh | sh
```

换源

```sh
UV_DEFAULT_INDEX="https://pypi.mirrors.ustc.edu.cn/simple"
```

环境管理

```shell
uv init # 创建项目结构，包括readme,pyproject.toml,等等，也可以不用
uv venv --python 3.11 # 创建虚拟环境
source .venv/bin/activate # 激活虚拟环境

uv pip install [package] # 安装包
uv pip freeze > requirements.txt # 导出包清单
uv pip sync requirements.txt # 复现环境

uv add [package] # 安装包，并生成uv.lock
uv sync # 根据uv.lock复现环境，必须和uv add生成的uv.lock配合使用
```

全局工具

```sh
uv tool install [package] # 无需编辑，全局安装
uv tool install -e . # 可编辑
```

- 实测发现有cuda依赖的包不容易独立安装，更建议创建独立的虚拟环境，通过``source .venv/bin/activate`来激活环境

### Modules

* 绝对导入：沿着`sys.path`搜索Module，首先是被执行的python文件所在的目录，注意不是工作目录，其次是Python包的安装路径

  * `import A.B`或`from A import B`

  * `sys.path.append() ## NOQA: E402` 将项目目录暂时添加到环境变量PYTHONPATH中

* 相对导入：仅限于package内部

  * 通过`__init__.py`中的`from . import A`或`from ..A import B`实现
  * 使用相对导入的程序无法作为主程序执行，只能用作package的入口
  * 最佳实践：在包根下的`__main__.py`中实现debug的代码，然后用`python -m my_package`调试

* 文件和文件夹在作为Module时是同一层级的，使用`from A import *`时都是导入其下所有的对象

  定义package

  `__init__.py`定义了导入一个包时的行为，

```
from . import moduleA
from .module A import x

__all__ = [] # 定义了import *时的行为

__version__ = '1.0.0'
__author__ = 'Jack'
```



## 异步

### Asyncio

提升程序的并发能力，而不需要多线程或多进程

| 名称                    | 简介                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `async def`             | 定义一个异步函数（返回 coroutine 对象）                      |
| `await`                 | 1. 交出await所在任务的控制权<br />2. 将协程转化为需要执行的任务<br />3. 直到被执行的任务结束才会向下执行 |
| `asyncio.create_task()` | 将协程转化为任务，加入等待队列<br />注：任务只要被创建，一定会执行，除非事件循环终止 |
| `asyncio.run()`         | 启动并运行事件循环                                           |
| `asyncio.gather()`      | 并发运行多个任务，等待全部完成                               |
| `asyncio.sleep()`       | 异步睡眠（不阻塞事件循环）                                   |

- 异步函数只能通过run、await和gather来调用
- 调用异步函数的函数必须是异步函数 (async def)

## NLP

### spacy

[第三方：用于生物医学语料的spacy模型](https://allenai.github.io/scispacy/)

## Retrival

### faiss

索引

| Index | Tips                 |
| ----- | -------------------- |
| Flat  | <10k, 搜索质量要求高 |
|       |                      |

## Website

### Streamlit

通过各种组件来显示

```
# 显示文本的两种方式，缺点是高度不可调
st.text()
st.markdown()
```

通过容器类来调整布局

```
cols = st.columns([2,1,1])
with cols[0]:
	st.write()
```

导入数据，并使用缓存机制防止重复读写

```
@st.cache_data
def load_data(nrows):
	pass
load_data()
```

Multipage App

优点是可以独立编写各个Page的代码，缺点是交互性不如单页

```
entry.py
pages/
	page1.py
	page2.py
	page3.py
```



## Web

### FastAPI & Requests

https://www.runoob.com/fastapi/fastapi-tutorial.html

[利用FastAPI构建大模型接口服务](https://blog.csdn.net/qq_36187610/article/details/131835752)

```python
# Server
from fastapi import FastAPI
from pydantic import BaseModel
import uvicorn

app = FastAPI()

class Item(BaseModel):
    text: str

@app.post("/predict")
def infer(item: Item):

    return {"pred":...}

uvicorn.run(app, host="0.0.0.0", port=9898, workers=1)

# Client
import requests

requests.post(
        "http://0.0.0.0:9898/predict", json={"text": discharge_summary}
    )
```



## Data Processing

### Polars

pandas上位替代

https://docs.pola.rs/user-guide/getting-started/

- 构建DataFrame

  既可以通过键值对（列名，和列元素组成的列表）来定义，也可以从csv读取）

  ```
  import polars as pl
  import datetime as dt
  
  df = pl.DataFrame(
      {
          "name": ["Alice Archer", "Ben Brown", "Chloe Cooper", "Daniel Donovan"],
          "birthdate": [
              dt.date(1997, 1, 10),
              dt.date(1985, 2, 15),
              dt.date(1983, 3, 22),
              dt.date(1981, 4, 30),
          ],
          "weight": [57.9, 72.5, 53.6, 83.1],  # (kg)
          "height": [1.56, 1.77, 1.65, 1.75],  # (m)
      }
  )
  
  
  df = pl.read_csv("docs/assets/data/output.csv", try_parse_dates=True)
  ```

- 表达式与上下文

  这是polars最核心的概念，表达式只有在上下文里才会生效

  当然，表达式通常是以列为单位的，它必然包含`pl.col('列名')`

  ```
  pl.col("weight") / (pl.col("height") ** 2)
  ```

  这个表达式的含义是通过身高列和体重列计算BMI，然而它单独执行是无效的，必须在上下文中被使用

- 增加/删除列

  `df.select(`只会保留选取的列，此外还可以增加新的列，并通过`alias()`来改变列名

  ```
  df.select(
      pl.col("name"),
      pl.col("birthdate").dt.year().alias("birth_year"),
      (pl.col("weight") / (pl.col("height") ** 2)).alias("bmi"),
  )
  ```

  `df.with_columns()`则会在现有的基础上增加一些新列

- 基于条件筛选行

  ```
  df.filter(pl.col("birthdate").dt.year() < 1990)
  ```

  注意，只有polars支持的布尔算子可以被使用，例如，包含关系要用`is_in()`

- 分组聚合

  首先按照某个key进行分组，然后可以对多行进行聚合，默认会聚合成列表，也可以用first()之类的方法来选择更高级的聚合操作

  ```
  result = (
      df.with_columns(
          (pl.col("birthdate").dt.year() // 10 * 10).alias("decade"),
          pl.col("name").str.split(by=" ").list.first(),
      )
      .select(
          pl.all().exclude("birthdate"),
      )
      .group_by(
          pl.col("decade"),
          maintain_order=True,
      )
      .agg(
          pl.col("name"),
          pl.col("weight", "height").mean().round(2).name.prefix("avg_"),
      )
  )
  ```


- 合并多个DataFrame

  `df.join()`会基于某个字段进行匹配

  `pl.concat()`则是直接将两个表格按照行/列/对角线进行拼接

### Pandas

[菜鸟教程yyds](https://www.runoob.com/pandas/pandas-dataframe.html)

创建Dataframe

```python
# 从外部读取
read_csv() # sep:分隔符; header:默认是第一行; names:如果指定则说明没有header;
read_excel()

# 创建空的DataFrame
Dataframe(columns=[column_name1, column_name2])

# 根据可迭代对象创建DataFrame
Dataframe([{column1:a,column2:b},{}])  # 每个元素是一个Dict，表示一行
Dataframe([[a,b],[c,d],[e,f]]，columns=['c1','c2'])  # 每个元素是一个List，代表一行

# 根据Dict创建DataFrame
Dataframe.from_dict({column1:[...],column2:[...]}) #字典的key表示某一列
Dataframe.from_dict({index1:[c1,c2,c3], index2:[c1,c2,c3], ...}) #字典的key表示某一行的索引
```

添加新的行/列

```python
# 创建新的列
df[new_column] = constant / df[a]+df[b] / df[a].apply(func) # 老版本的写法
df.loc[:,c] = df[a] + df[b] # 更建议的写法

# 创建新的行
df.loc[len(df)] = Series({column1=x,column2=y,...})
```

筛选行/列/元素

```python
# 索引单行单列
df[column_name]`  # 索引某一列
df.loc[idx]  # 索引某一行

# 索引单一元素
df.loc[idx, column_name]  # 索引某个元素
df.iloc[idx, column_idx]  # 
df.loc[bool_index].values #

# 索引多行多列
df.loc[[idx1,idx2]][[column_name1,colunmn_name2]] #索引多行多列
df.loc[[idx1,idx2],[column_name1,colunmn_name2]] #索引多行多列

# 按条件筛选
df.loc[df[a] == df[b],:] # 按列筛选的结果，本质上是一种行索引

```

更改索引列

```python
df[new_column_list] # 重排column
df.reset_index(names=[]) # 生成新的索引列，原索引列重命名
```

统计分析

```python
df.value_counts(column_name)
df.plot()
```

- 判断某个元素是否属于某一列，必须先将该列转化为可迭代对象

### Matplotlib

单图：基于

```python
plt.bar(bins1,height=hist1) # 柱状图
plt.scatter(x,y,size,color) # 散点图
```

多图：基于坐标系对象(Axes)的语法，一张Figure中有多个Axes（自定义函数通常也以ax为参数）

```python
fig, axes = plt.subplots(2,3,figsize=(6,6)) # 首先定义多个Axes，可以是subplots或者gridspec

ax = axes[0,0] # 以ax为基本单位，进行绘制/删除
ax.plot_surface()
ax.set_zlabel()
axes[1,1].remove()

ax = fig.add_subplot(2,2,4,projection='3d') # 添加一个subplot
fig.add_gridspec() # 添加1个网格,辅助定位

fig.suptitle() # 大图层面的标题和坐标轴标签
fig.supxlabel()
fig.supylabel()

plt.show() # 显示/保存，两者选择其一即可
plt.savefig()
```

风格/尺寸/字体

```python
import matplotlib as mpl
plt.style.use('ggplot/seaborn-dark')
mpl.rcParams['font.familiy'] = ['Heiti SC']
mpl.rcParams['figure.dpi'] = 300


from matplotlib.font_manager import FontProperties

font = FontProperties(fname="../PingFang.ttc", size=10) # 导入本地字体
plt.title(title, fontproperties=font) # 在每一次绘制文本时，指定相应的字体
```

参考资料

[官方示例](https://matplotlib.org/stable/gallery/index.html)

[第三方示例](https://www.python-graph-gallery.com/)

### Jupter Notebook

在cell中执行shell

```sh
!pwd #单条shell命令
%%bash #整个cell作为shell脚本
```

模块自动重载，否则每次修改需要重启kernel

```
%load_ext autoreload
%autoreload 2 #每执行一个cell都会重载所有模块
```

### json/yaml

以json为例，其读写文件需要使用文件句柄

```python
with open(filename,'r') as f: 
    json_dict = json.load(f) #读

with open(filename,'w') as f:
    json.dump(json_dict, f, ensure_ascii=False) #写
    
```

yaml与json的写法是类似的，只是用到的库是yaml

[yaml校验器](https://www.bejson.com/validators/yaml_editor/)

## Deep Learning

### Torch

#### Tensor

创建tensor

```python
torch.tensor()  #将其他数据类型（list,array等）转换为张量
torch.empty()  
torch.zeros()                                                                            
torch.ones()                                                                              
torch.rand()    # 均匀分布, 0-1之间
torch.randn()   # 正态分布, 均值为0, 方差为1
torch.normal()  # 可指定均值和方差的正态分布
torch._()
torch.randint() # 需要指定上下界                                                            
```

* 创建tensor时，需要指定size和dtype

* 一般size=(x, y, z), dtype=torch.xx

* float=float32; double=float64; int=int32; long=int64

  操作tensor

```python
#拼接
torch.cat([tensor,...], dim=0) # 在现有的维度上堆叠
torch.stack([tensor,...], dim=0) # 创建一个新的维度，在上面堆叠
torch.nn.utils.rnn.pad_seq([seq_a, seq_b, ...], batch_first=True) # 不等长序列补零后拼接

# 索引
tensor[x,y,z] # 标量索引，索引坐标为(x,y,z)的元素
tensor[[x1,x2],y,z] # 列表索引，索引坐标为(x1,y,z)和(x2,y,z)的元素
tensor[tensor1,tensor2,tensor3] # 张量索引，索引坐标为(x1,y1,z1),...(xn,yn,zn)的元素，tensor必须是一维张量
(tensor > 0.5).nonzero(as_tuple=False) # 获取满足条件的索引

# 扩张
tensor.expand() #沿维度扩张n倍
tensor.repeat(n1, n2) # repeat的维度必须>=原始维度，并且原始的tensor会置于最后
'''
x = randn(dim1,dim2)
x.repeat(n1,n2)返回(n1*dim1,n2*dim2)
x.repeat(n1,n2,1,1)返回(n1,n2,dim1,dim2)
'''

# 转置
tensor.permute(dim1, dim2, dim3, …)
tensor.transpose(dim1, dim2) #只能二维

#变形
tensor.view(dim1,dim2,dim3,...)
tensor.reshape(dim1,dim2,dim3,...)

#计算
tensor.norm(dim, keep_dim=True) #沿指定维度求范数

# 复制+去除梯度
tensor.clone().detatch()
```

* 交换维度：permute可以对任意高维矩阵进行转置，transpose只能在两个维度之间转换，其他维度保持不变。
* Tensor重排: view更快，reshape分配新的内存，更慢但更通用
* Broadcast机制：维度不等的两个Tensor在进行运算时，从后往前遍历各个维度，若两个Tensor在某一维度不等，且较小的Tensor在该维度的长度为1或不存在时，可以Broadcast至相等维度

#### Model

注册参数

```python
model.register_buffer(name, tensor)
model.register_parameter(name, tensor)
nn.Parameter()
```

模型遍历

```python
model.modules()  #遍历model的所有子层，类比DFS搜索，有重复。
model.children()  #遍历model的所有浅层
model.parameters()  #保存的是Weights和Bais参数的值
```

模型微调

```python
for p in self.parameters():
    p.requires_grad = False
```

显存占用

1. 模型参数：初始化产生

2. 中间结果：前向传播产生（网络中如果存在一些较长的连接，比如第10层的网络需要使用来自网络第一层的输出结果，这部分的特征图就会一直占用显存）

3. 模型参数的梯度：反向传播产生

4. 优化器状态：例如Adam会保存每个参数的二阶梯度

   常见模块的的参数量和计算量

|       Module       |                            参数量                            |
| :----------------: | :----------------------------------------------------------: |
|       Conv2d       | $C_{out} \times H_{kernel} \times W_{kernel} \times C_{in}$  |
|       Linear       |                   $C_{out} \times C_{in}$                    |
| MultiHeadAttention | $N_{head} \times (C_{in} \times C_{query} + C_{in} \times C_{key} +C_{in} \times C_{value})$ |

#### Dataset & Dataloader

- transform: 一般在Dataset中定义，在返回一个Index的sample时进行处理

- batch_sampler: 从Dataset中获取一个batch的sample, 其中index的获取取决于采样算法

- collect_function: 将样本变成Batch
  - 输入：列表，长度为Batch_size，列表的每个元素是字典，代表一个样本
  - 输出：字典，每个键值对是一个类型和对应的Tensor，并且已经有了Batch_size这个维度

- [PyTorch DataLoader工作原理可视化](

### PyG: torch_gemotry

官方文档

- [Introduction by Example](https://pytorch-geometric.readthedocs.io/en/latest/get_started/introduction.html)

- [Heterogeneous Graph Learning](https://pytorch-geometric.readthedocs.io/en/latest/tutorial/heterogeneous.html)

  Installation

1. 根据cuda版本和pytorch版本，选择合适的PyG版本

2. 用whl安装各个PyG依赖包，然后把sparse-conv卸掉

   Debug

3. 报错信息并不一定反映真实情况

   1. 在CPU上调试以获取尽可能有效的报错信息
   2. 可能是版本问题或者是dtype不一致这种基础错误

4. PyG对异构图的支持不如同构图完善

   1. 优先使用同构图

   2. 异构图需要每张图都包含所有类型的节点和边，如果没有就用torch.empty()填充

   3. 异构图的边类型命名不能含有数字

      子图采样

      `torch_geometric.utils`提供了若干用于从大图中进行子图采样的API

| API            |                                                              |
| -------------- | ------------------------------------------------------------ |
| subgraph       | 只包含给点节点的子图；自动滤除没有边连接的节点；返回用全局索引表达的边和边属性 |
| k_hop_subgraph | 会给k-hop节点分配新的局部索引；edge_index是由新的局部索引来表达；edge_mask是按原始边索引的相对位置来排布的，用于筛选这些边对应的特征； |

GNN设计

在pyg的设计理念中，边是有向的，消息传递是沿着source节点到target节点，如果是无向边，需要自己设置两条边

## Experiemnt

### TorchMetrics

[quick start](https://lightning.ai/docs/torchmetrics/stable/pages/quickstart.html)

预定义指标

函数式

```python
import torch
import torchmetrics

preds = torch.randn(10, 5).softmax(dim=-1)
target = torch.randint(5, (10,))
acc = torchmetrics.functional.accuracy(preds, target, task="multiclass", num_classes=5)
```

对象式

```python
import torch
import torchmetrics

metric = torchmetrics.classification.Accuracy(task="multiclass", num_classes=5)

n_batches = 10
for i in range(n_batches):
    # simulate a classification problem
    preds = torch.randn(10, 5).softmax(dim=-1)
    target = torch.randint(5, (10,))
    # metric on current batch
    acc = metric(preds, target)
    print(f"Accuracy on batch {i}: {acc}")

# metric on all batches using custom accumulation
acc = metric.compute()
print(f"Accuracy on all data: {acc}")

# Resetting internal state such that metric ready for new data
metric.reset()
```

自定义指标

在```__init__```中定义不同batch计算结果的聚合方式，在```update```中定义每个batch的前向计算，在```compute```中定义所有batch计算完成后的后处理。

方式一：在每个batch进行指标计算，后处理时聚合

```python
from torchmetrics import Metric

class MyAccuracy(Metric):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.add_state("correct", default=torch.tensor(0), dist_reduce_fx="sum")
        self.add_state("total", default=torch.tensor(0), dist_reduce_fx="sum")

    def update(self, preds: Tensor, target: Tensor) -> None:
        preds, target = self._input_format(preds, target)
        if preds.shape != target.shape:
            raise ValueError("preds and target must have the same shape")

        self.correct += torch.sum(preds == target)
        self.total += target.numel()

    def compute(self) -> Tensor:
        return self.correct.float() / self.total
```

方式二：在每个batch保存结果，最终一起计算

```python
from torchmetrics import Metric
from torchmetrics.utilities import dim_zero_cat

class MySpearmanCorrCoef(Metric):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)
        self.add_state("preds", default=[], dist_reduce_fx="cat")
        self.add_state("target", default=[], dist_reduce_fx="cat")

    def update(self, preds: Tensor, target: Tensor) -> None:
        self.preds.append(preds)
        self.target.append(target)

    def compute(self):
        # parse inputs
        preds = dim_zero_cat(self.preds)
        target = dim_zero_cat(self.target)
        # some intermediate computation...
        r_preds, r_target = _rank_data(preds), _rank_dat(target)
        preds_diff = r_preds - r_preds.mean(0)
        target_diff = r_target - r_target.mean(0)
        cov = (preds_diff * target_diff).mean(0)
        preds_std = torch.sqrt((preds_diff * preds_diff).mean(0))
        target_std = torch.sqrt((target_diff * target_diff).mean(0))
        # finalize the computations
        corrcoef = cov / (preds_std * target_std + eps)
        return torch.clamp(corrcoef, -1.0, 1.0)
```

### Tensorboard/wandb/Swanlab

**Tensorboard**

```python
from torch.utils.tensorboard import SummaryWriter

writer = SummaryWriter(log_dir=log_dir) # 创建tensorboard的指标记录器
writer.add_scalar(loss_name, loss_val, n_iter) # 在相应的地方保存需要可视化的指标
```

端口映射与浏览器访问

```sh
 ssh -L 16006:127.0.0.1:6006 -p server_port username@server_ip # on the local PC
 tensorboard --logdir log --port=6006 # on the remote server
```

[Wandb](https://docs.wandb.ai/guides/)

[**Swanlab**](https://docs.swanlab.cn)

强烈推荐

## AI Infra for LLM

### LLM Download

Huggingface

```python
from huggingface_hub import hf_hub_download
from huggingface_hub import snapshot_download

os.environ["HF_ENDPOINT"] = "https://hf-mirror.com"
repo_id = "mistralai/Ministral-8B-Instruct-2410"
snapshot_download(
    repo_id=repo_id,
    local_dir="models/" + repo_id.split("/")[-1],
    token=token,
)
```

- 国内访问需要指定镜像源

- 部分LLM需要申请，在下载时需要传入huggingface token

  [ModelScope](https://modelscope.cn/docs/models/download)

  国内模型推荐使用该网站，速度较快

### Transformers

***AutoModel***

如果不需要调整模型结构，那么只需要使用AutoClass三件套`AutoConfig`、`AutoTokenizer`和`AutoModel`

AutoModel不会对模型架构进行限制，只是根据路径名称去查找相应的模型架构和权重

```
model = AutoModelForSequenceClassification.from_pretrained("google-bert/bert-base-cased", num_labels=5)
```

如果要实现更加定制化的操作，PLM也可以作为Backbone组件，实现**自定义结构的下游任务模型**

***Custom Model***

[创建自己的模型](https://huggingface.co/docs/transformers/en/custom_models)

- 继承PLM对应的`PreTrainedModel`，并传入同一个`Config`
- 可以使用`init_weights`，会根据config的参数来对特定的层做初始化
- 可以使用`from_pretrained`和`save_pretrain`方法

```python
class CustomConfig(PretrainedConfig):
  model_type = "icd_code_model"

class CustomModel(PreTrainedModel):

    def __init__(self, config):
        super().__init__(config)
        self.prtrained_model = ResNet(config)
				self.classifier = Linear(config.hidden_size, config.num_labels)
				self.init_weights()
        
    def forward(self, tensor, labels=None):
        hidden_states = self.prtrained_model(tensor)
        logits = self.classifier(hidden_states)
        if labels is not None:
            loss = torch.nn.cross_entropy(logits, labels)
            return {"loss": loss, "logits": logits}
        return {"logits": logits}
      
config = AutoConfig.from_pretrained(model_name_or_path, num_labels=num_labels)
tokenizer = AutoTokenizer.from_pretrained(model_name_or_path)
model = CustomModel.from_pretrained('pretrained_model', config)
```

#### 创建Custom Model

```
class CustomConfig(PretrainedConfig):
    model_type = "my_model"

class CustomModel(PreTrainedModel):
    config_class = CustomConfig
```



#### 使用AutoModel导入Custom Model

```
from transformers import AutoConfig, AutoModel
from custom_model import CustomConfig, CustomModel

AutoConfig.register("custom_model", CustomConfig)
AutoModel.register(CustomConfig, CustomModel)
```

逻辑：首先在路径下寻找config，并根据config中的model_type来寻找Config和Model的定义，从而初始化。

### Datasets

[将自己的Dataset封装成datasets库格式](https://huggingface.co/docs/datasets/v3.5.0/en/loading#python-list-of-dictionaries)

### Accelerate

- 导入并实例化`Accelerator`对象
- 去除所有的`model.to(device)`和`batch.to(device)`
- 用`accelerator.prepare`将`model, dataloader, optimizer`并行化
- 用`accelerator.backward(loss)`替代`loss.backward()`

```python
+ from accelerate import Accelerator

+ accelerator = Accelerator()

- device = torch.device("cuda")
- model.to(device)

+ train_dataloader, eval_dataloader, model, optimizer = accelerator.prepare(
+     train_dataloader, eval_dataloader, model, optimizer)

  for epoch in range(num_epochs):
      for batch in train_dataloader:
-         batch = {k: v.to(device) for k, v in batch.items()}
          outputs = model(**batch)
          loss = outputs.loss
-         loss.backward()
+         accelerator.backward(loss)

          optimizer.step()
          lr_scheduler.step()
          optimizer.zero_grad()

# 保存模型
if args.output_dir is not None and args.num_train_epochs > 0:
	accelerator.wait_for_everyone()
  unwrapped_model = accelerator.unwrap_model(model)
  unwrapped_model.save_pretrained(args.output_dir, save_function=accelerator.save)

```

基于上述代码修改，可以在命令行使用多卡或者单卡进行模型的训练

- Accelerate和tensor.to(device)不是互斥的，某些情况下仍然需要手动修改device

  在命令行指定显卡

```sh
accelerate launch --multi_gpu --gpu_ids="0,1" --num_processes=2 --num_machines=1 main.py # 单机多卡
CUDA_VISIBLE_DEVICES=0 python main.py # 单机单卡
```

### VLLM

主要针对Decoder-only的LLM的KV Cache做了加速，对Encoder-only的PLM作用不大

VLLM支持加速的模型主要分为三种：

1. VLLM原生支持，见 [vllm/model_executor/models](https://github.com/vllm-project/vllm/blob/main/vllm/model_executor/models)

2. Transformers中的模型部分支持（主要是Decoder-only）

3. Pytorch模型：需要人工编写相关代码，见[在VLLM中实现基本模型](https://docs.vllm.ai/en/latest/contributing/model/basic.html)

   多卡推理

   [Data Parallel](https://docs.vllm.ai/en/latest/getting_started/examples/data_parallel.html)

### LLaMa Factory

### VERL

## File System

[相关链接](https://cloud.tencent.com/developer/article/2148641)

### 正则匹配与路径操作

[正则](https://liaoxuefeng.com/books/python/reg-exp/index.html)

re

```python
import re

text = "Hello, my email is example@email.com"
pattern = r'\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b'

# 使用 re.findall 匹配所有符合模式的子字符串，以列表返回
matches = re.findall(pattern, text)

# 使用 re.sub 将指定模式的子串替换为新子串
redacted_text = re.sub(pattern, "REDACTED", text)

# 使用 re.split 按指定模式的子串分割文本
parts = re.split(pattern, text)

# 使用 re.search 搜索文本, 返回第一个匹配项的首尾位置
match = re.search(pattern, text)

# 使用 re.match 验证字符串是否符合模式
bool(re.match(pattern, number))
```



正则匹配，格式是字符+限定符

限定符

|       |         |
| ----- | ------- |
| ?     | 0或1个  |
| *     | 任意个  |
| +     | 1个以上 |
| {m,n} | m-n个   |

字符、元字符

|                  |                                       |
| ---------------- | ------------------------------------- |
| [a-zA-Z0-9][abc] | 小写或大写或数字a或b或c ; 否定:[^a-z] |
| a\|b\|c          | a或b或c                               |
| \w               | 单词（英文/数字/下划线）              |
| \d               | 数字                                  |
| \s               | 空格（包括换行等）                    |
| \b               | 边界 e.g.\bword\b                     |
| .                | 任意字符                              |

* [正则表达式在线测试工具](https://regex101.com/)

* 正则表达式里有转义符"\"时，应该在字符串前加“r”

  glob

  包括*、**、? 、[ ]这四个通配符。

  \*  匹配0个或多个字符  
  ** 匹配所有文件、目录、子目录和子目录里的文件  
  ? 匹配一个字符  
  [] 匹配指定范围内的字符，如[0-9]匹配数字，[a-z]匹配小写字母  

  pathlib

  操作文件路径

```python
p.name  #获取文件名  
p.suffix  #获取文件后缀
```

### 

### 工作目录与包管理

查看和更改工作目录，以及工作目录的文件情况

```python
os.getcwd(path)  #查看工作目录
os.chdir(path)  #改变工作目录
os.listdir(path)  #浏览工作目录
os.walk(path) # 遍历目录
os.mkdir(path)  #创建文件夹
os.remove(path)  #删除文件
```

### 文件的增删改查

文件/文件夹的复制、删除、重命名

```python
shutil.copy(src, dst)  #复制文件
shutil.copytree(src, dst)  #复制文件夹
shutil.move(src, dst)  #重命名文件/文件夹
shutil.rmtree(src, dst)  #删除文件夹
```

* os.remove()方法只能删除某个文件; os.mdir()只能删除某个空文件夹
  压缩

```python
shutil.make_archive(base_name, format='zip')
```

ipdb

```python
ipdb.set_trace()
```

代码会在执行到断点处时暂停，可通过vscode内置terminal查看各个变量的状态

