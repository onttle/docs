---
title: '테이블 설계'
description: '콘텐츠 요구사항을 Column으로 변환하고 운영에 적합한 Table 스키마를 설계합니다.'
category: 'core-features'
slug: 'table-design'
locale: 'ko'
apiVersion: 'v1'
order: 1
updatedAt: '2026-11-12'
---

# 테이블 설계

이 가이드를 완료하면 실제 서비스 요구사항을 Onttle Table 스키마로 설계하고, 발행 상태 관리와 API 응답 형식을 이해할 수 있습니다.

**사전 조건:** Workspace와 Project가 생성되어 있어야 합니다.

---

## Column 타입 선택 기준

콘텐츠 필드의 성격에 따라 적합한 Column 타입을 선택합니다.

| 데이터 성격                         | 권장 타입                            |
| ----------------------------------- | ------------------------------------ |
| 제목, 요약, 짧은 텍스트             | `TEXT`                               |
| 본문, 긴 텍스트                     | `TEXT` (textarea 옵션) 또는 `EDITOR` |
| HTML이 포함된 리치 콘텐츠           | `EDITOR`                             |
| 숫자, 가격, 순서                    | `NUMBER`                             |
| 날짜, 게시일                        | `DATE`                               |
| 활성화 여부                         | `BOOLEAN`                            |
| 미리 정의된 선택지 (태그, 카테고리) | `CATEGORY`                           |
| 이미지, 첨부 파일                   | `FILE`                               |
| 다른 Table의 콘텐츠 참조            | `REFERENCE`                          |
| 비정형 JSON                         | `JSON`                               |

---

## Column 옵션

타입별로 설정할 수 있는 주요 옵션입니다.

### required

값이 반드시 입력되어야 하는 필드에 사용합니다. 값이 없으면 저장이 거부됩니다.

### unique

같은 Table 내에서 중복 값을 허용하지 않습니다. `slug`처럼 유일해야 하는 필드에 사용합니다.

### localization

`TEXT`, `EDITOR` 타입에서 사용합니다. 같은 필드에 locale별로 다른 값을 저장할 수 있습니다. API 요청 시 `?locale=ko`처럼 locale을 지정해 해당 언어의 값을 받습니다.

### autoIncrement

`NUMBER` 타입에서 콘텐츠가 생성될 때 자동으로 값이 1씩 증가합니다. 순서 필드에 활용합니다.

### multiple

하나의 필드에 복수의 값을 저장합니다. `FILE` 타입에서는 여러 파일을 첨부할 때, `CATEGORY` 타입에서는 복수 선택을 허용할 때 사용합니다.

### defaultValue

콘텐츠 생성 시 필드의 초기값을 지정합니다.

---

## 샘플 스키마: 블로그 게시물

```
Table: posts
├── title         TEXT        (required, localization)
├── slug          TEXT        (required, unique)
├── summary       TEXT        (textarea, localization)
├── body          EDITOR      (localization)
├── thumbnail     FILE
├── tags          CATEGORY    (multiple)
├── published_at  DATE
└── is_featured   BOOLEAN     (defaultValue: false)
```

**API 응답 예시**

```json
{
  "id": "cnt_01hxk2",
  "slug": "hello-onttle",
  "status": "PUBLISHED",
  "data": {
    "title": { "ko": "Onttle 소개", "en": "Hello Onttle" },
    "summary": { "ko": "첫 번째 글입니다", "en": "This is the first post" },
    "published_at": "2026-08-30T09:00:00.000Z",
    "is_featured": false
  }
}
```

---

## 샘플 스키마: 상품 정보

```
Table: products
├── name          TEXT        (required, localization)
├── sku           TEXT        (required, unique)
├── description   EDITOR      (localization)
├── price         NUMBER      (required, min: 0)
├── currency      CATEGORY    (required)
├── images        FILE        (multiple)
├── stock         NUMBER      (defaultValue: 0)
└── is_available  BOOLEAN     (defaultValue: true)
```

---

## 발행 상태와 API 연동

콘텐츠 상태별 외부 API 기본 응답 포함 여부입니다.

| 상태        | 기본 응답 포함 | 설명           |
| ----------- | -------------- | -------------- |
| `DRAFT`     | X              | 작성 중인 초안 |
| `REVIEW`    | X              | 검토 요청 중   |
| `APPROVED`  | X              | 검토 완료      |
| `PUBLISHED` | O              | 발행 완료      |
| `ARCHIVED`  | X              | 보관됨         |

`?status=DRAFT`처럼 명시적으로 상태를 지정하면 해당 상태의 콘텐츠도 조회할 수 있습니다. 단, 시크릿 API Key(`sk-proj_…`)가 필요합니다.

---

## API 엔드포인트와 권한 규칙

Table을 만들거나 수정하는 화면 아래쪽에서 이 Table의 API를 설정합니다.

- **API 엔드포인트:** 목록 조회, 단건 조회, 생성, 수정, 삭제, 검색 엔드포인트를 각각 켜고 끕니다. 꺼진 엔드포인트는 `403`을 반환합니다.
- **권한 규칙:** 게시용 키로 호출할 때 읽기·생성·수정·삭제를 누구에게 허용할지 정합니다. 기본값은 읽기 "공개", 쓰기 "비밀 키 전용"입니다. "로그인한 회원"이나 "작성자 본인"으로 바꾸면 [회원](/docs/core-features/members)이 직접 콘텐츠를 읽고 쓸 수 있습니다.

---

## Column 변경 시 주의 사항

운영 중인 Table의 스키마를 변경할 때는 다음 사항을 확인하세요.

| 작업             | 주의 사항                                                       |
| ---------------- | --------------------------------------------------------------- |
| Column 추가      | 기존 Contents의 해당 필드는 `null`로 처리됩니다                 |
| Column 타입 변경 | 기존 값이 새 타입과 호환되지 않으면 데이터가 손실될 수 있습니다 |
| Column 삭제      | 해당 Column에 저장된 모든 값이 영구적으로 삭제됩니다            |
| required 추가    | 기존 Contents 중 값이 없는 항목은 저장 시 오류가 발생합니다     |

운영 데이터가 있는 Table에서 Column을 삭제하기 전에 반드시 Contents를 내보내기 하세요.

---

## 다음 단계

- [API 레퍼런스 - Tables](/docs/api-reference/endpoints) — Tables 관련 엔드포인트를 확인합니다.
- [번역](/docs/guides/translations) — localization Column을 활용한 다국어 콘텐츠를 설정합니다.
- [파일 업로드](/docs/guides/file-upload) — FILE Column에 파일을 연결하는 방법을 알아봅니다.
