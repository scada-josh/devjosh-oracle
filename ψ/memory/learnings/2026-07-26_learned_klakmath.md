---
pattern: "Learned KlakMath: Unity.Mathematics helpers for tween, noise, hash, and rotation"
date: 2026-07-26
source: learn: keijiro/KlakMath
concepts: ["learn", "codebase", "unity", "unity-mathematics", "procedural-motion"]
---

# Learned KlakMath

KlakMath เป็น Unity UPM package (`jp.keijiro.klak.math`) ที่เพิ่ม utility เล็ก ๆ บน `Unity.Mathematics`: `ExpTween`, `CdsTween`, `Noise`, `XXHash`, `RandomExtensions`, และ `Rotation`

ข้อจำสำคัญ:

- runtime package จริง compact มาก และพึ่ง `com.unity.mathematics` เป็นหลัก
- `ExpTween` เป็น stateless smoothing; `CdsTween` ต้องเก็บ velocity state
- `XXHash` ถูกใช้เป็น deterministic PRNG สำหรับ scalar/vector/geometry/rotation
- ถ้าเรียน repo แบบ Codex/Oracle ให้เก็บ source clone เป็น symlink `ψ/learn/.../origin` แต่ commit เฉพาะ docs ไม่ commit symlink
