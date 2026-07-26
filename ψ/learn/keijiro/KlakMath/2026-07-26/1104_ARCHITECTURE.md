# KlakMath Architecture

**Repo**: `keijiro/KlakMath`  
**Source**: `/Users/devjosh/Documents/Workspace/Oracle-Skills/26 July/devjosh-oracle-main/ψ/learn/keijiro/KlakMath/origin`  
**Commit read**: `413064b` (`2.1.1`, `origin/master`)  
**Mode**: `/learn` default

## ภาพรวม

KlakMath เป็น Unity UPM package ขนาดเล็กที่ต่อยอด `Unity.Mathematics` ด้วย helper สำหรับ tweening, gradient noise, deterministic hashing/random, random geometry sampling และ rotation ระหว่าง vector สองตัว

ตัว repo เป็น Unity project สำหรับพัฒนาและ demo package ไปพร้อมกัน:

```text
Packages/jp.keijiro.klak.math/Runtime/  # runtime package จริง
Assets/Noise/                           # demo scenes/scripts สำหรับ noise
Assets/Random/                          # demo สำหรับ RandomExtensions
Assets/Tween/                           # demo สำหรับ ExpTween/CdsTween/Rotation
Assets/XXHash/                          # demo สำหรับ hash-based sampling
ProjectSettings/                        # Unity project settings
```

## Package Surface

runtime หลักอยู่ใน namespace `Klak.Math` และมีไฟล์สำคัญ 6 ไฟล์:

| File | Role |
|---|---|
| `CdsTween.cs` | critically damped spring interpolation พร้อม velocity state |
| `ExpTween.cs` | exponential interpolation แบบ stateless |
| `Noise.cs` | 1D gradient noise, vectorized noise, fractal noise, noise-driven rotation |
| `XXHash.cs` | deterministic hash/PRNG API สำหรับ scalar/vector/bool/geometry/rotation |
| `RandomExtensions.cs` | extension methods ให้ `Unity.Mathematics.Random` |
| `Rotation.cs` | helper `FromTo(float3, float3)` สร้าง quaternion จาก vector หนึ่งไปอีก vector |

`package.json` ระบุ package เป็น:

```json
{
  "name": "jp.keijiro.klak.math",
  "version": "2.1.1",
  "unity": "2022.3",
  "dependencies": {
    "com.unity.mathematics": "1.2.6"
  },
  "license": "Unlicense"
}
```

## Dependency Shape

ตัว UPM package จริงพึ่ง `com.unity.mathematics` เท่านั้น ส่วน Unity project ที่ใช้ demo เพิ่ม dependency อื่น:

| Dependency | ใช้ตรงไหน |
|---|---|
| `com.unity.mathematics` | runtime API ทั้งหมด |
| `com.unity.burst` | demo scripts ที่ compile update loops ด้วย Burst |
| `jp.keijiro.klak.util` | demo ใช้ `RawSpan` และ `ObjectUtil` |
| `jp.keijiro.test-assets` | demo/test asset support |

จุดสำคัญคือ runtime package ไม่ผูกกับ demo helper โดยตรง ทำให้ package ใช้งานง่ายใน Unity project อื่น

## Core Abstractions

### Tween

`ExpTween` เป็น stateless helper:

```csharp
value = ExpTween.Step(value, target, speed);
```

รองรับ `float`, `float2`, `float3`, `float4`, `quaternion` และมี overload รับ `dt` เอง หรือใช้ `UnityEngine.Time.deltaTime`

`CdsTween` เป็น stateful helper:

```csharp
(position, velocity) = CdsTween.Step((position, velocity), target, speed);
```

มันต้องเก็บ velocity state ต่อเนื่องเพื่อให้ motion เป็น critically damped spring ส่วน quaternion ใช้ velocity เป็น `float4` และ normalize quaternion หลัง step

### Noise

`Noise` เป็น static partial class สำหรับ gradient noise 1D และเวอร์ชัน vectorized:

```csharp
float n = Noise.Fractal(x, octave, seed);
float3 p = Noise.Fractal3(position, octave, seed);
quaternion r = Noise.FractalRotation(position, octave, angles, seed);
```

implementation ใช้ `XXHash` สร้าง gradient deterministic จาก seed และ lattice index แล้ว blend ด้วย kernel polynomial

### XXHash

`XXHash` เป็น readonly struct ที่เก็บ `Seed` และ expose method เยอะมาก:

- `UInt`, `UInt2`, `UInt3`, `UInt4`
- `Int`, `Int2`, `Int3`, `Int4`
- `Float`, `Float2`, `Float3`, `Float4`
- `Bool`, `Bool2`, `Bool3`, `Bool4`
- `OnCircle`, `InCircle`, `OnSphere`, `InSphere`, `Rotation`

รูปแบบ usage คือ data/index + seed ให้ output deterministic:

```csharp
var hash = new XXHash(seed);
float3 p = hash.InSphere((uint)i);
quaternion r = hash.Rotation((uint)i);
```

### RandomExtensions

เพิ่ม extension ให้ `Unity.Mathematics.Random`:

```csharp
float2 p = rand.NextFloat2OnDisk();
float3 q = rand.NextFloat3InSphere();
```

ทั้งสองใช้ direction sampling จาก Unity.Mathematics แล้วปรับ radius ด้วย `sqrt` หรือ cube root เพื่อให้กระจายตัวในพื้นที่/ปริมาตรถูกต้อง

### Rotation

`Rotation.FromTo(v1, v2)` สร้าง quaternion จาก cross/dot product และ normalize แบบ safe เหมาะกับการ align object ไปหา direction เป้าหมายแบบสั้น ๆ

## Demo Project

โฟลเดอร์ `Assets` เป็นชุด demo:

| Demo | สิ่งที่โชว์ |
|---|---|
| `NoiseGraph1D.cs` | วาด line strip จาก `Noise.Fractal` |
| `NoiseGraph2D.cs` | ใช้ `Noise.Fractal2` ขับเส้นทาง 2D |
| `NoiseMotion.cs` | ใช้ fractal noise ขับ position และ rotation |
| `TweenTest.cs` | เทียบ `ExpTween` กับ `CdsTween` โดย target มาจาก `XXHash` |
| `FromToRotationTest.cs` | ใช้ `Rotation.FromTo` align object ไปหา target |
| `RandomInSphereTest.cs` | sample จุดด้วย `Random.NextFloat3InSphere` |
| `RandomOnDiskTest.cs` | sample จุดด้วย `Random.NextFloat2OnDisk` |
| `Hash*Test.cs` | visualize `XXHash` geometry/rotation utilities |

## Release Workflow Notes

`AGENTS.md` ระบุ workflow สำหรับ release:

- root docs และ package docs ต้อง sync กัน
- changelog ใช้ Keep a Changelog + SemVer
- release ต้อง bump `package.json`, update `_upm.changelog`, เปลี่ยน heading changelog, commit, tag
- npm publish ต้องใช้ tarball ที่ export จาก Unity Editor

## Architecture Read

KlakMath ออกแบบแบบ "small pure math helpers" มากกว่า framework ไม่มี lifecycle, service, editor tooling หรือ configuration system Runtime API เป็น static/readonly style ทั้งหมด จึงใช้งานง่ายใน Burst-compatible code และ deterministic simulations จุดที่ต้องจำคือ API บางตัวใช้ `Time.deltaTime` overload ถ้าอยาก deterministic จริงใน simulation/test ควรเรียก overload ที่ส่ง `dt` เอง
