---
title: Strangler Fig
date: 2026-11-05
tags:
  - architecture
---

A way to replace a legacy system gradually: put a routing layer in front of it, build new functionality alongside, and move traffic over piece by piece until the old system has nothing left to do.

It's slower to start than a rewrite, but you ship value continuously and can stop or reverse at any point.
