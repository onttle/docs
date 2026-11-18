---
title: '개념 설명'
description: 'Workspace, Project, Table의 관계와 핵심 개념을 이해합니다.'
category: 'getting-started'
slug: 'concepts'
locale: 'ko'
apiVersion: null
order: 3
updatedAt: '2026-10-30'
---

# 개념 설명

Onttle의 데이터는 **Workspace → Project → Table → Contents** 계층 구조로 구성됩니다. 각 개념의 역할과 관계를 이해하면 효율적으로 스키마를 설계할 수 있습니다.

```text
Workspace   (팀 / 조직)
   │
   │ 1 : N
   ▼
Project     (하나의 서비스 / 도메인)
   │
   │ 1 : N
   ▼
Table       (콘텐츠 스키마 정의)
   │
   │ 1 : N
   ▼
Contents    (실제 데이터 행)
```

---

## Workspace

Workspace는 모든 리소스의 최상위 단위입니다. 하나의 팀 또는 조직에 대응합니다.

- **멤버 관리:** Workspace에 초대된 멤버는 소속된 Project에 접근할 수 있습니다.
- **결제 및 사용량:** 크레딧 잔액, 자동 충전 설정, 월간 사용량 한도는 Workspace 단위로 관리합니다.
- **복수 Project 포함:** 하나의 Workspace 아래 여러 Project를 생성할 수 있습니다.

**언제 새 Workspace를 만드나요?**

일반적으로 팀이나 회사 단위로 하나의 Workspace를 사용합니다. 결제를 분리해야 하는 독립적인 사업 단위라면 별도의 Workspace를 만드세요.

---

## Project

Project는 하나의 서비스 또는 도메인에 대응합니다. API Key와 Table 데이터는 Project 범위에서 분리됩니다.

- **API Key 범위:** API Key는 특정 Project에 소속되며, 해당 Project의 데이터만 접근할 수 있습니다.
- **복수 Table 포함:** 하나의 Project 아래 여러 Table을 생성할 수 있습니다.
- **멤버 역할:** Workspace 멤버에게 Project별로 역할(OWNER, ADMIN, EDITOR, GUEST)을 부여할 수 있습니다.

**Workspace와 Project의 차이**

| 항목           | Workspace      | Project                        |
| -------------- | -------------- | ------------------------------ |
| 결제 및 크레딧 | Workspace 단위 | —                              |
| API Key        | —              | Project 단위                   |
| 멤버 역할      | Workspace 수준 | Project 수준 (오버라이드 가능) |
| 데이터 격리    | —              | Project 단위로 격리            |

---

## Table

Table은 콘텐츠 스키마를 정의합니다. 관계형 데이터베이스의 테이블에 비유할 수 있습니다.

- **Column 정의:** 각 Table은 여러 Column을 가집니다. Column에는 타입, 필수 여부, 기본값 등을 설정합니다.
- **Contents 포함:** Table에 정의된 스키마에 맞는 실제 데이터 행이 Contents입니다.
- **자동 CMS:** Table을 생성하면 대시보드에 콘텐츠 입력, 수정, 발행 화면이 자동으로 생성됩니다.

---

## Column

Column은 Table의 데이터 필드를 정의합니다. 타입에 따라 대시보드 입력 UI와 API 응답 형식이 달라집니다.

| 타입        | 설명                       | 주요 옵션                                                  |
| ----------- | -------------------------- | ---------------------------------------------------------- |
| `TEXT`      | 단순 텍스트 또는 장문      | required, localization, unique, textarea, minText, maxText |
| `NUMBER`    | 정수 또는 소수             | autoIncrement, min, max, step                              |
| `DATE`      | 날짜와 시간                | required, multiple                                         |
| `BOOLEAN`   | true / false               | defaultValue                                               |
| `CATEGORY`  | 미리 정의된 선택지         | required, multiple                                         |
| `FILE`      | 이미지 또는 파일           | required, multiple                                         |
| `EDITOR`    | 리치 텍스트 편집기         | localization                                               |
| `JSON`      | 비정형 JSON 데이터         | defaultValue                                               |
| `REFERENCE` | 다른 Table의 Contents 참조 | required                                                   |

