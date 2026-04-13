# Design Reference: File Storage & Sync (Google Drive)

## Requirements
- Upload, download, sync files across devices
- File versioning and conflict resolution
- Real-time notifications of changes
- Support large files

## Architecture
```
Client -> Block Server (chunked upload) -> Cloud Storage (S3)
       -> Metadata Service -> Metadata DB (relational, ACID)
       -> Notification Service (long polling) -> Other devices
```

## Key Patterns

### Block-Level Sync (Delta Sync)
- Split files into 4MB blocks
- SHA-256 hash per block
- Upload only changed blocks (not entire file)
- Server reassembles from blocks

### Conflict Resolution
- First version processed wins
- Conflicting user gets both copies to merge manually
- File versions stored as read-only rows

### Notification: Long Polling (not WebSocket)
- File change notifications are infrequent and unidirectional
- Long polling is simpler and sufficient

### Strong Consistency
- Required for file operations (can't lose data)
- Relational DB for metadata (ACID guarantees)
- Invalidate cache on every DB write

## Storage Optimization
- Block-level dedup: identical blocks (same hash) stored once
- Limit versions: keep last 30, weight recent versions
- Cold storage: move old versions to S3 Glacier
- Compression before upload

## Schema
```sql
files: id, name, namespace_id, is_directory, latest_version, created_at, updated_at
file_versions: id, file_id, version_number, device_id, size, checksum, storage_url, created_at
blocks: id, file_version_id, block_order, checksum, storage_url, size
```
