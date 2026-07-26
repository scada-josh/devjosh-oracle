# KlakMath Quick Reference

**Repo**: `keijiro/KlakMath`  
**Package**: `jp.keijiro.klak.math`  
**Version read**: `2.1.1`  
**Unity**: `2022.3+`  
**License**: Unlicense

## What It Is

KlakMath เป็น extension library สำหรับ `Unity.Mathematics` เพิ่ม helper เล็ก ๆ แต่ใช้บ่อยสำหรับ animation smoothing, deterministic random/hash, gradient noise, geometry sampling และ rotation math

## Install

ติดตั้งผ่าน Unity Package Manager ด้วย Keijiro scoped registry:

```json
{
  "scopedRegistries": [
    {
      "name": "Keijiro",
      "url": "https://registry.npmjs.com",
      "scopes": ["jp.keijiro"]
    }
  ],
  "dependencies": {
    "jp.keijiro.klak.math": "2.1.1"
  }
}
```

package dependency โดยตรง:

```text
com.unity.mathematics: 1.2.6
```

## Namespace

```csharp
using Unity.Mathematics;
using Klak.Math;
```

## Tweening

### ExpTween

Stateless exponential smoothing:

```csharp
float3 p = ExpTween.Step(p, target, speed);
quaternion r = ExpTween.Step(r, targetRotation, speed);
```

deterministic/manual dt:

```csharp
p = ExpTween.Step(p, target, speed, dt);
```

เหมาะกับ: smoothing ค่าแบบง่าย, camera follow, UI/motion ที่ไม่ต้องเก็บ velocity

### CdsTween

Critically damped spring ต้องเก็บ velocity:

```csharp
(float3 x, float3 v) state;
state = CdsTween.Step(state, target, speed, dt);
```

quaternion:

```csharp
(quaternion x, float4 v) rotState;
rotState = CdsTween.Step(rotState, targetRotation, speed, dt);
```

เหมาะกับ: motion ที่อยากได้ spring-like response แบบไม่ overshoot ง่าย

## Noise

1D:

```csharp
float n = Noise.Float(p, seed);
float f = Noise.Fractal(p, octave, seed);
```

vectorized:

```csharp
float2 n2 = Noise.Fractal2(p2, octave, seed);
float3 n3 = Noise.Fractal3(p3, octave, seed);
float4 n4 = Noise.Fractal4(p4, octave, seed);
```

rotation from noise:

```csharp
quaternion r = Noise.FractalRotation(p3, octave, angles, seed);
```

## XXHash

Deterministic hash/PRNG:

```csharp
var hash = new XXHash(seed);

uint id = hash.UInt(index);
float value = hash.Float(-1, 1, index);
float3 point = hash.InSphere(index);
quaternion rotation = hash.Rotation(index);
```

vector methods:

```csharp
float3 v = hash.Float3(-1, 1, index);
bool4 b = hash.Bool4(index);
int2 i = hash.Int2(0, 10, index);
```

geometry helpers:

| Method | Output |
|---|---|
| `OnCircle(data)` | point on unit circle |
| `InCircle(data)` | point inside unit circle |
| `OnSphere(data)` | point on unit sphere |
| `InSphere(data)` | point inside unit sphere |
| `Rotation(data)` | random deterministic quaternion |

## RandomExtensions

ใช้กับ `Unity.Mathematics.Random`:

```csharp
using Random = Unity.Mathematics.Random;

var rand = new Random(seed);
float2 disk = rand.NextFloat2OnDisk();
float3 sphere = rand.NextFloat3InSphere();
```

## Rotation

```csharp
quaternion q = Rotation.FromTo(fromVector, toVector);
```

ใช้ align local rotation จาก vector ตั้งต้นไปหา target direction

## Demo Scripts To Read

| File | Why |
|---|---|
| `Assets/Tween/TweenTest.cs` | เห็น `ExpTween` vs `CdsTween` ใช้จริง |
| `Assets/Noise/NoiseMotion.cs` | pattern noise-driven transform |
| `Assets/Noise/NoiseGraph1D.cs` | Burst + mesh line graph จาก fractal noise |
| `Assets/Random/RandomInSphereTest.cs` | extension method sampling |
| `Assets/XXHash/HashRotationTest.cs` | deterministic rotation visualization |

## Gotchas

- ถ้าใช้ overload ที่ไม่มี `dt` จะพึ่ง `UnityEngine.Time.deltaTime`
- `CdsTween` ต้องเก็บ velocity state ต่อ frame
- `XXHash` deterministic ตาม `(seed, data)` เหมาะกับ index-based generation ไม่ใช่ mutable random stream
- repo branch หลักชื่อ `master`
- ไม่มี test suite แยกชัดเจนใน repo; มี demo/test-style MonoBehaviour scripts ใน `Assets`

## Best Mental Model

KlakMath คือ "math utility belt" สำหรับ Unity.Mathematics: ไม่มี lifecycle, ไม่มี framework, ไม่มี config, แค่ pure-ish helper ที่เอาไปใช้ใน gameplay/demo/procedural motion ได้ทันที
