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

##### 声明效验

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

##### 高级类型定义

在创建请求体时，除了使用常规的基本类型外，还可以使用List类型、Set类型：

```python
class Item(BaseModel):
    tags1: List[str] = []
    tags2: Set[str] = set()
    tags3: Dict[int, float] = {}
```

不只是请求体，对于请求函数的单独入参也可以这样声明。

还可以接受另外一个模型，用来嵌套：

```python
class Image(BaseModel):
    url: str
    name: str


class Item(BaseModel):
    name: str
    image: Union[Image, None] = None
```

#### 声明示例

对于接口文档，我们可以对请求体的内容声明一个示例，用作在接口文档上示例。

只需要在请求体模型内定义一个`Config`类，在其`schema_extra`中定义“example”：

```python
class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: Union[float, None] = None

    class Config:
        schema_extra = {
            "example": {
                "name": "Foo",
                "description": "A very nice Item",
                "price": 35.4,
                "tax": 3.2,
            }
        }
```





#### GET查询的请求体参数拼装

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
  
  
  @app.get("/")
  def read_root(student: Student = Depends()):
      return {&quot;name&quot;: student.name, &quot;age&quot;: student.age}是
  ```

  直接在GET请求入参下输入对应请求类，默认值上使用`Depends()`。这样既可将其请求类以查询参数方式进行工作。
  
#### PUT更新

  更新数据请用 [HTTP `PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 操作。

把输入数据转换为以 JSON 格式存储的数据（比如，使用 NoSQL 数据库时），可以使用 `jsonable_encoder`。例如，把 `datetime` 转换为 `str`。

```python
@app.put("/items/{item_id}", response_model=Item)
async def update_item(item_id: str, item: Item):
    update_item_encoded = jsonable_encoder(item)
    items[item_id] = update_item_encoded
    return update_item_encoded
```





### 数据类型

FastAPI利用Pydantic来实现类型检验。它支持多种数据类型，其中数据有一些常用的数据类型：

* int
* float
* str
* bool

除此之外，还有众多其他数据类型：

* UUID：一种标准的 "通用唯一标识符" ，在许多数据库和系统中用作ID。在请求和响应中将以 `str` 表示。
* datetime.datetime：在请求和响应中将表示为 ISO 8601 时间格式的 `str` ，比如: `2008-09-15T15:53:00+05:00`。
  * datetime.date：在请求和响应中将表示为 ISO 8601 格式的 `str` ，比如: `2008-09-15`。
* Decimal：在请求和相应中被当做 `float` 一样处理。

更多额外的内置数据类型，可查询Pydantic文档https://pydantic-docs.helpmanual.io/usage/types/

### 请求头部

要实现对FastAPI接口的请求头部进行发送相关内容的话，需要导入fastapi包下的Header函数。

在接口参数下，定义需要对头部定义参数即可，如下：

```python
from typing import Union
from fastapi import FastAPI, Header

@app.get("/header/")
async def read_items(admin_token: Union[str, None] = Header(default=None)):
    return {"admin-token": admin_token}
```

注意：大多数标准的headers用 "连字符" 分隔，也称为 "减号" (`-`)。

但是像 `user-agent` 这样的变量在Python中是无效的。

因此, 默认情况下, **`Header` 将把参数名称的字符从下划线 (`_`) 转换为连字符 (`-`) 来提取并记录 headers。**

同时，HTTP headers 是大小写不敏感的，因此，因此可以使用标准Python样式(也称为 "snake_case")声明它们。因此，您可以像通常在Python代码中那样使用 `user_agent` ，而不需要将首字母大写为 `User_Agent` 或类似的东西。



### 响应模型

前面我们创建了请求模型，它是用作封装请求接口的。

而对于有些响应返回的类型我们在项目中通常也会将其封装，称为响应模型。

它的创建方法和请求模型一样，都是继承与BaseModel类。

```python
class ResultItem(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: float = 10.5
```

当它直接被用作返回时，也不会被有检验效果。需要在对应请求接口注解上的response_model进行配置：

```python
@app.post("/items/", response_model=Item)
async def create_item(xxx:xxx):
	...
	# item是一个Item类的实例对象
    return item
```

有时，为了简便，通常用一个BaseModel既做 请求模型 又做 响应模型。但出现一个隐私问题，比如某个BaseModel中存在一些隐私数据的话，像用户注册时输入的密码，直接返回整个相同的BaseModel是十分不安全的。

对于隐私数据，可以在接口请求注解上使用`response_model_exclude`来排除某个指定的参数：

