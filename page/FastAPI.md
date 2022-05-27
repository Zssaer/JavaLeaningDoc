# FastAPI

## 前言

![](../picture/logo-fastApi.png)

[FastAPI](https://github.com/tiangolo/fastapi) 是一个用于构建 API 的现代、快速（高性能）的 web 框架，使用 Python 3.6+ 并基于标准的 Python 类型提示。

它拥有的关键特性:

- **快速**：可与 **NodeJS** 和 **Go** 比肩的极高性能（归功于 Starlette 和 Pydantic）。[最快的 Python web 框架之一](https://fastapi.tiangolo.com/zh/#_11)。
- **高效编码**：提高功能开发速度约 200％ 至 300％。*
- **更少 bug**：减少约 40％ 的人为（开发者）导致错误。*
- **智能**：极佳的编辑器支持。处处皆可自动补全，减少调试时间。
- **简单**：设计的易于使用和学习，阅读文档的时间更短。
- **简短**：使代码重复最小化。通过不同的参数声明实现丰富功能。bug 更少。
- **健壮**：生产可用级别的代码。还有自动生成的交互式文档。
- **标准化**：基于（并完全兼容）API 的相关开放标准：[OpenAPI](https://github.com/OAI/OpenAPI-Specification) (以前被称为 Swagger) 和 [JSON Schema](https://json-schema.org/)。

FastAPI相比于其余的Python Web框架来说，主要用于构建后端API方向，它拥有比其余的Python Web框架更好的性能。启动时间十分之快，极大提升了开发进度。并且自动拥有API文档界面，更方便的支持类型输入。

FastAPI是去年2021年Github上年度最佳新兴框架，目前在GitHub上拥有45.5K的Star，被多个国际大公司所认可，十分具有学习、使用价值。



## 入门

在学习使用FastAPI前，确保运行环境下安装Python3.6以上版本，因为FastAPI基于Python3.6+设计。

按正常流程是安装 **FastAPI** ，但是这里不建议直接在CMD中使用PIP安装。

这里 以项目形式，使用Pycharm IDE来进行学习。创建一个正常的Python项目，在项目中安装FastAPI、uvicorn，其中uvicorn是一个目前流行的异步服务器。

![](../picture/20220526165625.png)

随后在项目 `main.py`文件中输入如下内容：

```python
import uvicorn
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

if __name__ == "__main__":
    uvicorn.run(app, host="127.0.0.1", port=8000)
```

点击运行main。成功在127.0.0.1:8000下启动服务器：

```bash
INFO:     Started server process [34400]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
```

上面内容就定义了一个首页的API输出，打开浏览器、后者使用第三方工具 访问http://127.0.0.1:8000页面。

会发现成功输出了`{"message": "Hello World"}`内容，这一切都使用了RestAPI形式。

## API文档页面

前面提到了FastAPI中自动生成了API文档页面，这一切都不需要配置。

在服务器启动状态下，访问http://127.0.0.1:8000/docs 页面，你将会看到自动生成的交互式 API 文档，它使用Swagger UI形式。

![](../picture/index-01-swagger-ui-simple.png)

除了Swagger-UI文档之外，FastAPI还内置了ReDoc形式文档，进入http://127.0.0.1:8000/redoc 即可。

## 详细学习

### 启动服务器

从上面的入门中，我们就可以发现，要启动整个FastAPI服务器的话，分3大部分：

1. 创建FastAPI的实例：

   ```python
   app = FastAPI()
   ```

2. 创建对应接口：这一部分就是我们需要关注项目的主要部分之一了。

3. 在Python文件入口中，开启服务器：

   ```python
   if __name__ == "__main__":
       uvicorn.run(app, host="127.0.0.1", port=8000)
   ```

### 接口的书写

#### GET方法请求

FastAPI的接口写法和Java的Spring的Restful API写法十分相像。

```python
@app.get("/")
async def root():
    return {"message": "Hello World"}

# 动态路径的请求
@app.get("/param/{item_id}")
async def get_param(item_id: int):
    return {"itemId": item_id,"message":"This is a param content"}

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]

# 参数查询的请求
@app.get("/get")
async def get1(id: int = 10):
    return {"id": id, "data": fake_items_db[:3]}

# 可选参数的设置
# 其中Union为typing 包函数
@app.get("/get")
async def get2(id: int = 10,limit:Union[int, None] = None):
    if limit:
        return {"id": id, "data": fake_items_db[:limit]}
    return {"id": id, "data": fake_items_db[:3]}
```

上面为FastAPI中GET方法的一些使用方法。

其中`@app.get()`注解，表示其为GET方法的接口，它需要接受一个路径，用作API地址。相应的POST、DELETE、PUT都是同样类似方法。

API接口可以使用`async`异步，表示此接口为异步步骤，与不带异步关键字的 接口的使用区别：

> 当你使用到第三方库时，需要调用到它们`await`时，就需要在接口函数`def`前使用`async`，才能实现异步并发效果。
>
> 而当您正在使用与某些东西（数据库、API、文件系统等）通信并且不支持使用的第三方库`await`（目前大多数数据库库都是这种情况），然后声明您的*路径操作*正常运行，只需`def`即可。
>
> 而当你的项目中，不必与其它东西进行链接通讯时，使用`async def`可以获得最佳性能。

FastAPI在输入参数可以对其参数类型进行 规定，并且可以设置默认值。当API 输入的参数类型不一致的话，FastAPI会抛出类型错误。

```python
async def get1(id: int = 10):
    ...
```

默认情况下入参是必输内容。但利用Python3.5+类型的特性（参数类型支持2个），结合Union函数，在另一个参数类型中定义None，并且设置默认值为None，即可将其参数设置为可选参数。

```python
async def get2(id: int = 10,limit:Union[int, None] = None):
    ...
```

除了查询参数的可选设置，还可以设置响应的检验：

```python
from fastapi import FastAPI, Query

@app.get("/items/")
async def read_items(q: Union[str, None] = Query(default=None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

​	这里的`q: Union[str, None] = Query(default=None, max_length=50)`，表示这个参数时一个可选参数并且最大长度为50。除了Query中有 max_length外，还拥有最小值检验`min_length`。

也可以在正则表达式检验：

```python
q: Union[str, None] = Query(
        default=None, regex="^fixedquery$"
)
```

上述Query中都带有default，表明这个是可选输入参数。对于必须参数的话，不要带有default：

```python
q: str = Query(min_length=3)
```

除此之外，Query还支持下面这些内容定义：

* default：参数的默认值。
* title：参数的标题。用作在 OpenAPI 和自动 API 文档用户界面中作为 API 的标题/名称使用。
* description：参数的说明。用作在 OpenAPI 和自动 API 文档用户界面中对该参数的描述。
* gt：要求参数大于这个值，必须为数字。



#### POST方法请求

如上所描述，FastAPI的POST方法请求使用的`@app.post()`注解。同样PUT方法使用`@app.put()`注解。

```python
@app.post("/items/")
async def create_item(name: str,description: Union[str, None] = None):
    ...
    return ...
```

#### 请求模型

在项目中通常是将请求内容封装为请求模型，当做请求的模型。

要将其请求参数组装为模型的话，这个请求模型要继承`pydantic` 的 `BaseModel`。

```python
from typing import Union

from fastapi import FastAPI
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None

app = FastAPI()

@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
```

在请求函数内部，还可以利用`dict`函数提取到对应dict，然后使用update可以向提交的请求体中添加内容。

**注意：你不能使用 `GET` 操作（HTTP 方法）发送请求体。**

要发送数据，你必须使用下列方法之一：`POST`（较常见）、`PUT`、`DELETE` 或 `PATCH`。



对于同时请求链接中拥有路径参数时，FastAPI 将识别出与路径参数匹配的函数参数应**从路径中获取**，而声明为 Pydantic 模型的函数参数应**从请求体中获取**。

```python
class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None

app = FastAPI()

@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item):
    return {"item_id": item_id, **item.dict()}
```

我们除了定义请求模型之外，通常情况下还需要对每个参数在接口文档中进行说明，否则接口文档中的参数很难被其他人所明白。

对请求模型的参数使用 Pydantic 的 `Field` 即可对参数进行声明校验和元数据等。

```python
class Item(BaseModel):
    name: str
    description: Union[str, None] = Field(
        default=None, title="说明", max_length=300
    )
    price: float = Field(gt=0, description="这个金额必须大于0!")
    tax: Union[float, None] = None
```

使用Fileld就和前面提到的Query一样。

#### GET查询参数拼装

前面提到了创建BaseModel作为请求体可以大幅减少请求重复使用的问题，以及规范化请求。但是也提到了只能使用到POST相关的请求接口上，GET相关请求无法使用。

对于GET请求的请求体创建使用，官方称之为使用依赖注入机制。它有2种实现方式：

* 第一种实现方法：依赖项就是一个函数，且可以使用与*路径操作函数*相同的参数，它返回对应的GET查询请求的内容：

  ```python
  async def common_parameters(
      q: Union[str, None] = None, skip: int = 0, limit: int = 100
  ):
      return {"q": q, "skip": skip, "limit": limit}
  
  
  @app.get("/items/")
  async def read_items(commons: dict = Depends(common_parameters)):
      return commons
  ```

  这里创建了一个`common_parameters`函数当做依赖项。在请求接口的输入参数中输入dict，使用Depends函数。

  这样这个请求接口输入参数就会有 依赖项返回的内容了。

* 第二种实现方法：创建一个请求类，继承BaseModel，内部参数遵循查询参数写法：

  ```python
  class Student(BaseModel):
      name: str
      age: Union[int, None] = None
  
  
  @app.get(&quot;/&quot;)
  def read_root(student: Student = Depends()):
      return {&quot;name&quot;: student.name, &quot;age&quot;: student.age}是
  ```

  直接在GET请求入参下输入对应请求类，默认值上使用`Depends()`。这样既可将其请求类以查询参数方式进行工作。













