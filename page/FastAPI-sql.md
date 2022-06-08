# FastAPI中数据库操作

FastAPI官方推荐利用[SQLAlchemy](https://www.sqlalchemy.org/)进行数据库操作。

 SQLAlchemy 支持操作任何数据库，例如：

- PostgreSQL
- MySQL
- SQLite
- Oracle
- 微软 SQL Server 等

## 操作方法

要实现对数据库操作，一种在Python常见的模式是使用“ORM”：一个“对象关系映射”。

相当于在项目中创建一个数据库表实体模型 与一个响应模型。

在FastAPI中 数据库表实体模型使用传统的SQLAlchemy模型即可，而响应模型则使用Pydantic模型。

下面来进行一个案例：

 利用FastAPI操作Mysql：

### 第一步-创建SQLAlchemy配置

在项目中创建一个`database.py`文件，用作SQLAlchemy配置，内容如下：

```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "mysql+pymysql://root:000000@127.0.0.1:3306/fast-api?charset=utf8"

engine = create_engine(
    SQLALCHEMY_DATABASE_URL, pool_pre_ping=True
)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```

其中`SQLALCHEMY_DATABASE_URL` 配置了 一个pymysql的连接。

pymysql的连接规则如下：

```
mysql+pymysql://<username>:<password>@<host>/<dbname>[?<options>]
```

其中<username>为Mysql用户名，<password>为Mysql用户密码，<host>和<dbname>为Mysql地址、数据库名称。[?<options>]为额外配置。具体内容根据Mysql的实际情况填写。

使用`create_engine`函数创建一个`engine`，这里我们除了接受了数据库地址之外，额外添加了一个`pool_pre_ping`属性，表示每次连接从池中检测，如果有错误，监测为断开的状态，连接将被立即回收。

`SessionLocal`为数据库连接，这里只是进行初始化，只有当实例化后才会被真正创建。

`Base` 稍后我们将从这个类用作继承来创建每个SQLAlchemy模型。

### 第二步-创建SQLAlchemy模型（ORM模型）

这一步我们在项目中创建SQLAlchemy模型，它是用来和数据库进行通讯的模型，它的设计将会与数据库的表一一对应。

这里我们在项目中创建一个`models.py`文件，它里面用来存放各种SQLAlchemy模型。这里我们就以user为例：

```python
from datetime import datetime

from sqlalchemy import Column, String, DateTime, Integer

from database import Base


class User(Base):
    __tablename__ = 'user'  # 数据库表名

    id = Column(Integer, primary_key=True, index=True)
    username = Column(String(255), unique=True, index=True)
    hash_pwd = Column(String(255), nullable=False)
    create_time = Column(DateTime, default=datetime.now)
```

其中SQLAlchemy模型 继承于 刚刚创建的`database.py`中的Base类。

使用`__tablename__`属性定义 该模型在数据库对应的表名。

而SQLAlchemy模型内部的属性代表着数据库的表的列，使用Column函数来进行对类的数据定义。

### 第三步-创建Pydantic模型（响应模型）

在创建完SQLAlchemy模型后，我们接着创建一个 Pydantic模型，它又被称为`schemas`，主要是作用在请求、响应上。

我们在项目中创建一个`schemas.py`文件，它里面用来存放各种Pydantic模型。这里我们就以user为例：

```python
import datetime

from pydantic import BaseModel


class User(BaseModel):
    id: int
    username: str
    hash_pwd: str
    create_time: datetime.datetime

    class Config:
        orm_mode = True


class UserCreate(BaseModel):
    username: str
    pwd: str
```

FastAPI基础中，提到了Pydantic模型创建，这里就不多论述了。

这里我们不仅仅创建了一个User的Pydantic模型，还创建了一个UserCreate的Pydantic模型，后者主要用作创建的请求上，它省略了一些不必要的字段。

**注意，这里官方也提到了关于两者的写法区别，Pydantic模型使用":"来进行定义类型，而SQLAlchemy模型使用“=”和Column来定义的，而且它们两者的定义类型也是不一样的。这是需要特别注意的。**

### 第四步-定义操作层

这一步定义一个专门操作User这个模型（主要是SQLAlchemy模型）的数据库相关操作的文件。

官方将其定义为一个工具类，当然我认为理解为建立一个MVC中的Dao层更方便。当然这只是Web通用设计，不代表绝对的要求，对于小操作，你甚至可以将其放到视图层内（不推荐）。

创建一个`user_dao.py`，内容如下：

```python
from sqlalchemy.orm import Session

from . import models, schemas


def get_user_by_id(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


def create_user(db: Session, user: userSchema.UserCreate):
    if db.query(models.User).filter(models.User.username == user.username).first():
        return 0
    fake_hashed_password = user.pwd + "notreallyhashed"
    new_item = models.User(username=user.username, hash_pwd=fake_hashed_password)
    db.add(new_item)
    db.commit()
    db.refresh(new_item)
    return new_item


def get_all_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()
```

上面内容 我们分别定义了 按id查询用户、创建用户、查询全部用户。

这些操作都是SQLAlchemy的操作，这里就不一一论述了。

### 第五步-创建FastAPI主程序

这一步我们创建FastAPI主程序，并且创建一系列的请求路径（当然这在规范的中大型项目通常使用Router进行设置路径请求）。

创建`main.py`

```python
models.Base.metadata.create_all(bind=engine)

app = FastAPI()


# Dependency
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()


@app.post("/users/", response_model=schemas.User)
def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    db_user = user_dao.create_user(db, user)
    if db_user == 0:
        raise HTTPException(status_code=400, detail="用户名已经存在!")
    return db_user


@app.get("/users/", response_model=List[schemas.User])
def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    users = user_dao.get_all_users(db, skip=skip, limit=limit)
    return users
```

这里的`models.Base.metadata.create_all(bind=engine)` 表示对数据库进行初始化，当数据库中不存在这个表时，执行新增操作将会自动创建这个表（就不想要在手动创建对应的表了）。

这里定义的`get_db`依赖项，当每次应用到该依赖项时将自动打开数据库连接池，操作结束后再关闭，这功归功于`yield`。

将它应用到每个请求上的db Session类上，就会自动实现对数据库连接打开、关闭操作。

#### 补充：使用中间件代替`yield`依赖

除了使用`yield`的依赖项来进行自动打开、关闭数据库连接之外，还有一个使用 中间件方法：

```python
@app.middleware("http")
async def db_session_middleware(request: Request, call_next):
    response = Response("Internal server error", status_code=500)
    try:
        request.state.db = SessionLocal()
        response = await call_next(request)
    finally:
        request.state.db.close()
    return response
```

它主要是利用reqeust中的state进行存储数据库会话对象。

相比于`yield`依赖项的方法，这种方法缺陷很多。

- 它需要更多的代码并且更复杂一些。
- 中间件必须是一个async函数。
  - 其中有代码必须“等待”网络，它可能会在那里“阻止”您的应用程序并稍微降低性能。
  - 尽管这里的`SQLAlchemy`工作方式可能不是很成问题。
  - 但是，如果您向等待大量I/O的中间件添加更多代码，则可能会出现问题。
- 每个请求都会运行一个中间件。
  - 因此，将为每个请求创建一个连接。
  - 即使处理该请求的*路径操作*不需要数据库。

中间件这种方法其实是之前FastAPI旧版本的使用的方法，对于新版本往往推荐使用`yield`依赖项方法。

### 运行测试

五步下来后，案例项目就大功告成，使用Uvicorn 服务器打开这个main即可。

```shell
$ uvicorn main:app --reload
```

当然嫌麻烦就直接在main文件上加入Uvicorn 运行代码：

```python
if __name__ == "__main__":
    uvicorn.run(app, host="127.0.0.1", port=8000)
```















