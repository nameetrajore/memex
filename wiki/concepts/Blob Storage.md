---
title: "Blob Storage"
type: concept
tags: [system-design, storage, s3, infrastructure]
created: 2026-05-13
updated: 2026-05-13
sources: [system-design-key-technologies]
---

## Summary

Dedicated storage for large unstructured data (images, videos, files). Use S3/GCS instead of your database — it's cheaper, infinitely scalable, and purpose-built. Store metadata (URLs) in your core DB; blobs in blob storage.

## Key Points

- **Presigned URLs**: client uploads/downloads directly to/from storage, bypassing app servers. Server generates temporary scoped credentials.
- **Upload flow**: client requests presigned URL → server returns URL + records in DB → client uploads directly → storage triggers notification → server updates status.
- **Download flow**: client requests file → server returns presigned URL → client downloads via CDN.
- **Chunking**: multipart upload for large files. Enables resumable uploads and parallel transfers.
- **Durability**: replication + erasure coding. Data is extremely safe.
- **Cost**: S3 ~$0.023/GB/month vs DynamoDB ~$1.25/GB/month.
- Don't use S3 as your primary database.

## Common Use Cases
- YouTube → videos in S3, metadata in DB
- Instagram → images in S3, metadata in DB
- Dropbox → files in S3, metadata in DB

## Related

- [[System Design Key Technologies]]
- [[Caching]] (CDN layer)
- [[System Design Interview Patterns]] (Large Blobs pattern)