**localization 옵션**

TEXT와 EDITOR 컬럼에 `localization`을 설정하면 locale별로 다른 값을 저장하고 API에서 `?locale=ko`처럼 조회할 수 있습니다.

---

## Contents

Contents는 Table 스키마에 따라 작성한 실제 데이터입니다.

**발행 상태**

**상태(Status)**: 발행 상태는 `DRAFT`, `PUBLISHED` 두 가지만 가능합니다. CMS에서만 확인할 수 있으며, 실제 API를 통한 접근은 `PUBLISHED` 상태의 Contents만 가능합니다.

**단계(Stage)**: 발행 상태와 기존 발행 여부를 기준으로 콘텐츠가 '최신'인지 '업데이트 필요'인지를 판가름합니다.

| 단계          | 설명                                                                        |
| ------------- | --------------------------------------------------------------------------- |
| 발행 대기     | 아직 API로 접근이 불가능한 상태입니다.                                      |
| 최신 상태     | `PUBLISHED`이면서 가장 최신 상태입니다.                                     |
| 업데이트 필요 | 이전에 `PUBLISHED`되었지만 현재 다른 버전의 내용이 `DRAFT`로 작업 중입니다. |

**버전 관리**

Contents Form을 열어 '히스토리' 섹션에서 버전을 조회, 비교, rollback할 수 있습니다. 버전은 `v1`, `v2`처럼 `v#` 형태로 증가합니다.

**slug**

`slug`는 자동으로 생성되지 않으며, Table에서 slug 옵션을 체크해야 Contents 생성 시 자동으로 생성됩니다.

**SEO**

Table에서 SEO 지원을 선택하면 Contents에 SEO 입력용 제목, 설명 탭이 활성화됩니다.

**읽기 전용**

Table에서 읽기 전용을 체크하면 CMS에서는 데이터 편집이 불가능하고, API 호출을 통해서만 데이터 제어가 가능합니다. 주로 로그, 결제 내역 등에 사용됩니다.

---

## API Key

API Key는 외부 애플리케이션이 REST API를 사용하기 위한 인증 수단입니다. Project 범위에서 발급됩니다.

- **키 유형:** **게시용(Publishable)** 키(`pk-proj_…`)는 읽기(GET) 전용이며 브라우저나 공개 웹사이트에서 사용할 수 있습니다. **시크릿(Secret)** 키(`sk-proj_…`)는 데이터 생성·수정·삭제, 파일 업로드, Chat·Embeddings API 호출도 가능하므로 서버에서만 사용하세요. 유형은 발급 시 정해지며 바꿀 수 없습니다.
- **허용 호스트:** 특정 도메인 또는 IP 주소(CIDR 대역 포함)에서만 요청을 허용하도록 제한할 수 있습니다.
- **보안:** API Key는 생성 직후에만 전체 값이 표시됩니다. Onttle은 키의 해시만 저장하므로 목록에는 접두사와 마지막 4자리만 표시됩니다. 환경 변수로 관리하고 시크릿 키는 클라이언트 코드에 포함하지 마세요.

---

## 데이터 삭제 시 주의 사항

- **Column 삭제:** 해당 Column에 저장된 모든 Contents 데이터가 함께 삭제됩니다.
- **Table 삭제:** Table에 속한 모든 Column과 Contents가 함께 삭제됩니다.
- **Project 삭제:** Project에 속한 모든 Table, Contents, API Key가 함께 삭제됩니다.
- **Workspace 삭제:** 모든 Project와 연결된 데이터, 결제 정보가 삭제됩니다.

삭제는 되돌릴 수 없으므로 운영 중인 데이터 삭제 전 반드시 내보내기를 진행하세요.

---

## 다음 단계

- [테이블 설계](/docs/guides/table-design) — 실제 서비스에 맞는 Table 스키마를 설계합니다.
- [인증 방식](/docs/api-reference/authentication) — API Key의 권한과 보안 설정을 알아봅니다.
- [REST API 사용법](/docs/rest-api/request-examples) — API 호출 방법을 단계별로 익힙니다.
