---
title: 'TypeScript'
description: '테이블 스키마별로 TypeScript 타입 정의를 자동으로 생성하는 방법을 설명합니다.'
category: 'developers'
slug: 'typescript'
locale: 'ko'
apiVersion: 'v1'
order: 3
updatedAt: '2026-11-16'
---

# TypeScript 타입 정의

Onttle은 Table 스키마를 기반으로 TypeScript 타입 정의를 자동으로 생성합니다. Column을 추가하거나 변경하면 정의가 즉시 다시 생성되므로, 프론트엔드 코드에서 타입을 직접 작성하거나 관리하지 않고도 Contents를 타입 안전하게 다룰 수 있습니다.

---

## 위치

Project 좌측 메뉴에서 **TypeScript**를 열면 해당 Project의 모든 Table에 대한 타입 정의를 확인할 수 있습니다.

```
/workspaces/{workspaceId}/projects/{projectId}/typescript
```

**Contents** 툴바의 **TypeScript definitions** 버튼을 클릭하면 해당 Table의 정의로 바로 이동합니다.

> **[IMAGE AREA]** TypeScript 페이지 — Tables / Predefined 탭과 생성된 정의 블록

이 페이지는 두 개의 탭으로 구성됩니다.

| 탭             | 설명                                                                           |
| -------------- | ------------------------------------------------------------------------------ |
| **Tables**     | 각 Table의 Column을 기준으로 생성되는 `Request` / `Response` 인터페이스        |
| **Predefined** | `ProjectFile`, `Category`, `Variable` 등 모든 Table에서 공통으로 참조하는 타입 |

각 정의 블록에는 코드를 복사하는 **복사** 버튼이 있으며, Table 블록에는 해당 Table의 **콘텐츠로 이동**, **API 엔드포인트로 이동** 바로가기가 함께 제공됩니다.

---

## Tables 탭

각 Table마다 Contents를 생성하거나 수정할 때 사용하는 요청 본문용 `Request` 인터페이스와, API가 반환하는 데이터 형태를 나타내는 `Response` 인터페이스가 생성됩니다.

### 타입 이름

Table 이름은 `PascalCase`로 변환되어 타입 이름의 접두어로 사용됩니다. 이름이 숫자로 시작하면 앞에 `Table`을 붙입니다.

| Table 이름   | 생성되는 타입 이름 접두어 |
| ------------ | ------------------------- |
| `blog-post`  | `BlogPost`                |
| `2024_promo` | `Table2024Promo`          |

### Request 인터페이스

```typescript
export interface BlogPostRequest {
  slug?: string
  seo_title?: string
  seo_description?: string
  seo_image?: string
  is_publish?: boolean
  title: string
  body?: string
}
```

| 필드                                          | 포함 조건                                            |
| --------------------------------------------- | ---------------------------------------------------- |
| `slug`                                        | Table에 Slug 옵션이 활성화된 경우                    |
| `seo_title` / `seo_description` / `seo_image` | Table에 SEO 필드가 활성화된 경우                     |
| `is_publish`                                  | 항상 포함                                            |
| Column 필드                                   | 편집 가능한 모든 Column, Column의 `name`을 키로 사용 |

Column에 **필수(Required)** 옵션이 설정되지 않은 경우 필드는 선택(`?`) 값으로 생성됩니다. `SEPARATOR`, `LABEL`, `UNKNOWN` 타입의 Column은 레이아웃 전용이므로 생성되는 인터페이스에서 제외됩니다.

### Response 인터페이스

```typescript
export interface BlogPostResponse {
  id: string
  slug: string
  created_at: string
  updated_at: string
  published_at: string | null
  data: {
    title: string
    body: string
  }
}
```

| 필드                                             | 포함 조건                                                   |
| ------------------------------------------------ | ----------------------------------------------------------- |
| `id`, `created_at`, `updated_at`, `published_at` | 항상 포함                                                   |
| `slug`                                           | Table에 Slug 옵션이 활성화된 경우                           |
| `seo_title` / `seo_description` / `seo_image`    | Table에 SEO 필드가 활성화된 경우 (`seo_image: ProjectFile`) |
| `data`                                           | `SEPARATOR`, `LABEL`, `UNKNOWN`을 제외한 모든 Column        |

Column의 `name`이 유효한 TypeScript 식별자가 아닌 경우(공백을 포함하거나 숫자로 시작하는 등) 해당 키는 문자열 리터럴로 감싸 표기합니다.

---

## Column 타입 매핑

