---
title: Idempotency in Payment APIs
description: Why retries are dangerous in money movement, and how an idempotency key makes them safe.
date: 2026-11-10
tags:
  - architecture
  - fintech
sidebarNote: 'Related notes: <a href="/bliki/idempotency-key/">Idempotency Key</a> and <a href="/bliki/outbox-pattern/">Outbox Pattern</a>.'
---

Networks fail. A client sends a payment request, the connection drops, and it has no idea whether the money moved. The natural reaction is to retry, and that is exactly where double charges come from.

## The problem

A retry is only safe if repeating the operation produces the same outcome as doing it once. Most "create" endpoints don't behave that way: every call creates a new record.

## The idea

The client generates a unique key per _intended_ operation and sends it with the request. The server stores the key together with the result of the first attempt.

```js
async function createPayment(req, db) {
	const key = req.headers["idempotency-key"];
	const existing = await db.findByKey(key);
	if (existing) return existing.response; // replay, don't redo

	const response = await processPayment(req.body);
	await db.saveKey(key, response);
	return response;
}
```

## Things that bite in practice

- **Race conditions.** Two identical requests can arrive at the same moment. Enforce uniqueness on the key in the database, not just in application code.
- **Key scope.** Scope keys per client or account, otherwise one customer can collide with another.
- **Payload mismatch.** If the same key arrives with a different body, reject it instead of silently returning the old result.
- **Expiry.** Keep keys long enough to cover realistic retry windows, such as 24 hours.

## Takeaway

Idempotency turns "did it work?" from a dangerous question into a safe one: ask again.
