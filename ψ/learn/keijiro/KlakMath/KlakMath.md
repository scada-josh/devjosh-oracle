# KlakMath Learning Index

## Source

- **Origin**: `./origin/`
- **GitHub**: https://github.com/keijiro/KlakMath
- **Commit read**: `413064b`

## Explorations

### 2026-07-26 1104 (default)

- [[2026-07-26/1104_ARCHITECTURE|Architecture]]
- [[2026-07-26/1104_CODE-SNIPPETS|Code Snippets]]
- [[2026-07-26/1104_QUICK-REFERENCE|Quick Reference]]

**Key insights**:

- KlakMath เป็น UPM package เล็ก ๆ ที่ต่อยอด `Unity.Mathematics` ด้วย tween, noise, hash/random, geometry sampling และ rotation helpers
- runtime package จริงพึ่ง `com.unity.mathematics` เป็นหลัก ส่วน Unity project รอบนอกมี demo scenes และ Burst/Klak.Util support
- API ออกแบบเป็น static/readonly utility ไม่มี framework state ยกเว้น `CdsTween` ที่ผู้ใช้ต้องถือ velocity state เอง
