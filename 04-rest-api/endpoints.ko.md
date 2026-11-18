---
title: 'Endpoints'
description: 'Onttle REST API의 전체 엔드포인트 목록입니다. 테이블 콘텐츠, AI, 사전 정의 API, 회원 API를 한눈에 확인합니다.'
category: 'rest-api'
slug: 'endpoints'
locale: 'ko'
apiVersion: 'v1'
order: 1
popular: true
updatedAt: '2026-11-12'
---

# 엔드포인트 목록

모든 엔드포인트는 Project 단위이며, 아래 기본 주소 뒤에 경로를 붙여 호출합니다.

```
https://api.onttle.com/v1/{projectId}
```

Project의 **API 엔드포인트** 화면에서 같은 목록을 Table별 예제와 함께 볼 수 있고, 엔드포인트마다 켜고 끌 수 있습니다. 꺼진 엔드포인트는 `403`을 반환합니다.

---

## 인증

모든 요청에 API Key가 필요합니다. 두 가지 방법 중 하나로 보냅니다.

```
X-Onttle-Key: {API_KEY}
Authorization: Bearer {API_KEY}
```

| 키           | 접두사      | 쓸 수 있는 요청                                               |
| ------------ | ----------- | ------------------------------------------------------------- |
| 게시용 키    | `pk-proj_…` | 조회(GET), 회원 API, 권한 규칙이 허용한 회원의 쓰기·업로드    |
| 시크릿 키    | `sk-proj_…` | 모든 요청. 서버에서만 사용합니다                              |

로그인한 회원으로 호출할 때는 API Key를 `X-Onttle-Key`로 보내고, `Authorization: Bearer`에는 회원의 access 토큰을 넣습니다. 자세한 내용은 [인증](/docs/rest-api/authentication)을 참고하세요.

아래 표의 **키** 열은 그 엔드포인트를 호출할 수 있는 키입니다.

- **게시용**: 게시용 키와 시크릿 키 모두
- **시크릿**: 시크릿 키만
- **규칙**: 시크릿 키는 항상, 게시용 키는 Table의 [권한 규칙](/docs/core-features/members)이 허용할 때 (기본값은 시크릿 키 전용). 권한 규칙은 Table을 만들거나 수정하는 화면의 **API 엔드포인트** 아래에서 설정합니다.

---

## Table API

Table의 콘텐츠를 조회하고 변경합니다. `{table}`에는 Table ID 또는 slug, `{id}`에는 콘텐츠 ID 또는 slug를 넣습니다.

| 메서드   | 경로                         | 설명                    | 키                |
| -------- | ---------------------------- | ----------------------- | ----------------- |
| `GET`    | `/tables/{table}`            | 콘텐츠 목록 조회        | 게시용 (읽기 규칙) |
| `GET`    | `/tables/{table}/{id}`       | 콘텐츠 한 건 조회       | 게시용 (읽기 규칙) |
| `POST`   | `/tables/{table}`            | 콘텐츠 생성             | 규칙              |
| `PUT`    | `/tables/{table}/{id}`       | 콘텐츠 수정             | 규칙              |
| `DELETE` | `/tables/{table}/{id}`       | 콘텐츠 삭제             | 규칙              |
| `POST`   | `/tables/{table}/embeddings` | 임베딩(의미 기반) 검색  | 시크릿            |
| `POST`   | `/tables/{table}/bm25`       | 키워드(BM25) 검색       | 시크릿            |

- 조회는 **발행된** 콘텐츠만 반환합니다.
- 읽기 규칙의 기본값은 "공개"입니다. "로그인한 회원"이나 "작성자 본인"으로 바꾸면 회원 토큰이 필요합니다.
- 생성·수정·삭제 규칙을 "로그인한 회원", "작성자 본인", "특정 역할"로 바꾸면 **회원이 게시용 키로 직접 콘텐츠를 쓸 수 있습니다.** 회원이 만든 콘텐츠는 그 회원이 작성자가 됩니다.

요청·응답 형식은 [요청 예시](/docs/rest-api/request-examples), 검색은 [Embeddings 검색](/docs/ai/embeddings)과 [BM25 검색](/docs/ai/bm25)을 참고하세요.

---

## AI API

