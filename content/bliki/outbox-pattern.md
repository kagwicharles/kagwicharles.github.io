---
title: Outbox Pattern
date: 2026-11-08
tags:
  - architecture
---

Instead of updating a database and publishing a message as two separate steps (which can fail halfway), write the message to an "outbox" table in the **same transaction** as the data change. A separate process reads the outbox and publishes the messages.

The result is that the data change and the message are either both recorded or both absent. The trade-off is that consumers may see a message more than once, so they should be idempotent.
