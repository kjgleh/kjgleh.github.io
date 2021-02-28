---
title: 'List of HTTP status codes'
layout: post
categories: etc
---

HTTP response status code는 5가지 그룹으로 분류할 수 있다.
- 1xx informational response
- 2xx success
- 3xx redirection
- 4xx client errors
- 5xx server errors

## 2xx success

#### 200 OK
> 요청이 성공적으로 처리되었다.

- GET, POST 요청 결과를 반환한다.
- PUT, DELETE 요청은 204 No Content가 적절하다. 

#### 201 Created
> 새 리소스가 성공적으로 생성되었다.

- 적절한 Method: POST
- 생성된 리소스를 반환한다.
- 생성된 리소스의 경로는 요청 URL이거나 header의 Content-Location에 명시한다.

#### 202 Accepted
> 요청은 성공적으로 받았으나 완료되지 않았다.

예를 들어 비동기, 메시지 처리, 배치 처리 등에서 사용할 수 있다.

#### 204 No Content
> 요청이 성공적으로 처리되었지만 별도의 응답 메시지를 제공하지 않는다.

예를 들어 PUT, DELETE 요청 시 또는 위키 사이트의 저장하고 계속 수정 시 사용할 수 있다.    

## 4xx client errors

#### 400 Bad Request
> 요청이 잘못된 경우

서버는 요청이 들어오면 서버가 정의한 유효성에 맞는지 확인 후 진행한다.

#### 401 Unauthorized
> 리소스에 대한 인증(authentication)되지 않은 요청

#### 403 Forbidden
> 리소스에 대한 권한(authorize)이 없는 요청

#### 404 Not Found
> 리소스를 찾을 수 없는 경우

예를 들어 페이지를 찾을 수 없거나 /users/123241343532과 같은 요청 시 리소스를 찾을 수 없는 경우 사용할 수 있다.

#### 405 Method Not Allowed
> 요청 메소드를 리소스가 지원하지 않는 경우

#### 409 Conflict
> 요청내용이 리소스의 현재 상태와 충돌하는 경우

PUT 메소드 요청인 경우 발생할 수 있는데 예를 들어 업로드하는 파일이 현재 버전보다 전 버전일 경우 해당 에러를 반환한다.

#### 429 Too Many Requests
> 단위 시간 내 요청이 제한 횟수를 초과한 경우

서버 정책에 따라 단위 시간 내 요청 횟수를 제한할 수 있다.

## 5XX Server errors
> 서버의 응답에서 5XX Server errors가 발생해서는 안된다.

## Reference
- <https://developer.mozilla.org/en-US/docs/Web/HTTP/Status>
- <https://sanghaklee.tistory.com/61>



