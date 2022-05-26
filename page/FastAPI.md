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







