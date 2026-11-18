---
title: 'File Upload'
description: 'Upload images and documents, use OCR and image optimization, and link files to FILE Columns.'
category: 'core-features'
slug: 'file-upload'
locale: 'en'
apiVersion: 'v1'
order: 3
updatedAt: '2026-10-30'
---

# File Upload

By the end of this guide, you will be able to upload files to Onttle, link them to FILE Columns in Contents, and take advantage of OCR and image optimization.

**Prerequisites:** A Project must be created. Uploading files requires a secret API Key (`sk-proj_…`) or dashboard access; publishable keys cannot upload.

---

## Uploading Files from the Dashboard

1. Click **Files** in the Project menu.
2. Click **Upload File** or drag and drop files.
3. For image files, you can select the **Image Optimization** option.
4. For PDF or image files, selecting the **OCR** option automatically extracts text.
5. After upload, you can view the file URL.

> **[IMAGE AREA]** File upload screen (drag-and-drop area and options UI)

---

## Uploading Files via API

Use `multipart/form-data` to upload files via the REST API.

```bash
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -F "file=@/path/to/image.jpg" \
  -F "isOcr=false"
```

**TypeScript Example**

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
  return data.id // ID of the uploaded file (uuid)
}
```

**Response Example**

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

> The file `id` is a uuid-formatted string.

---

## Linking Files to a FILE Column

Link an uploaded file to a FILE Column in a Contents item.

1. When editing Contents in the dashboard, click the **Select File** button on a FILE-type Column.
2. Choose from the already-uploaded file list or upload a new file.

When creating or updating Contents via the API, use the file ID.

```typescript
// Use the file ID received after upload when creating Contents
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
        title: 'New Post',
        thumbnail: '3f2a1c8e-9b6d-4a11-8f3a-2d7c5e0b1a4f', // Uploaded file ID (uuid)
      },
    }),
  },
)
```

---

## Image Optimization

You can configure thumbnails directly from the **Files → Image thumbnail settings** button. Define presets with a display name and pixel size, and any image file uploaded afterward will automatically generate thumbnails at the specified sizes.

- Preset changes apply only to files uploaded after the change; existing thumbnails are not affected.
- Thumbnails larger than the original image are never generated.
- The generated thumbnails can be viewed in the `thumbnails` field of the file response.

---

## OCR (Text Extraction)

Enabling the OCR option when uploading image (JPG, PNG, etc.) or PDF files causes a Vision model to automatically extract text and metadata.

- The extracted text is stored in the `text_vision` field of the file response.
- The automatically generated description and keywords are stored in the `description_generated` and `keywords` fields, respectively.
- This information is also used later for Vector embedding generation.
- OCR usage is recorded under the `FILE.OCR.CALLS` metric.

```bash
# Upload with OCR enabled
curl -X POST https://api.onttle.com/v1/{projectId}/files \
  -H "Authorization: Bearer $ONTTLE_API_KEY" \
  -F "file=@/path/to/document.pdf" \
  -F "isOcr=true"
```

---

## EXIF Tags

When an image file is uploaded, its EXIF metadata (capture device, capture date, GPS coordinates, etc.), if present, is automatically extracted and stored in the `meta.exif` field of the file response.

- You can view the extracted EXIF tags and their count in the file edit screen of the dashboard.
- For images without EXIF information, `meta.exif` is `null`.

---

## Replacing and Deleting Files

**Replacing a File**

To replace an existing file with a new one, send a `PUT /files/{fileId}` request including the new file. `FILE.UPLOAD.CALLS` and `FILE.UPLOAD.BYTES` are recorded again for the replacement.

**Deleting a File**

```bash
curl -X DELETE https://api.onttle.com/v1/{projectId}/files/{fileId} \
  -H "Authorization: Bearer $ONTTLE_API_KEY"
```

After deletion, the file link is removed from any Contents that referenced it.

---

## Error Handling

| Error                   | Cause                      | Resolution                                                 |
| ----------------------- | -------------------------- | ---------------------------------------------------------- |
| `400 Bad Request`       | Unsupported file format    | Check the allowed MIME types                               |
| `413 Payload Too Large` | File size exceeded         | Reduce file size or use image optimization before retrying |
| `401 Unauthorized`      | Missing or invalid API Key | Verify the Authorization header                            |

---

## Next Steps

- [Usage & Billing - Metric Types](/docs/usage-and-billing/metric-types) — Understand how file-related metrics are recorded.
- [Ask AI (Assistant)](/docs/ai/ask-ai) — Search files, get download links and usage, and extract text from images and PDFs through the assistant.