각 Column 타입은 다음과 같이 TypeScript 타입으로 매핑됩니다. `Request` 열은 입력에 사용하는 타입을, `Response` 열은 API가 반환하는 타입을 나타냅니다.

| Column 타입                                          | Request 타입                                                                   | Response 타입             |
| ---------------------------------------------------- | ------------------------------------------------------------------------------ | ------------------------- |
| `NUMBER`                                             | `number`                                                                       | `number`                  |
| `BOOLEAN`                                            | `boolean`                                                                      | `boolean`                 |
| `DATE`, `TIMESTAMP`, `TEXT`, `EMAIL`, `URL`, `COLOR` | `string`                                                                       | `string`                  |
| `JSON`                                               | `unknown` (임의의 JSON 값 또는 JSON 텍스트) | `unknown` |
| `MAP`                                                | `Record<string, unknown>`                                                      | `Record<string, unknown>` |
| `EDITOR`                                             | `string \| { type: 'doc'; content?: unknown[] }` (Markdown 문자열 또는 TipTap 문서) | `{ json: Record<string, unknown>; html: string; markdown: string; plain: string }` |
| `CATEGORY`                                           | `string`                                                                       | `Category`                |
| `FILE`                                               | `string`                                                                       | `ProjectFile`             |
| `REFERENCE`                                          | `string`                                                                       | `Record<string, unknown>` |
| 그 외 / 인식되지 않는 타입                           | `unknown`                                                                      | `unknown`                 |

### 배열 및 다국어 옵션

두 가지 Column 옵션은 생성되는 타입을 추가로 변형하며, 함께 사용할 수 있습니다.

| 옵션           | 효과                                                     |
| -------------- | -------------------------------------------------------- |
| `multiple`     | 타입을 `Array<T>`로 감쌉니다.                            |
| `localization` | 타입을 `Partial<Record<ProjectLanguage, T>>`로 감쌉니다. |

```typescript
// multiple + localization이 활성화된 TEXT Column
tags?: Partial<Record<ProjectLanguage, Array<string>>>
```

---

## Predefined 탭

**Predefined** 탭에는 모든 Table의 `Response` 인터페이스에서 공통으로 참조하는 타입이 포함됩니다.

```typescript
export type ProjectLanguage = 'ko' | 'en'

export interface Translation {
  key: string
  value: Partial<Record<ProjectLanguage, string>>
  description: string | null
}

export interface Variable {
  key: string
  value: unknown
  description: string | null
}

export interface FileMeta {
  name: string
  extension: string
  lastModified: number
  size: number
  type: string
  width: number | null
  height: number | null
  exif: Record<string, string> | null
}

export interface FileThumbnail {
  path: string
  presigned_url: string
  size: string
  width: number
  height: number
}

export interface ProjectFile {
  id: string
  name: string
  description: string | null
  meta: FileMeta
  s3_path: string
  presigned_url: string
  thumbnails: FileThumbnail[]
  text_vision: string | null
  description_generated: string | null
  keywords: string[]
}

export interface Category {
  id: string
  name: string
}
```

`ProjectLanguage`는 현재 Project에 설정된 언어를 기준으로 유니언 타입으로 생성되므로 항상 Project의 실제 언어 설정을 반영합니다.

---

## 참조 타입에 마우스 올리기

**Tables** 탭에서 `ProjectFile`, `Category`, `ProjectLanguage`처럼 다른 타입을 참조하는 이름에는 밑줄이 표시됩니다. 해당 이름에 마우스를 올리거나 포커스를 두면 **Predefined** 탭으로 이동하지 않고도 툴팁으로 타입 정의를 확인할 수 있습니다.

---

## 생성된 타입 사용하기

정의를 프로젝트에 복사한 뒤, REST API `fetch` 응답의 타입으로 사용합니다.

```typescript
const res = await fetch(
  'https://api.onttle.com/v1/{projectId}/tables/blog-post/contents',
  { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
)

const { list }: { list: BlogPostResponse[] } = await res.json()
console.log(list[0].data.title)
```

---

## 다음 단계

- [테이블 설계](/docs/core-features/table-design) — Column 설정이 생성되는 타입 정의에 어떻게 반영되는지 확인합니다.
- [응답 형식](/docs/rest-api/response-format) — 목록 및 단일 항목 API 응답의 전체 구조를 확인합니다.
- [OpenAPI 명세](/docs/developers/openapi) — Contents 뿐 아니라 전체 API 타입을 생성하는 방법을 알아봅니다.
