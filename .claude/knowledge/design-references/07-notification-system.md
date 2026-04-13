# Design Reference: Notification System

## Requirements
- Multi-channel: Push (APNs/FCM), SMS (Twilio), Email (SendGrid), In-App (WebSocket)
- Reliable delivery with retry
- User preferences (opt-in/opt-out per channel)
- Rate limiting (prevent notification fatigue)
- Template system for message content

## Architecture
```
Event Source -> Notification Service -> Priority Queue (per channel)
                    |                        |
              Template Engine         Channel Workers
              User Preferences        ├── Push Worker -> APNs/FCM
                                      ├── SMS Worker -> Twilio
                                      ├── Email Worker -> SendGrid
                                      └── In-App Worker -> WebSocket
```

## Key Design Points
- **Decouple channels**: Separate queue per channel. SMS outage doesn't affect email.
- **Templates**: Mustache/Handlebars templates with variable substitution
- **Dedup**: Idempotency key per notification (prevent duplicate sends)
- **Rate limit per user**: Max 3 push/hour, 1 SMS/day, etc.
- **Retry**: Exponential backoff. Max 3 retries. Dead letter queue for failures.
- **Priority**: Urgent (bypass quiet hours) vs normal
- **Analytics**: Track delivery, open rate, click rate

## Schema
```sql
notifications: id, user_id, channel, template_id, data(JSON), status, created_at, sent_at
user_preferences: user_id, channel, enabled, quiet_hours_start, quiet_hours_end
templates: id, name, channel, subject_template, body_template
```
