# KlakMath Code Snippets

**Repo**: `keijiro/KlakMath`  
**Commit read**: `413064b`  
**Source**: `/Users/devjosh/Documents/Workspace/Oracle-Skills/26 July/devjosh-oracle-main/ψ/learn/keijiro/KlakMath/origin`

## Exponential Tween

`ExpTween` ใช้ exponential decay เป็น interpolation factor:

```csharp
public static float3 Step(float3 x, float3 target, float speed, float dt)
  => math.lerp(target, x, math.exp(-speed * dt));
```

ข้อสังเกต: ลำดับ argument ของ `math.lerp(target, x, t)` ทำให้เมื่อ `t = exp(-speed * dt)` ลดลง ค่า `x` จะเข้าใกล้ `target` มากขึ้น เป็นสูตร smoothing ที่ frame-rate independent เมื่อส่ง `dt` ถูกต้อง

quaternion ใช้ `math.nlerp`:

```csharp
public static quaternion Step(quaternion x, quaternion target, float speed, float dt)
  => math.nlerp(target, x, math.exp(-speed * dt));
```

## Critically Damped Spring Tween

`CdsTween` เก็บทั้ง position และ velocity:

```csharp
public static (float3 x, float3 v)
  Step((float3 x, float3 v) state, float3 target, float speed, float dt)
{
    var n1 = state.v - (state.x - target) * (speed * speed * dt);
    var n2 = 1 + speed * dt;
    var nv = n1 / (n2 * n2);
    return (state.x + nv * dt, nv);
}
```

usage จาก demo:

```csharp
(p, _velocity.p) = CdsTween.Step((p, _velocity.p), _target.p, _speed);
(r, _velocity.r) = CdsTween.Step((r, _velocity.r), _target.r, _speed);
```

quaternion path มี detail สำคัญ: ถ้า dot product ติดลบ จะกลับ sign ของ target quaternion เพื่อเลือก pose ที่ใกล้กว่า:

```csharp
if (math.dot(state.x, target) < 0) target.value *= -1;
var n = Step((state.x.value, state.v), target.value, speed, dt);
return (math.normalize(math.quaternion(n.x)), n.v);
```

## Gradient Noise

`Noise.Float` สร้าง hash จาก seed แล้วใช้ cell index สองฝั่งของตำแหน่ง `p`:

```csharp
var hash = new XXHash(seed);
var i = (uint)((int)p + 0x10000000);
var x = math.frac(p);
```

kernel:

```csharp
var k = math.float2(x, 1 - x);
k = 1 - k * k;
k = k * k * k;
```

gradient จาก hash:

```csharp
var g = math.float2(hash.Float(-1, 1, i),
                    hash.Float(-1, 1, i + 1));
```

final value:

```csharp
var n = math.dot(k * g, math.float2(x, x - 1));
return n * 2 * 32 / 27;
```

fractal helper stack octave แบบเรียบง่าย:

```csharp
for (var i = 0; i < octave; i++)
{
    f += w * Float(p, seed);
    p *= 2.0f;
    w *= 0.5f;
}
```

## Noise-Driven Motion

จาก `Assets/Noise/NoiseMotion.cs`:

```csharp
var hash = new XXHash(_seed + 0x100000);
var x = (Time.time + 100) * _frequency * hash.Float3(0.95f, 1.05f, 0);
transform.localPosition = Noise.Fractal3(x, _octaves, _seed) * _radius;
transform.localRotation = Noise.FractalRotation(x, _octaves, _angle, _seed + 1);
```

pattern นี้ใช้ hash เพื่อ randomize frequency per-axis อย่าง deterministic แล้วใช้ noise ขับ transform

## XXHash Deterministic PRNG

constructor:

```csharp
public uint Seed { get; }
public XXHash(uint seed) => Seed = seed;
```

scalar/vector hash:

```csharp
public uint  UInt (uint  data) => CalculateHash(data, Seed);
public uint3 UInt3(uint3 data) => CalculateHash(data, (uint3)Seed);
```

single-seed vector expansion ใช้ offset constants:

```csharp
public uint3 UInt3(uint data)
  => UInt3(math.uint3(data, data + 0x10000000, data + 0x20000000));
```

range helpers:

```csharp
public float Float(float min, float max, uint data)
  => Float(data) * (max - min) + min;
```

geometry helpers:

```csharp
public float3 OnSphere(uint data)
{
    var phi = Float(math.PI * 2, data);
    var z = Float(-1, 1, data + 0x10000000);
    var w = math.sqrt(1 - z * z);
    return math.float3(math.cos(phi) * w, math.sin(phi) * w, z);
}

public float3 InSphere(uint data)
  => OnSphere(data) * math.pow(Float(data + 0x20000000), 1.0f / 3);
```

## RandomExtensions

```csharp
public static float2 NextFloat2OnDisk(ref this Random self)
  => self.NextFloat2Direction() * math.sqrt(self.NextFloat());

public static float3 NextFloat3InSphere(ref this Random self)
  => self.NextFloat3Direction() * math.pow(self.NextFloat(), 1.0f / 3);
```

นี่เป็น sampling ที่ถูกต้องทาง distribution สำหรับ disk/sphere interior เพราะ radius ต้องแปลงตามมิติ ไม่ใช่ใช้ random radius ตรง ๆ

## From-To Rotation

```csharp
public static quaternion FromTo(float3 v1, float3 v2)
{
    var a = math.cross(v1, v2);
    var v1v2 = math.dot(v1, v1) * math.dot(v2, v2);
    var w = math.sqrt(v1v2) + math.dot(v1, v2);
    return math.normalizesafe(math.quaternion(math.float4(a, w)));
}
```

demo:

```csharp
transform.localRotation =
  Rotation.FromTo(_fromVector, _target.localPosition - transform.localPosition);
```

## Practical Usage Pattern

สำหรับ object motion ที่ target เปลี่ยนเป็นช่วง ๆ:

```csharp
var hash = new XXHash(seed);
targetPosition = hash.InSphere(i++) * radius;
targetRotation = hash.Rotation(i++);

position = ExpTween.Step(position, targetPosition, speed, dt);
rotation = ExpTween.Step(rotation, targetRotation, speed, dt);
```

ถ้าต้องการ spring feel:

```csharp
(position, velocity) = CdsTween.Step((position, velocity), targetPosition, speed, dt);
```
