---
title: '파일 업로드'
description: '이미지와 문서를 업로드하고 OCR, 이미지 최적화, FILE Column 연결 방법을 알아봅니다.'
category: 'core-features'
slug: 'file-upload'
locale: 'ko'
apiVersion: 'v1'
order: 3
updatedAt: '2026-10-30'
---

# 파일 업로드

이 가이드를 완료하면 Onttle에 파일을 업로드하고, FILE Column에 연결하고, OCR과 이미지 최적화를 활용할 수 있습니다.

**사전 조건:** Project가 생성되어 있어야 합니다. 파일을 업로드하려면 시크릿 API Key(`sk-proj_…`) 또는 대시보드 접근이 필요합니다. 게시용 키로는 업로드할 수 없습니다.

---

## 대시보드에서 파일 업로드

1. Project의 **Files** 메뉴를 클릭합니다.
2. **파일 업로드** 버튼을 클릭하거나 파일을 드래그 앤 드롭합니다.
3. 이미지 파일의 경우 **이미지 최적화** 옵션을 선택할 수 있습니다.
4. PDF 또는 이미지 파일의 경우 **OCR** 옵션을 선택하면 텍스트를 자동으로 추출합니다.
5. 업로드가 완료되면 파일 URL을 확인할 수 있습니다.

---

## API로 파일 업로드

파일을 REST API로 업로드하려면 `multipart/form-data`를 사용합니다.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -F "file=@/path/to/image.jpg" \
  -F "isOcr=false"
```

**TypeScript 예제**

```typescript
async function uploadFile(file: File): Promise<string> {
  const formData = new FormData()
  formData.append('file', file)
  formData.append('isOcr', 'false')

  const res = await fetch(
    `https://api.onttle.com/v1/${process.env.ONTTLE_PROJECT_ID}/files`,
    {
      method: 'POST',
      headers: {
        Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      },
      body: formData,
    },
  )

  if (!res.ok) {
    throw new Error(`Upload failed: ${res.status}`)
  }

  const data = await res.json()
  return data.id // 업로드된 파일 ID (uuid)
}
```

**응답 예시**

```json
{
  "id": "3f2a1c8e-9b6d-4a11-8f3a-2d7c5e0b1a4f",
  "name": "image.jpg",
  "description": null,
  "meta": {
    "name": "image.jpg",
    "extension": "jpg",
    "lastModified": 1735500000000,
    "size": 204800,
    "type": "image/jpeg",
    "width": 1920,
    "height": 1080,
    "exif": null
  },
  "s3_path": "https://file.onttle.com/uploads/original/2026/11/12/image.jpg",
  "presigned_url": "https://file.onttle.com/uploads/original/2026/11/12/image.jpg?expires=1794470400&signature=…",
  "thumbnails": [],
  "text_vision": null,
  "description_generated": null,
  "keywords": []
}
```

> 파일 `id`는 uuid 형식의 문자열입니다.

---

## FILE Column에 파일 연결

업로드한 파일을 Contents의 FILE Column에 연결합니다.

1. 대시보드에서 Contents를 편집할 때 FILE 타입 컬럼의 **파일 선택** 버튼을 클릭합니다.
2. 이미 업로드된 파일 목록에서 선택하거나 새 파일을 업로드합니다.

API로 Contents를 생성하거나 수정할 때는 파일 ID를 사용합니다.

```typescript
// 파일 업로드 후 받은 file ID를 Contents 생성 시 사용
const res = await fetch(
  `https://api.onttle.com/v1/{projectId}/tables/posts/contents`,
  {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${process.env.ONTTLE_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      data: {
        title: '새 게시물',
        thumbnail: '3f2a1c8e-9b6d-4a11-8f3a-2d7c5e0b1a4f', // 업로드된 파일 ID (uuid)
      },
    }),
  },
)
```

---

## 이미지 최적화

이미지 썸네일은 **파일 → 이미지 썸네일 설정 (Image thumbnail settings)** 버튼에서 직접 설정할 수 있습니다. 표시 이름과 픽셀 크기로 썸네일 프리셋을 구성하면, 이후 업로드되는 이미지 파일에 대해 지정한 크기의 썸네일이 자동으로 생성됩니다.

- 프리셋 변경 사항은 새로 업로드되는 파일부터 적용되며, 기존에 생성된 썸네일에는 영향을 주지 않습니다.
- 원본 이미지보다 큰 크기의 썸네일은 생성되지 않습니다.
- 생성된 썸네일은 파일 응답의 `thumbnails` 필드에서 확인할 수 있습니다.

---

## OCR (텍스트 추출)

이미지(JPG, PNG 등) 또는 PDF 파일 업로드 시 OCR 옵션을 활성화하면 Vision 모델이 텍스트와 메타데이터를 자동으로 추출합니다.

- 추출된 텍스트는 파일 응답의 `text_vision` 필드에 저장됩니다.
- 자동 생성된 설명과 키워드는 각각 `description_generated`, `keywords` 필드에 저장됩니다.
- 이 정보들은 이후 Vector 임베딩 생성에도 활용됩니다.
- OCR 실행 횟수는 `FILE.OCR.CALLS` 메트릭에 기록됩니다.

```bash
# OCR 활성화하여 업로드
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -F "file=@/path/to/document.pdf" \
  -F "isOcr=true"
```

---

## EXIF 태그

이미지 파일 업로드 시 EXIF 메타데이터(촬영 기기, 촬영 일시, GPS 좌표 등)가 있다면 자동으로 추출되어 파일 응답의 `meta.exif` 필드에 저장됩니다.

- 대시보드의 파일 편집 화면에서 추출된 EXIF 태그 목록과 개수를 확인할 수 있습니다.
- EXIF 정보가 없는 이미지의 경우 `meta.exif` 값은 `null`입니다.

---

## 파일 교체 및 삭제

**파일 교체**

기존 파일을 새 파일로 교체하려면 `PUT /files/{fileId}`에 새 파일을 포함해 요청합니다. 새 파일로 교체될 때 `FILE.UPLOAD.CALLS`와 `FILE.UPLOAD.BYTES`가 다시 기록됩니다.

**파일 삭제**

```bash
curl -X DELETE https://api.onttle.com/v1/{projectId}/files/{fileId} \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

파일 삭제 후에는 해당 파일을 참조하는 Contents에서 파일 링크가 해제됩니다.

---

## 오류 처리

| 오류                    | 원인                     | 해결 방법                                    |
| ----------------------- | ------------------------ | -------------------------------------------- |
| `400 Bad Request`       | 지원하지 않는 파일 형식  | 허용된 MIME 타입 확인                        |
| `413 Payload Too Large` | 파일 크기 초과           | 파일 크기를 줄이거나 이미지 최적화 후 재시도 |
| `401 Unauthorized`      | API Key 누락 또는 잘못됨 | Authorization 헤더 확인                      |

---

## 다음 단계

- [사용량 및 과금 - 메트릭 종류](/docs/usage-and-billing/metric-types) — 파일 관련 메트릭 기록 방식을 확인합니다.
- [Ask AI (Assistant)](/docs/ai/ask-ai) — 어시스턴트로 파일을 검색하고, 다운로드 링크와 사용처를 확인하고, 이미지·PDF의 텍스트를 추출합니다.
