---
title: API Reference

language_tabs:
  - shell
  - http
  - javascript
  - python

toc_footers:
  - <a href='https://github.com/slatedocs/slate'>Slate on GitHub</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Project 1 # API 문서 관리

[Home](/)

## 0. Slate 설치

Provided in the slate repo is a Dockerfile you can use to run slate using [Docker](https://www.docker.com/), as well as providing pre-built images on [Docker Hub](https://hub.docker.com/r/slatedocs/slate). Docker is similar to Vagrant in that it provides a reproducible, portable development environment using virtualization, however it does not provide a full VM, rather piggy backing off the host, allowing for a slimmer installation profile than Vagrant / full VMs. However, Docker does come with a number of its own terms, and for beginners, we recommend looking at
[this Glossary](https://docs.microsoft.com/en-us/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
to familiarize yourself with some of them.

### 1) Dependencies

* [Docker](https://www.docker.com/), see [this page](https://www.docker.com/get-started) for installing Docker Desktop.

### 2) Getting Started

1. Fork this repository on Github.
2. Clone *your forked repository* (not our original one) to your hard drive with `git clone https://github.com/YOURUSERNAME/slate.git`
3. `cd slate`
4. Grab the slate image (`docker pull slatedocs/slate`) or build the docker image for the repository (`docker build . -t slatedocs/slate`).

Note: If you are using the pre-built images for Slate, you may wish to remove all files other than the `source` directory from your repository.

### 3) Building Slate

To use Docker to just build your site, run:

```
docker run --rm --name slate -v $(pwd)/build:/srv/slate/build -v $(pwd)/source:/srv/slate/source slatedocs/slate build
```

After this command completes, you should see the built artifacts for your site in the `$(pwd)/build` directory, which you can then statically serve for your website.

_Note_: You may omit the final `build` argument and get the same result. By default, if given no command, the Dockerfile will run `build`.

### 4) Running Slate

If you wish to run the development server for Slate to aid in working on the site, run:

```
docker run --rm --name slate -p 4567:4567 -v $(pwd)/source:/srv/slate/source slatedocs/slate serve
```

and you will be able to access your site at http://localhost:4567 until you stop the running container process.

### 5) Advanced Usage

The provided Docker images contain the full Slate environment and all related files. This means that you may
provide as much or as little as you want into the container while utilizing defaults. For example, if you only
wanted to include your markdown sources and not overwrite the default JS, CSS, etc. you may wish to use the
following mount options:

```
-v $(pwd)/source/index.html.md:/srv/slate/source/index.html.md -v $(pwd)/source/includes:/srv/slate/source/includes
```

## 1. 질의 검색 API

### 1.1 일반 질의 검색 API

### 1) API 설명

- 질의에 대한 답변을 검색하는 API
- 입력 창에 텍스트를 입력한 내용에 대해 질의 검색 시 사용한다.

### 2) API 정보

| 구분         | 설명                                     |
|--------------|------------------------------------------|
| **url**      | `http://{API Gateway Server}/api/v1/talk/` |
| **Method**   | `POST`                                   |
| **Content Type** | `JSON`                                |

### 3) API 요청 파라미터

> API 요청 예시

```json
{
    "chatbot_id": "9c771a76-4305-11ee-8119-4f7fd8f839bc",
    "session_id": "111a76-4305-11ee-8119-4f7fd8f839bc",
    "data": {
        "text": "회원가입 방법 알려줘",
        "type": "TALK",
        "service": "S"
    }
}
```

| 파라미터    | 타입     | 필수 | 설명                                                                  |
|-------------|----------|------|-----------------------------------------------------------------------|
| chatbot_id  | String   | Y    | 챗봇 고유 id (10자리 이상) ex) `9c771a76-4305-11ee-8119-4f7fd8f839bc`  |
| session_id  | String   | Y    | 세션 ID ex) `9c771a76-4305-11ee-8119-4f7fd8f839bc`                   |
| data        | Object   | Y    | 세부 요청 데이터                                                       |

### 4) API 요청 Object 파라미터

| 파라미터 | 타입   | 필수 | 설명                                                   |
|----------|--------|------|--------------------------------------------------------|
| text     | String | Y    | 질의 내용                                              |
| type     | String | Y    | 질의 유형 (질의 유형 코드 참조), 대문자로 작성, 예: `TALK` |
| service  | String | N    | 서비스 유형 (S : 실제 서비스, T: Test), 기본값 : `S`, 대문자로 작성 |

### 5) API 질의 유형(type) 코드

| 질의 유형 코드 | 설명                                         |
|----------------|----------------------------------------------|
| TALK           | 입력 창에 입력한 Text에 대한 검색            |
| WELCOME        | 인사말 검색                                  |
| ANSWER         | intent_code를 통한 답변 검색                 |
| SELECT         | 여러 select item에서 사용자가 입력한 Text가 가장 유사한 항목에 대한 답변 검색 |

### 6) API 결과 파라미터

> api 결과 예시

```json
{
    "code": 200,
    "result": "success",
    "contents": {
        "text": "회원가입 방법 알려줘",
        "type": "TALK",
        "confidence": 0.887777,
        "intent_code": "98",
        "intent_name": "회원가입",
        "total": 2,
        "answers": [
            {
                "answer_content": "답변 내용 1"
            },
            {
                "answer_content": "답변 내용 2"
            }
        ]
    }
}
```

| 파라미터 | 타입   | 필수 | 설명                                          |
|----------|--------|------|-----------------------------------------------|
| code     | Number | Y    | 결과 코드 (HTTP status code 체계와 동일) ex) 200 |
| result   | String | Y    | API 실행 결과 (성공 : `success`, 실패 : `fail`) |
| contents | Object | Y    | API 결과에 대한 세부 정보                      |

### 7) API 성공 결과 Object 파라미터

| 파라미터   | 타입   | 필수 | 설명                                                        |
|------------|--------|------|-------------------------------------------------------------|
| text       | String | Y    | 질의 내용                                                   |
| type       | String | Y    | 질의 유형 (질의 유형 코드 참조), 대문자로 작성             |
| confidence | Number | Y    | 유사도 수치 ex) `0.8787292`                                 |
| intent_code| String | Y    | 대화 sequence number                                        |
| intent_name| String | Y    | 대화명 ex) `회원가입`                                       |
| total      | Number | Y    | 답변 수                                                     |
| answers    | Array  | Y    | 답변 Database에 저장된 답변 내용                            |

### 8) API 실패 결과 Object 파라미터

> api 요청 실패

```json
{
    "code": 400,
    "result": "fail",
    "contents": {
        "error": "invalidate parameter"
    }
}
```

| 파라미터 | 타입   | 필수 | 설명                     |
|----------|--------|------|--------------------------|
| error    | String | Y    | 에러 세부 메시지         |



