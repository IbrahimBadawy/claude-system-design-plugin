---
description: Design for 10x growth, build for current needs
globs: "*"
---

# Rule: Scale Incrementally

1. **Design for 10x, build for 1x**: Current implementation should be simple.
   Architecture should accommodate 10x growth without a rewrite.

2. **Stateless services**: App servers must be stateless.
   Session data goes to external store (Redis).
   Any server can handle any request.

3. **Cache everything expensive**: If a computation or query takes > 100ms,
   is called > 10 times/minute, and returns same result for same input -> cache it.

4. **Async by default**: If the user doesn't need immediate result,
   put it in a queue (email, notifications, file processing, reports, analytics).

5. **Don't over-engineer**: Don't shard a database serving 100 users.
   Don't use microservices for an MVP.
   Don't add Kafka for 10 messages/second.

6. **Know the scaling progression**:
   ```
   Single server -> Load balancer + multiple servers -> Read replicas + cache
   -> Sharding -> Microservices -> Multi-region -> Cell architecture
   ```

7. **Identify the breaking point**: Know which component breaks first at 10x.
