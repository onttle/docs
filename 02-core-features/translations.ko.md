---
title: '번역'
description: '번역 리소스를 관리하고 애플리케이션 i18n 라이브러리와 연결합니다.'
category: 'core-features'
slug: 'translations'
locale: 'ko'
apiVersion: 'v1'
order: 2
updatedAt: '2026-10-30'
---

# 번역

이 가이드를 완료하면 Onttle에서 번역 키-값을 관리하고 프론트엔드 애플리케이션에서 locale별 번역을 API로 가져올 수 있습니다.

**사전 조건:** Project가 생성되어 있어야 합니다. API Key가 필요합니다. 번역은 조회만 하므로 게시용 키(`pk-proj_…`)로 충분합니다.

---

## 번역 리소스의 구조

Onttle의 번역은 **키-값** 구조로 관리됩니다. 하나의 키에 지원하는 locale별 값을 각각 저장합니다.

```
Key: "common.save"
  ko: "저장"
  en: "Save"

Key: "common.cancel"
  ko: "취소"
  en: "Cancel"

Key: "common.delete"
  ko: "삭제"
  en: "Delete"

Key: "common.confirm"
  ko: "확인"
  en: "Confirm"

Key: "common.close"
  ko: "닫기"
  en: "Close"

Key: "nav.home"
  ko: "홈"
  en: "Home"

Key: "nav.settings"
  ko: "설정"
  en: "Settings"

Key: "nav.profile"
  ko: "프로필"
  en: "Profile"

Key: "button.submit"
  ko: "제출"
  en: "Submit"

Key: "button.next"
  ko: "다음"
  en: "Next"

Key: "button.previous"
  ko: "이전"
  en: "Previous"

Key: "error.required"
  ko: "필수 입력 항목입니다"
  en: "This field is required"

Key: "error.invalid"
  ko: "유효하지 않은 값입니다"
  en: "Invalid value"

Key: "message.success"
  ko: "성공적으로 처리되었습니다"
  en: "Successfully processed"

Key: "message.welcome"
  ko: "환영합니다"
  en: "Welcome"
```

---

## 1단계: 지원 언어 설정

1. Project 설정으로 이동합니다.
2. **Languages** 메뉴에서 지원할 언어를 추가합니다.
3. 기본 언어를 선택합니다. 기본 언어는 locale을 지정하지 않은 API 요청에서 사용됩니다.

---

## 2단계: 번역 키 추가

1. Project의 **Translations** 메뉴를 클릭합니다.
2. **새 번역** 버튼을 클릭합니다.
3. 키 이름을 입력합니다. 점(`.`) 구분자로 계층 구조를 표현할 수 있습니다.
   - 예: `nav.home`, `button.submit`, `error.required`
4. 각 locale에 해당하는 값을 입력합니다.
5. **저장**을 클릭합니다.

---

## 3단계: API로 번역 조회

번역 전체 목록을 조회하거나 특정 locale의 번역 데이터를 가져올 수 있습니다.

**전체 번역 목록 조회**

```bash
curl https://api.onttle.com/v1/{projectId}/translations \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

**응답 예시**

```json
[
  {
    "key": "common.save",
    "values": {
      "ko": "저장",
      "en": "Save"
    }
  },
  {
    "key": "common.cancel",
    "values": {
      "ko": "취소",
      "en": "Cancel"
    }
  }
]
```

---

## next-intl 연동 예제

`next-intl`을 사용하는 Next.js 프로젝트에서 Onttle 번역 API를 연동합니다.

```typescript
// i18n/request.ts
import { getRequestConfig } from 'next-intl/server'

export default getRequestConfig(async ({ locale }) => {
  // Onttle API에서 번역 데이터를 가져옵니다
  const res = await fetch(
    `https://api.onttle.com/v1/${process.env.ONTTLE_PROJECT_ID}/translations`,
    {
      headers: {
        Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      },
      next: { revalidate: 3600 }, // 1시간 캐시
    },
  )

  const { list } = await res.json()

  // 키-값 flat 객체로 변환합니다
  const messages = list.reduce(
    (
      acc: Record<string, string>,
      item: { key: string; values: Record<string, string> },
    ) => {
      acc[item.key] = item.values[locale] ?? item.values['ko'] ?? item.key
      return acc
    },
    {},
  )

  return { messages }
})
```

---

## 누락된 번역값 처리

API 응답에서 특정 locale 값이 없는 경우 fallback 전략을 사용합니다.

```typescript
function getTranslation(
  values: Record<string, string>,
  locale: string,
  defaultLocale = 'ko',
): string {
  return values[locale] ?? values[defaultLocale] ?? ''
}
```

번역값이 없는 키는 키 이름 자체를 표시하거나 기본 언어 값으로 대체하는 방법 중 서비스 요건에 맞는 방식을 사용하세요.

---

## 사용량 반영

번역 데이터는 `DATA.STORE.BYTES` 메트릭에 포함됩니다.

- 번역 키를 새로 생성하면 저장된 데이터 크기가 메트릭에 기록됩니다.
- 번역값을 수정하면 변경 전후 데이터 크기의 차이가 기록됩니다.

---

## 다음 단계

- [테이블 설계](/docs/guides/table-design) — TEXT, EDITOR Column의 localization 옵션으로 콘텐츠별 다국어를 설정합니다.
- [REST API 사용법](/docs/rest-api/query-parameters) — locale 쿼리 파라미터 사용법을 확인합니다.
- [사용량 및 과금](/docs/usage-and-billing/metric-types) — DATA.STORE.BYTES 메트릭 기록 방식을 알아봅니다.