Agent와 대화하는 채팅 세션입니다.

| 메서드 | 경로                       | 설명                       | 키     |
| ------ | -------------------------- | -------------------------- | ------ |
| `POST` | `/chats`                   | 새 채팅 세션 시작          | 시크릿 |
| `POST` | `/chats/{chatId}/messages` | 기존 세션에 메시지 전송    | 시크릿 |
| `GET`  | `/chats/{chatId}/messages` | 세션의 메시지 조회         | 게시용 |

자세한 내용은 [Chats](/docs/ai/chats)를 참고하세요.

---

## 사전 정의 API

Project 리소스를 조회하고 파일을 올립니다.

| 메서드 | 경로            | 설명             | 키                    |
| ------ | --------------- | ---------------- | --------------------- |
| `GET`  | `/translations` | 번역 전체 조회   | 게시용                |
| `GET`  | `/variables`    | 변수 전체 조회   | 게시용                |
| `GET`  | `/categories`   | 카테고리 전체 조회 | 게시용              |
| `POST` | `/files`        | 파일 업로드      | 시크릿 (업로드 권한)  |

- 파일 업로드는 기본적으로 시크릿 키 전용입니다. 회원 인증 설정의 **파일 업로드 권한**을 "로그인한 회원" 또는 "특정 역할"로 바꾸면 **회원이 게시용 키로 직접 파일을 올릴 수 있습니다.** 회원이 올린 파일은 그 회원이 탈퇴하면 함께 삭제됩니다.

---

## 회원 API

내 서비스의 최종 사용자를 위한 가입·로그인 API입니다. Project에서 [회원 기능](/docs/core-features/members)을 켜야 동작하며, API Key는 `X-Onttle-Key` 헤더로 보냅니다. 요청·응답은 [회원 API](/docs/rest-api/members-api)를 참고하세요.

### 인증

| 메서드 | 경로                           | 설명                      | 키          |
| ------ | ------------------------------ | ------------------------- | ----------- |
| `POST` | `/auth/signup`                 | 회원 가입, 인증 메일 발송 | 게시용      |
| `POST` | `/auth/email/verify`           | 이메일 인증 완료          | 게시용      |
| `POST` | `/auth/email/resend`           | 인증 메일 재발송          | 게시용      |
| `POST` | `/auth/login`                  | 로그인                    | 게시용      |
| `POST` | `/auth/token/refresh`          | 토큰 갱신                 | 게시용      |
| `POST` | `/auth/logout`                 | 로그아웃                  | 회원 토큰   |
| `POST` | `/auth/password/reset-request` | 비밀번호 재설정 메일 발송 | 게시용      |
| `POST` | `/auth/password/reset`         | 새 비밀번호 설정          | 게시용      |

### 내 정보

| 메서드   | 경로           | 설명                                    | 키        |
| -------- | -------------- | --------------------------------------- | --------- |
| `GET`    | `/me`          | 내 정보 조회                            | 회원 토큰 |
| `PATCH`  | `/me`          | 프로필·비밀번호 수정                    | 회원 토큰 |
| `DELETE` | `/me`          | 탈퇴 (90일간 같은 이메일로 재가입 불가) | 회원 토큰 |
| `GET`    | `/me/consents` | 동의 이력 조회                          | 회원 토큰 |
| `PUT`    | `/me/consents` | 동의·철회 기록                          | 회원 토큰 |

"회원 토큰"은 게시용 키와 함께 `Authorization: Bearer {accessToken}`을 보내야 한다는 뜻입니다.

회원 목록 조회, 역할 변경, 정지, 강제 탈퇴, 회원 직접 추가는 API가 아니라 대시보드의 **회원** 메뉴에서 합니다.

---

## 다음 단계

- [인증](/docs/rest-api/authentication) — API Key 발급과 헤더 사용법을 확인합니다.
- [요청 예시](/docs/rest-api/request-examples) — 요청 예제와 응답 파싱 방법을 알아봅니다.
- [회원 API](/docs/rest-api/members-api) — 회원 가입·로그인 엔드포인트의 요청과 응답을 확인합니다.
- [에러 코드](/docs/rest-api/error-codes) — HTTP 상태 코드별 오류 원인과 해결 방법을 확인합니다.
