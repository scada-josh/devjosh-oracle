---
title: "อ่าน Unity ด้วยสายตาคนทำเว็บ"
subtitle: "KlakMath, Three.js Knowledge Globe, และวิธีแปลโค้ดให้กลายเป็นภาพ"
author: "Oracle — AI speaking as itself"
date: 2026-07-26
language: Thai
register: developer-friendly Thai with English technical terms
target_pages: 10
source_repo: "keijiro/KlakMath"
working_repo: "scada-josh/devjosh-oracle"
---

# Outline

## หน้า 1: ปก

เป้าหมาย: ตั้งโจทย์ว่าเราไม่ได้เรียน Unity จาก Unity ก่อน แต่เรียนผ่าน web mental model

proof:
- `ψ/learn/keijiro/KlakMath/KlakMath.md`
- `index.html`

## หน้า 2: ทำไมต้องแปล

เป้าหมาย: อธิบายช่องว่างระหว่างคนทำเว็บกับ Unity repo

proof:
- `PRODUCT.md`
- `DESIGN.md`

## หน้า 3: แผนที่ของ KlakMath

เป้าหมาย: สรุป runtime modules หลัก 6 ตัว

proof:
- `ψ/learn/keijiro/KlakMath/2026-07-26/1104_ARCHITECTURE.md`

## หน้า 4: Tween คือ motion grammar

เป้าหมาย: เทียบ `ExpTween` กับ `CdsTween` ด้วยภาษาคนทำเว็บ

proof:
- `ψ/learn/keijiro/KlakMath/2026-07-26/1104_CODE-SNIPPETS.md`

## หน้า 5: Noise คือพื้นผิวของเวลา

เป้าหมาย: อธิบาย gradient/fractal noise และ motion ที่ไม่แข็ง

proof:
- `Noise.cs`
- `Assets/Noise/NoiseMotion.cs`

## หน้า 6: XXHash คือ deterministic memory

เป้าหมาย: อธิบาย seed + data -> repeatable random

proof:
- `XXHash.cs`

## หน้า 7: Rotation และ sampling

เป้าหมาย: อธิบาย `Rotation.FromTo`, disk/sphere sampling ในแง่ geometry

proof:
- `Rotation.cs`
- `RandomExtensions.cs`

## หน้า 8: จาก docs สู่ globe

เป้าหมาย: อธิบายการแปลง concepts เป็น nodes, links, groups

proof:
- `index.html`
- `DESIGN.md`

## หน้า 9: สิ่งที่ AI ทำถูกและทำพลาด

เป้าหมาย: ซื่อสัตย์เรื่อง verification, WebGL fallback, และการไม่ claim เกิน source

proof:
- `ψ/memory/learnings/2026-07-26_learned_klakmath.md`
- `ψ/memory/learnings/session-metrics.md`

## หน้า 10: ต่อจากนี้

เป้าหมาย: ชวนเรียนร่วมกันต่อด้วย experiments ที่คนทำเว็บเข้าใจได้

proof:
- `index.html`
- `ψ/learn/keijiro/KlakMath/2026-07-26/1104_QUICK-REFERENCE.md`
