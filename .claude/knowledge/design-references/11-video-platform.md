# Design Reference: Video Platform (YouTube)

## Requirements
- Upload and stream video
- Adaptive bitrate streaming
- Global low-latency playback via CDN
- Transcoding to multiple formats/resolutions

## Architecture
```
Upload:
  Client -> Upload Service (pre-signed URL) -> Blob Storage (original)
         -> Transcoding Pipeline (DAG) -> Transcoded Storage -> CDN

Streaming:
  Client -> CDN (edge) -> Origin (transcoded storage)
```

## Video Upload Flow
1. Client requests pre-signed upload URL
2. Client uploads directly to blob storage (S3)
3. Upload completion triggers transcoding pipeline
4. In parallel: metadata (title, description) saved to Metadata DB

## Transcoding Pipeline (DAG Model)
```
Original Video -> Preprocessor (split by GOP)
                      |
              DAG Scheduler (generates task graph)
                      |
              +-------+-------+-------+
              |       |       |       |
           Video   Video   Audio   Thumbnail
           1080p   720p   Extract  Generator
              |       |       |       |
              +-------+-------+-------+
                      |
              Merge & Package (HLS/DASH segments)
                      |
              Transcoded Storage -> CDN
```

## Streaming Protocols
| Protocol | By | Features |
|----------|-----|---------|
| HLS | Apple | Most widely supported, HTTP-based |
| DASH | MPEG | Open standard, adaptive |

Both work by splitting video into small segments (2-10s) with a manifest file listing available qualities.

## Key Optimizations
- **Parallel upload**: Split by GOP (Group of Pictures), upload chunks independently
- **Upload centers**: Deploy close to users (multiple regions)
- **Popular content on CDN**: Only ~20% of videos get 80% of views - cache aggressively
- **Long-tail on demand**: Less popular content transcoded on-demand or lower priority
- **Cost optimization**: Don't transcode to all resolutions immediately - start with common ones (720p, 1080p)

## Safety & DRM
- Pre-signed upload URLs (prevent unauthorized uploads)
- DRM: FairPlay (Apple), Widevine (Google), PlayReady (Microsoft)
- AES encryption for content protection
- Visual watermarking for leak detection

## Data Model
```sql
videos: id, user_id, title, description, status, duration, created_at
video_formats: id, video_id, resolution, codec, bitrate, url, size_bytes
thumbnails: id, video_id, url, is_default
```

## Scaling
- Transcoding: autoscale workers based on queue depth
- Storage: S3 with lifecycle policies (hot -> warm -> cold)
- CDN: multi-CDN strategy for resilience and cost
- Metadata DB: read replicas, cache video info in Redis
