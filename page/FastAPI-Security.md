# FastAPI-Security

FastAPI提供了多种安全认证方式。其中最为推荐的是  使用OAuth2。

您可以直接将 OAuth2 范围与**FastAPI**一起使用，它被集成以无缝工作。

## OAuth2

下面是一个创建Security案例：

```python
SECRET_KEY = "09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def verify_password(plain_password, hashed_password):
    """
  	验证密码
  	"""
    return pwd_context.verify(plain_password, hashed_password)


def authenticate_user(username: str, password: str, db: Session):
    """
    对比密码
    """
    user = get_user_by_name(db, username)
    if not user:
        return False
    if not verify_password(password, user.hash_pwd):
        return False
    return user
    
def create_access_token(data: dict,
                        expires_delta: Union[timedelta, None] = None):
    to_encode = data.copy()
    if expires_delta:
        expire = datetime.utcnow() + expires_delta
    else:
        expire = datetime.utcnow() + timedelta(minutes=15)
    to_encode.update({"exp": expire})
    encoded_jwt = jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)
    return encoded_jwt
    
@app.post("/token", response_model=Token)
async def login(form_data: OAuth2PasswordRequestForm = Depends(), db: Session = Depends(get_db)):
    user = authenticate_user(form_data.username, form_data.password, db)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect username or password",
            headers={"WWW-Authenticate": "Bearer"},
        )
    access_token_expires = timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    access_token = create_access_token(
        data={"sub": user.username}, expires_delta=access_token_expires
    )
    return {"access_token": access_token, "token_type": "bearer"}
```

这里验证密码登录后，生成JWT作为登录口令。

对于认证登录操作的话，主要是创建一个依赖项：

```python
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

async def get_current_user(
                           token: str = Depends(oauth2_scheme),
                           db: Session = Depends(get_db)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": authenticate_value},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username)
    except JWTError:
        raise credentials_exception
    user = get_user_by_name(db, token_data.username)
    if user is None:
        raise credentials_exception
    return user


async def get_current_active_user(
        current_user=Depends(get_current_user)):
    return User.from_orm(current_user)
```

对于需要认证登录身份操作的路径，只需要在路径中加入依赖项即可：

```python
@app.get("/users/me")
async def read_users_me(current_user: user.User = Depends(get_current_active_user)):
    return current_user
```

## 带有Scop范围认证的OAuth2

对于更加高级的、更加精密的环境系统，需要根据用户身份来允许不同身份使用不同路径接口。这种情况还需要使用带有Scop范围控制的OAuth2：

```python
# 在login路径中
@app.post("/token", response_model=Token)
async def login(form_data: OAuth2PasswordRequestForm = Depends(), db: Session = Depends(get_db)):
    ...
    access_token = create_access_token(
        data={"sub": user.username, "scopes": user.scopes.split(',')}, expires_delta=access_token_expires
    )
    ...
```

在JWT的载体中额外加入一个scopes的内容，用作记载用户的相关权限身份。

```python
# 在get_current_user依赖项中
async def get_current_user(security_scopes: SecurityScopes,
                           token: str = Depends(oauth2_scheme),
                           db: Session = Depends(get_db)):
    if security_scopes.scopes:
        authenticate_value = f'Bearer scope="{security_scopes.scope_str}"'
    else:
        authenticate_value = f"Bearer"
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": authenticate_value},
    )
     try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        token_scopes = payload.get("scopes", [])
        if username is None:
            raise credentials_exception
        token_data = TokenData(username=username, scopes=token_scopes)
    except JWTError:
        raise credentials_exception
    user = get_user_by_name(db, token_data.username)
    if user is None:
        raise credentials_exception

    for scope in security_scopes.scopes:
        if scope not in token_data.scopes:
            raise HTTPException(
                status_code=status.HTTP_401_UNAUTHORIZED,
                detail="Not enough permissions",
                headers={"WWW-Authenticate": authenticate_value},
            )
    return user
```

在认证依赖项中，我们额外添加了Scopes的相关内容，对路径需要拥有的范围与用户的范围进行判断，从而来进行控制用户的访问。

````python
@app.get("/users/me",dependencies=[Security(get_current_active_user, scopes=['me', 'item'])])
async def read_users_me():
    return current_user
````

在需要认证的路径中使用`Security`函数，并在传入认证依赖项后又传入额外的要求范围，当用户的权限范围全部满足这些范围时才能正常访问接口，否则抛出权限不足错误。

## 普通的HTTP身份认证

对于简单的请求下，可以直接只使用普通的HTTP身份认证功能。

当您第一次尝试打开 URL（或单击文档中的“执行”按钮）时，浏览器会询问您的用户名和密码。

```python
security = HTTPBasic()

def get_current_username(credentials: HTTPBasicCredentials = Depends(security)):
    correct_username = secrets.compare_digest(credentials.username, "stanleyjobson")
    correct_password = secrets.compare_digest(credentials.password, "swordfish")
    if not (correct_username and correct_password):
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Incorrect email or password",
            headers={"WWW-Authenticate": "Basic"},
        )
    return credentials.username


@app.get("/users/me")
def read_current_user(username: str = Depends(get_current_username)):
    return {"username": username}
```

