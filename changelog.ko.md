---
title: '변경 이력'
description: 'Onttle API 및 서비스의 버전별 변경 사항을 기록합니다.'
category: 'changelog'
slug: 'changelog'
locale: 'ko'
apiVersion: null
order: 1
updatedAt: '2026-11-03'
---

# 변경 이력

최신 변경 사항이 상단에 표시됩니다. 변경 유형은 **추가**, **변경**, **수정**, **중단예정**으로 구분합니다.

---

## v1.1.0 <span>2026-11-03</span>

### 추가

- **커스텀 도메인**: Workspace의 CMS를 `cms.onttle.com` 같은 고객 도메인으로 접속할 수 있습니다. **Workspace → 도메인**에서 서브도메인을 추가하고 안내된 DNS 레코드를 등록하면 자동으로 검증됩니다. Pro 이상, Workspace당 20개까지. [문서](/docs/core-features/custom-domains)
- **커스텀 테마**: 색상(라이트·다크), 라운드, 폰트, 밀도와 로고·파비콘·탭 제목을 **Workspace → 테마**에서 설정합니다. `app.onttle.com`과 커스텀 도메인에 모두 적용됩니다. Pro 이상. [문서](/docs/core-features/custom-themes)

### 변경

- 요금제별 기능 표에 커스텀 도메인과 커스텀 테마를 추가했습니다. 커스텀 도메인은 Pro+가 아닌 **Pro부터** 제공됩니다.

---

## v1.0.0 <span>2026-10-30</span>

### 추가

- **Onttle API 정식 출시**: REST API v1이 정식 출시되었습니다.
- 지원 기능: Workspace, Project, Table, Column, Contents, Translation, Variable, Category, File, API Key, Metrics, Logs
- **API Key 유형**: 키를 **게시용**(`pk-proj_…`, 읽기 전용, 브라우저에서 사용 가능)과 **시크릿**(`sk-proj_…`, 읽기+쓰기, 서버 전용)으로 구분해 발급합니다. 게시용 키로 쓰기, 파일 업로드, Chat·Embeddings를 호출하면 `403`을 반환합니다.
- **키 해시 저장**: 전체 키는 생성 시 한 번만 표시되고 이후에는 해시만 저장됩니다. 대시보드에는 접두사와 마지막 4자리가 표시되고 키별 마지막 사용 시각이 기록됩니다.

---

## 변경 유형 안내

| 유형         | 의미                                               |
| ------------ | -------------------------------------------------- |
| **추가**     | 새 기능 또는 엔드포인트가 추가됨                   |
| **변경**     | 기존 동작 또는 스펙이 변경됨 (하위 호환성 주의)    |
| **수정**     | 버그 수정 또는 성능 개선                           |
| **중단예정** | 향후 삭제될 예정. 마이그레이션 가이드를 참고하세요 |