```python
class Item(BaseModel):
    name: str
    description: Union[str, None] = None
    price: float
    tax: float = 10.5


items = {
    "foo": {"name": "Foo", "price": 50.2},
    "bar": {"name": "Bar", "description": "The Bar fighters", "price": 62, "tax": 20.2},
    "baz": {
        "name": "Baz",
        "description": "There goes my baz",
        "price": 50.2,
        "tax": 10.5,
    },
}

@app.get("/items/{item_id}/public", response_model=Item, response_model_exclude={"tax"})
async def read_item_public_data(item_id: str):
    return items[item_id]
```

返回的响应数据不一定需要相同类，FastAPI的响应模型只是输出内部对应的数据：

```python
class UserIn(BaseModel):
    username: str
    password: str
    full_name: Union[str, None] = None


class UserOut(BaseModel):
    username: str
    full_name: Union[str, None] = None


@app.post("/user/", response_model=UserOut)
async def create_user(user_in: UserIn):
    return user_in
```

上述返回的类型在函数内部是一个 UserIn对象结构，但是返回结果为 UserOut对象结构。

### 表单数据请求

在有时接口接收的不是Json数据，而是Form表单数据，或者上传文件的接口，这时需要用到Form配置。

要使用表单配置，需预先安装 [`python-multipart`](https://andrew-d.github.io/python-multipart/)。

```python
from fastapi import FastAPI, Form

@app.post("/login/")
async def login(username: str = Form(), password: str = Form()):
    return {"username": username}
```

创建表单（`Form`）参数的方式与 `Body` 和 `Query` 一样。

例如，OAuth2 规范的 "密码流" 模式规定要通过表单字段发送 `username` 和 `password`。该规范要求字段必须命名为 `username` 和 `password`，并通过表单字段发送，不能用 JSON。使用 `Form` 可以声明与 `Body` （及 `Query`、`Path`、`Cookie`）相同的元数据和验证。

可在一个*路径操作*中声明多个 `Form` 参数，但不能同时声明要接收 JSON 的 `Body` 字段。因为此时请求体的编码是 `application/x-www-form-urlencoded`，不是 `application/json`。

> 表单数据的「媒体类型」编码一般为 `application/x-www-form-urlencoded`。
>
> 但包含文件的表单编码为 `multipart/form-data`。

### 上传文件请求

要实现上传文件的请求，和表单数据请求一样，需要需预先安装 [`python-multipart`](https://andrew-d.github.io/python-multipart/)。

从 `fastapi` 导入 `File` 和 `UploadFile`：

```python
from fastapi import FastAPI, File, UploadFile

app = FastAPI()


@app.post("/files/")
async def create_file(file: bytes = File()):
    return {"file_size": len(file)}


@app.post("/uploadfile/")
async def create_upload_file(file: UploadFile):
    return {"filename": file.filename}
```

声明文件体必须使用 `File`，否则，FastAPI 会把该参数当作查询参数或请求体（JSON）参数。

其中 `File()`文件作为「表单数据」上传。如果把*路径操作函数*参数的类型声明为 `bytes`，**FastAPI** 将以 `bytes` 形式读取和接收文件内容。**这种方式把文件的所有内容都存储在内存里，适用于小型文件。**

而 `UploadFile`则是使用`spooled` 文件，它在存储到内存的文件超出最大上限时，FastAPI 会把文件存入磁盘，所以UploadFile 能上传更大文件，并且它可获取上传文件的元数据。

大多数情况下，`UploadFile` 更好使用。

`UploadFile` 的属性有如下：

- `filename`：上传文件名字符串（`str`），例如， `myimage.jpg`；
- `content_type`：内容类型（MIME 类型 / 媒体类型）字符串（`str`），例如，`image/jpeg`；
- `file`： [`SpooledTemporaryFile`](https://docs.python.org/zh-cn/3/library/tempfile.html#tempfile.SpooledTemporaryFile)（ [file-like](https://docs.python.org/zh-cn/3/glossary.html#term-file-like-object) 对象）。其实就是 Python文件，可直接传递给其他预期 `file-like` 对象的函数或支持库。



`UploadFile` 支持以下 `async` 方法，（使用内部 `SpooledTemporaryFile`）可调用相应的文件方法：

- `write(data)`：把 `data` （`str` 或 `bytes`）写入文件；
- `read(size)`：按指定数量的字节或字符（`size` (`int`)）读取文件内容；
- `seek(offset)`：移动至文件 `offset` （`int`）字节处的位置；
  - 例如，`await myfile.seek(0)` 移动到文件开头；
  - 执行 `await myfile.read()` 后，需再次读取已读取内容时，这种方法特别好用；
- `close()`：关闭文件。

上述方法都是 `async` 方法，要搭配「await」使用。

例如，在 `async` *路径操作函数* 内，要用以下方式读取文件内容：

```
contents = await myfile.read()
```

在普通 `def` *路径操作函数* 内，则可以直接访问 `UploadFile.file`，例如：

```
contents = myfile.file.read()
```

#### 多文件上传

FastAPI 支持同时上传多个文件。

对于多文件上传，将其封装到List内即可：

```python
@app.post("/files/")
async def create_files(files: List[bytes] = File()):
    return {"file_sizes": [len(file) for file in files]}


@app.post("/uploadfiles/")
async def create_upload_files(files: List[UploadFile]):
    return {"filenames": [file.filename for file in files]}
```

### 抛出错误

#### 抛出普通浏览器错误

当接口出现输入情况下，往往会出现响应的错误状态，如404Not Find等等。

我们可以手动抛出对应的错误状态，只需要使用导入fastapi下的 `HTTPException`：

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()

items = {"foo": "The Foo Wrestlers"}


@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(status_code=404, detail="Item not found")
    return {"item": items[item_id]}
```

其中`status_code`下可以设置响应的浏览器错误码，`detail`则是错误时返回的Json内容，它接受的是任何可以被Json格式化的数据类型，不只是str。

#### 抛出自定义错误

大多数情况下，在项目中都是抛出自定义的错误，而不是浏览器错误。

对此需要先定义一个自定义错误模型：

```python
class UnicornException(Exception):
    def __init__(self, name: str):
        self.name = name
```

fastApi需要定义一个全局错误拦截器，捕捉到这个定义的自定义错误，并进行相应处理：

```python
from fastapi.responses import JSONResponse

@app.exception_handler(UnicornException)
async def unicorn_exception_handler(request: Request, exc: UnicornException):
    return JSONResponse(
        status_code=418,
        content={"message": f"Oops! {exc.name} did something. There goes a rainbow..."},
    )
```

上述捕捉到自定义错误后返回了一个指定格式的JSON，并设置了状态码为418。其中上述的Request、JSONResponse都是可以使用starlette包下的内容，它会fastapi中的内容一样。

最后当接口函数中抛出这个自定义异常即可触发相应处理：

```python
@app.get("/unicorns/{name}")
async def read_unicorn(name: str):
    if name == "yolo":
        raise UnicornException(name=name)
    return {"unicorn_name": name}
```



### 路径操作配置

在接口请求注解上，除了定义必须的 请求路径外，还可以配置各种信息，作用于接口。

#### 返回状态

`status_code` 用于定义*路径操作*响应中的 HTTP 状态码。可以直接传递 `int` 代码， 比如 `404`。也可以使用`status` 的快捷常量：

```python
from fastapi import status

@app.post("/items/", response_model=Item, status_code=status.HTTP_201_CREATED)
```

状态码在响应中返回，并会被添加到 OpenAPI 概图。

**FastAPI** 的`fastapi.status` 和 `starlette.status` 一样，只是快捷方式。

#### 接口分类

在接口设置`tags` 参数，对接口进行分类，规范化接口文档。

`tags` 参数的值是由 `str` 组成的 `list` （一般只有一个 `str` ）。

```python
@app.post("/items/", response_model=Item, tags=["items"])
```

![](../picture/20220531.png)

#### 接口说明

使用`summary`、`description`可以对接口进行声明。

```python
@app.post(
    "/items/",
    response_model=Item,
    summary="Create an item",
    description="Create an item with all the information, name, description, price, tax and a set of unique tags",
)
```

`summary`： 接口标题 、`description`：接口说明

当其中description说明描述非常长的时候，可以考虑使用`docstring`，它支持多段文字，以及支持 [Markdown](https://en.wikipedia.org/wiki/Markdown)，能正确解析和显示 Markdown 的内容，但要注意文档字符串的缩进。它的用法就是在函数内容开头以三个引号开始“”“，三个引号结束，中间进行描述。

```python
@app.post("/items/", response_model=Item, summary="Create an item")
async def create_item(item: Item):
    """
    Create an item with all the information:

    - **name**: each item must have a name
    - **description**: a long description
    - **price**: required
    - **tax**: if the item doesn't have tax, you can omit this
    - **tags**: a set of unique tag strings for this item
    """
    return item
```

![](../picture/202205312.png)

### Json转换

在某些情况下，您可能需要将数据类型（如 Pydantic 模型）转换为与 JSON 兼容的类型（如`dict`、`list`等）。

FastApi内置了一个JSON转换器 - `jsonable_encoder()`，使用它可以将其自己的数据类型转换为与JSON兼容的数据类型。它的作用是将其数据内部的不可转换的数据类型（比如datetime）转换为Str格式。

```python
from fastapi.encoders import jsonable_encoder

@app.put("/items/{id}")
def update_item(id: str, item: Item):
    json_compatible_item_data = jsonable_encoder(item)
    fake_db[id] = json_compatible_item_data
```

它不会直接转换为JSON的字符串，而是转换为list、dict这种兼容JSON的数据类型。





































