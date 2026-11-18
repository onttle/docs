---
title: '카테고리'
description: '선택지 목록을 카테고리로 관리하고 CATEGORY Column과 연결하는 방법, API로 카테고리를 조회하는 방법을 설명합니다.'
category: 'core-features'
slug: 'categories'
locale: 'ko'
apiVersion: 'v1'
order: 3
updatedAt: '2026-10-30'
---

# 카테고리

카테고리는 콘텐츠에서 고를 수 있는 **선택지 목록**을 Project 단위로 관리하는 기능입니다. 예를 들어 블로그 글의 분류, 상품의 색상, 문서의 대상 독자처럼 정해진 값 중에서 고르는 항목에 사용합니다.

선택지를 Column 옵션에 직접 적는 대신 카테고리로 관리하면, 여러 Table에서 같은 목록을 함께 쓰고 한 곳에서 수정할 수 있습니다.

---

## 카테고리 만들기

1. **Project → 카테고리**에서 **카테고리 만들기**를 클릭합니다.
2. 카테고리 이름과 설명을 입력합니다. 같은 Project 안에서 이름은 중복될 수 없습니다.
3. **값 추가**로 선택지를 추가합니다. 같은 카테고리 안에서 값은 중복될 수 없습니다.
4. **만들기**를 클릭합니다.

값은 입력한 순서대로 저장되며, 이 순서가 입력 폼과 API 응답에 그대로 사용됩니다.

### 값 추천

카테고리 이름을 입력한 뒤 **값 추천**을 클릭하면 AI가 어울리는 값을 제안합니다. 원하는 값을 선택해 **추가**합니다. 값 추천은 생성형 AI 토큰만큼 크레딧을 사용합니다.

---

## Table에 연결하기

1. Table에 Column을 추가할 때 Column 타입 목록의 **카테고리** 그룹에서 사용할 카테고리를 선택합니다.
2. 하나만 고르게 할지, 여러 개를 고르게 할지(multiple) 정합니다.

콘텐츠 입력 폼에서는 카테고리의 값이 선택 목록으로 표시됩니다. 콘텐츠 셀에서 **카테고리 값 추천**을 사용하면 AI가 콘텐츠 내용에 맞는 값을 골라 줍니다.

API 응답에서 CATEGORY Column 값은 다음 형태로 반환됩니다.

```json
{
  "category": { "id": "8b0c…", "name": "Tutorial" }
}
```

API로 콘텐츠를 만들거나 수정할 때는 카테고리 **값의 ID(UUID)** 를 보냅니다. 여러 값을 허용하는 Column은 ID 배열을 보냅니다.

---

## 카테고리 수정과 삭제

- 목록에서 카테고리를 선택해 이름, 설명, 값을 수정합니다.
- 카테고리를 삭제하면 **해당 카테고리와 관련된 모든 데이터가 삭제**됩니다. 이 카테고리를 사용하는 Column이 있다면 삭제하기 전에 확인하세요.

---

## API로 조회하기

`GET /categories`로 Project의 모든 카테고리와 값을 조회합니다. **API 엔드포인트 → 사전 정의 API**에서 카테고리 API를 활성화해야 합니다.

```bash
curl https://api.onttle.com/v1/{projectId}/categories \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
[
  {
    "id": "c1f2…",
    "name": "Docs Category",
    "description": null,
    "categories_values": [
      { "id": "a1…", "name": "getting-started" },
      { "id": "a2…", "name": "core-features" }
    ]
  }
]
```

카테고리는 이름순으로, 값은 카테고리에 저장한 순서대로 반환됩니다. 프론트엔드에서 필터 탭이나 메뉴를 만들 때 이 순서를 그대로 사용할 수 있습니다.

---

## 다음 단계

- [테이블 설계](/docs/core-features/table-design) — 카테고리 Column을 포함한 스키마 예시를 확인합니다.
- [Column 타입](/docs/developers/column-types) — CATEGORY Column의 옵션과 응답 형태를 확인합니다.
