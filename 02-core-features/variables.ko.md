---
title: '변수 (Variables)'
description: 'Project 전체에서 공유하는 글로벌 변수를 관리하고 API로 조회하는 방법을 설명합니다.'
category: 'core-features'
slug: 'variables'
locale: 'ko'
apiVersion: 'v1'
order: 4
updatedAt: '2026-08-30'
---

# 변수 (Variables)

Variable은 Project 전체에서 공유하는 글로벌 키-값 저장소입니다. API Base URL, 공지사항 문자열, 운영 중 자주 변경되는 설정값을 코드 배포 없이 관리할 때 사용합니다.

---

## Variable의 용도

| 사용 사례           | 예시                                                   |
| ------------------- | ------------------------------------------------------ |
| 사이트 전역 설정    | 사이트 이름, 기본 언어, 연락처 이메일                  |
| 배너 / 공지 문자열  | 이벤트 배너 메시지, 유지보수 안내                      |
| 기능 플래그         | `maintenance_mode: true`, `new_feature_enabled: false` |
| 외부 서비스 공개 키 | 지도 API 공개 키, 분석 스크립트 ID                     |

Variable은 민감한 시크릿 보관에 적합하지 않습니다. 비밀 키는 서버 환경 변수로 관리합니다.

---

## 대시보드에서 Variable 관리

1. Project 좌측 메뉴에서 **Variables**를 클릭합니다.
2. **새 Variable** 버튼을 클릭합니다.
3. **Key**와 **Value**를 입력하고 **저장**합니다.

Variable의 Key는 `snake_case` 또는 `SCREAMING_SNAKE_CASE` 형식을 권장합니다.

---

## 콘텐츠와 번역에서 Variable 사용하기

TEXT 타입 Variable은 Contents나 Translation의 텍스트 입력란에서 `{{`를 입력하면 자동완성으로 등록된 Variable 목록이 표시됩니다. 목록에서 원하는 Variable을 선택하면 `{{var_key}}` 형식의 토큰이 삽입되고, 실제 화면에는 해당 Variable의 값으로 치환되어 노출됩니다.

```
{{var_site_name}}에 오신 것을 환영합니다. → My Blog에 오신 것을 환영합니다.
```

- Rich Text 에디터에서도 툴바의 변수 삽입 버튼을 눌러 같은 방식으로 Variable을 삽입할 수 있습니다.
- 연결된 Variable의 값이 바뀌면 별도 수정 없이도 치환된 내용이 자동으로 최신 값을 반영합니다.
- FILE 타입 Variable은 텍스트 치환 대상이 아니므로 자동완성 목록에 표시되지 않습니다.

---

## API로 Variable 조회

### 전체 Variable 목록 조회

```bash
curl https://api.onttle.com/v1/{projectId}/variables \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
{
  "list": [
    { "key": "site_name", "value": "My Blog" },
    { "key": "maintenance_mode", "value": "false" }
  ],
  "pagination": { "page": 1, "limit": 20, "total": 2 }
}
```

### 특정 Variable 조회

```bash
curl https://api.onttle.com/v1/{projectId}/variables/{key} \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

```json
{
  "key": "site_name",
  "value": "My Blog"
}
```

---

## Variable과 다국어

Variable 값은 단일 문자열입니다. 언어별로 다른 값이 필요하면 Key에 locale을 접미사로 추가하는 방식을 권장합니다.

```
site_description_ko → "Onttle 기반 블로그입니다."
site_description_en → "A blog powered by Onttle."
```

또는 Translation 기능을 사용해 locale별 텍스트를 관리합니다.

---

## TypeScript에서 사용하기

```typescript
// 모든 Variable을 객체 형태로 로드
async function loadVariables(
  projectId: string,
): Promise<Record<string, string>> {
  const res = await fetch(
    `https://api.onttle.com/v1/${projectId}/variables?limit=1000`,
    { headers: { Authorization: `Bearer ${process.env.ONTTLE_API_KEY}` } },
  )
  const { list } = await res.json()
  return Object.fromEntries(
    list.map((v: { key: string; value: string }) => [v.key, v.value]),
  )
}

// 사용 예
const vars = await loadVariables('proj_01hxk2')
console.log(vars.site_name) // "My Blog"
```

---

## 다음 단계

- [번역](/docs/core-features/translations) — 언어별 텍스트를 관리하려면 Translation 기능을 사용합니다.
- [REST API 요청 예시](/docs/rest-api/request-examples) — Variable API 요청의 공통 규칙을 확인합니다.
