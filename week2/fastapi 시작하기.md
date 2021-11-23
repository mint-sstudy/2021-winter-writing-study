# FastAPI 시작하기

## FastAPI

> FastAPI는 현대적이고, 빠르며(고성능), 파이썬 표준 타입 힌트에 기초한 Python3.6+의 API를 빌드하기 위한 웹 프레임워크입니다. (FastAPI 공식 문서)

공식 문서에 따르면 FastAPI의 주요 특징으로는 **빠름**, **빠른 코드 작성**, **적은 버그**, **직관적**, **쉬움**, **짧음**, **견고함**, **표준 기반**이 있는데, 공식 문서를 읽어보며 조금 사용해 본 입장에서 가장 매력적으로 느껴졌던 특징은 **빠른 코드 작성**과 **쉬움**이었다. 그리고 엄청 가벼웠다.

추가로, FastAPI는 **비동기** 방식으로 요청을 처리한다. Python은 파이썬 스크립트(웹 어플리케이션)가 웹 서버와 통신하기 위한 인터페이스인 WSGI(Web Server Gateway Interface)를 사용한다. 하지만 WSGI는 동기 방식만 지원하기 때문에 먼저 들어온 요청에 대한 처리가 끝날 때까지 다음 요청은 계속 기다려야 한다는 단점이 있다. FastAPI는 WSGI 대신 비동기 방식인 ASGI(Asynchronous Server Gateway Interface)를 사용하기 때문에 비동기로 요청을 처리할 수 있다. 



## 왜 FastAPI를 선택했나?

새 프로젝트에 사용할 서버 프레임워크를 알아보다가 Devfest에서 만난 현직자께서 추천해 주셨던 FastAPI를 사용해 보기로 했다. Python이 제일 손에 익기도 했고, Javascript는 React 개발을 하면서 나와 정말 맞지 않는다는 걸 확실히 느꼈기 때문에 나름 최선의 선택이라고 할 수 있을 것 같다. 게다가 Django보다 가볍고 Flask보다 진화된 새로운 프레임워크라니 한 번 쯤 사용해볼만 하다고 생각했다.



## FastAPI 시작하기

> 공식 문서: https://fastapi.tiangolo.com/ko/

FastAPI를 사용하기 위해서는 Python 3.6+ 버전이 필요하다.



### 필요한 모듈 설치

```shell
pip install fastapi
```

ASGI 서버를 위해 uvicorn이나 Hypercorn을 설치한다.

```shell
pip install uvicorn[standard]
```



필요한 모듈을 다 설치했다면 아래의 코드를 담은 `main.py` 파일을 만들자.

```python
from typing import Optional
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/items/{item_id}")
def read_item(item_id: int, q: Optional[str] = None):
    return {"item_id": item_id, "q": q}
```

이 `main.py`를 실행하면 아래와 같은 메세지가 뜬다. 서버가 켜진 상태에서 http://127.0.0.1:8000에 접속하면 `{"Hello": "World"}` 의 JSON response를 볼 수 있다.



http://127.0.0.1:8000/items/13?q=hungry를 열어보면 아래와 같은 response를 볼 수 있다.

```json
{"item_id": 13, "q": "hungry"}
```

이렇게 간단한 방법으로 API를 2개나 생성해 봤다.





## Swagger

http://127.0.0.1:8000/docs로 가보면 Swagger UI가 제공하는 자동 대화형 API 문서를 확인할 수 있다. 자동으로 FastAPI에서 사용하고 있는 REST API를 문서화해주므로 협업에 용이하다.





## FastAPI에서 Request 보내기

> https://stackoverflow.com/questions/63872924/how-can-i-send-an-http-request-from-my-fastapi-app-to-another-site-api

내가 지금 만들고 있는 프로젝트 API 내에서 다른 API로 요청을 보내야 하는데, Django나 Flask였다면 `requests` 모듈을 사용해 요청을 보냈겠지만 FastAPI는 비동기 기반이므로 동기 라이브러리인  `requests`를 사용할 수 없다.

`requests`를 대신할 수 있는 비동기 라이브러리로 `httpx`와 `aiohttp`가 있다. 아래는 `httpx`를 사용해 FastAPI로 구현한 REST API에서 다른 API 서버로 요청을 보내는 예시 코드다.

```python
from fastapi import FastAPI
from time import time
import httpx
import asyncio

app = FastAPI()

URL = "http://httpbin.org/uuid"


async def request(client):
    response = await client.get(URL)
    return response.text


async def task():
    async with httpx.AsyncClient() as client:
        tasks = [request(client) for i in range(100)]
        result = await asyncio.gather(*tasks)
        print(result)


@app.get('/')
async def f():
    start = time()
    await task()
    print("time: ", time() - start)
```





사용한 지 일주일 째지만, Django로 개발했을 때보다 훨씬 더 개발 속도가 빨라진 것 같다. 단점을 꼽아보자면 Django는 디렉토리 구조를 강제하기 때문에 구조에 대해서 크게 고민하지 않아도 되지만 FastAPI는 구조를 강제하지 않는다는 거다. 공식 문서에 디렉터리 구조에 대한 조언이 나와있지만 내가 React 개발할 때 가장 신경쓰였던 부분이 디렉터리 구조였기 때문에... 프로젝트 규모가 커지면 FastAPI도 똑같은 이유로 나를 괴롭힐 것 같다. 

